```go
package main

import (
	"context"
	"encoding/json"
	"errors"
	"fmt"
	"log"
	"time"
)

type Status string

var (
	StatusPending   Status = "pending"
	StatusCompleted Status = "completed"
	StatusFailed    Status = "failed"
)
var (
	ErrProcessing = errors.New("already processing")
	ErrCompleted  = errors.New("completed")
)

func main() {
	fmt.Println("Hello, playground")
}

type State struct {
	ID             string
	Name           string
	Status         Status // When end
	Version        int64
	RequestParams  json.RawMessage // When start
	ResponseParams json.RawMessage // When end
	FailureReason  string          // When end

	StartedAt  *time.Time // When start
	CompleteBy *time.Time
	CreatedAt  time.Time
	UpdatedAt  time.Time

	RetryAttempt int64
}

type SchedulerRepo interface {
	Upsert(context.Context, State) (State, error)
	Update(context.Context, State) error
	Find(context.Context, string) (State, error)
}

type Scheduler struct {
	repo SchedulerRepo
}

type AgentFunc func(ctx context.Context, in json.RawMessage) (json.RawMessage, error)

func (s *Scheduler) Retry(ctx context.Context, id string, fn AgentFunc) error {
	state, err := s.repo.Find(ctx, id)
	if err != nil {
		return err
	}
	return s.Do(ctx, state, state.RequestParams, fn)
}

func (s *Scheduler) Wrap(ctx context.Context, name, id string, req json.RawMessage, fn AgentFunc) error {
	state, err := s.repo.Upsert(ctx, State{
		ID:      id,
		Name:    name,
		Status:  StatusPending,
		Version: 1,
	})
	if err != nil {
		log.Fatal(err)
	}
	return s.Do(ctx, state, req, fn)
}

func (s *Scheduler) Do(ctx context.Context, state State, req json.RawMessage, fn AgentFunc) error {
	switch state.Status {
	case StatusPending:
		if state.StartedAt != nil {
			return ErrProcessing
		}
	case StatusCompleted:
		return ErrCompleted
	case StatusFailed:
		state.RetryAttempt++
	default:
		log.Fatalf("invalid status: %s", state.Status)
	}

	now := time.Now()
	state.RequestParams = req
	state.StartedAt = &now
	if err := s.repo.Update(ctx, state); err != nil {
		return err
	}

	// Do work
	res, err := fn(ctx, req)
	if err != nil {
		state.Status = StatusFailed
		state.ResponseParams = res
		state.FailureReason = err.Error()
		return s.repo.Update(ctx, state)
	}
	state.Status = StatusCompleted
	state.ResponseParams = res
	state.FailureReason = ""
	return s.repo.Update(ctx, state)
}

type FlightScheduler struct {
	Scheduler
}

func (f *FlightScheduler) BookFlight(ctx context.Context, id string, arrivalAt time.Time) {
	f.Scheduler.Wrap(ctx, id, "flight-scheduler", json.RawMessage(`{"arrivedAt": %q}`), func(ctx context.Context, in json.RawMessage) (json.RawMessage, error) {
		var req FlightRequest
		if err := json.Unmarshal(in, &req); err != nil {
			return nil, err
		}
		// Book flight through http request.
		return nil, nil
	})
}
```
