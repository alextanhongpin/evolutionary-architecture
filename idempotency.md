Idempotent manager
```go
package main

import (
	"context"
	"encoding/json"
	"errors"
	"fmt"
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

type jsonMarshaler interface {
	MarshalJSON() ([]byte, error)
	UnmarshalJSON([]byte, interface{}) error
}

type RequestParams interface {
	jsonMarshaler
	ID() string
	Name() string
}

type ResponseParams interface {
	jsonMarshaler
}

type RequestFn interface {
	Do(ctx context.Context, req RequestParams) (ResponseParams, error)
}

type State struct {
	ID             string
	Name           string
	Status         Status // When end
	IdempotencyKey string
	RequestParams  json.RawMessage // When start
	ResponseParams json.RawMessage // When end
	FailureReason  string          // When end
	StartedAt      *time.Time      // When start
	CompleteBy     *time.Time
	CreatedAt      time.Time
	UpdatedAt      time.Time
}

type SchedulerRepo interface {
	Upsert(context.Context, State) (State, error)
	Update(context.Context, State) error
	Find(context.Context, string) (State, error)
}

type Scheduler struct {
	repo SchedulerRepo
}

func (s *Scheduler) Retry(ctx context.Context, id string, fn RequestFn) error {
	state, err := s.repo.Find(ctx, id)
	if err != nil {
		return err
	}
	return s.Do(ctx, state, fn, )
}

func (s *Scheduler) Do(ctx context.Context, state State, fn RequestFn, req RequestParams) error {
	state, err := s.updateInit(ctx, req)
	if err != nil {
		return err
	}
	if state.Status != StatusPending {
		return ErrProcessing
	}
	if err := s.updateStart(ctx, state, req); err != nil {
		return err
	}
	// If the server crash here, the request params would have been stored anyway.
	// That way, we can just retry the operation.

	// Do work
	res, err := fn.Do(ctx, req)
	// If the server crash here, we won't know unless we call back the source.
	// This should be handle in the supervisor.
	if err != nil {
		return s.updateFailure(ctx, state, res, err.Error())
	}
	return s.updateSuccess(ctx, state, res)
}
func (s *Scheduler) updateInit(ctx context.Context, req RequestParams) (State, error) {
	state, err := s.repo.Upsert(ctx, State{
		ID:     req.ID(),
		Name:   req.Name(),
		Status: StatusPending,
	})
	return state, err
}

func (s *Scheduler) updateStart(ctx context.Context, state State, req RequestParams) error {
	started := state
	now := time.Now()
	started.StartedAt = &now
	started.RequestParams, _ = req.MarshalJSON()
	return s.repo.Update(ctx, started)
}

func (s *Scheduler) updateSuccess(ctx context.Context, state State, res ResponseParams) error {
	success := state
	success.Status = StatusCompleted
	success.ResponseParams, _ = res.MarshalJSON()
	success.FailureReason = ""
	return s.repo.Update(ctx, success)
}

func (s *Scheduler) updateFailure(ctx context.Context, state State, res ResponseParams, failureReason string) error {
	failed := state
	failed.Status = StatusFailed
	failed.ResponseParams, _ = res.MarshalJSON()
	failed.FailureReason = failureReason
	return s.repo.Update(ctx, failed)
}

type Supervisor struct{}

func (s *Supervisor) Validate() {
	// Find expired
	// Check against external API response, if they are completed, mark them as completed with the value response.
	// Else, mark them back as pending.
}
```
