# Idempotent manager

Implementing idempotency in microservice is not hard, though there are several ways to do it. The end goal is to implement idempotency for external requests such as API calls within a transaction.

## Level 0 - Transaction wrapper

The simplest level is to wrap things in a transaction, call the external api and persist the entry once the call is successful. After a certain period, those entries can be removed as they do not need to be persisted for long. 
```
begin transaction
  insert entry into database with a unique key
  if conflict, rollback
  begin pg advisory lock
  make http call
  if error, rollback
  if server crash here, the entry won't be in the database (the call may or may not be successful)
  save entry into database
commit
```
However, if the server crash before the entry is persisted in the database, there won't be a record of the call, and if the service is not idempotent, we may incure side-effects twice. So this implementation is only useful if the service already handles idempotency. 

Cons
- lack of observability - has the call been made? If the call fails, do we need to retry it? Is the call mandatory?
- loss of the data when the server crash
- possibility of mutation if the external call is not idempotent

## Level 1 - Lifecycle manager

To ensure the situation above does not happen, we need to have multiple atomic phases, with the first one inserting a record with the status pending into the database. When making the call, we will then update the status to indicate it started, failed or completed. If the server crash in between, we will know that the service is initialized and could potentially be triggered. The only way to check this is to call the external api provider if they provide an endpoint to check the status of the call.


We can use the `Scheduler agent supervisor pattern`. Since the scheduler will run the job, they should not be invoked directly. Instead, a call should be made to insert the row for scheduler to pick up the task.
```
# Scheduler pseudo code
atomic phase 1
create an entry with idempotency key and status pending. 
also set the last run at timestamp/request params
if the entry exists, and the status is not pending, fail this.

atomic phase 2
begin transaction
begin adivisory lock for the given request
make agent call
if error, save entry and commit
if success, save entry and commit
if server crash here, the status will stay pending, a supervisor will resolve this
```

```
# Supervisor pseudo code
find all pending and last run at greater than specified threshold
call external api call to check the status
if the status is successful, save them
if the status is failed, save them and log error
if the status is not yet started, reset the last run at for the scheduler to pick again
```


Pros
- lifecycle visible, from when the call is requested to when it fails/succeeds, they are recorded in the database
- idempotency key can be generated and stored in db
- if the external party provides api to check status, we can use them to check if the server crash before recording to database

Cons
- additional complexity
- YAGNI
- does not handle multiple workflow steps

## Implementation
See also https://github.com/Azure-Samples/saga-orchestration-serverless/blob/main/docs/saga-orchestrator.md
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
	UnmarshalJSON(interface{}) error
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

type requestParams struct {
	id, name string
	payload  []byte
}

func (r requestParams) ID() string {
	return r.id
}
func (r requestParams) Name() string {
	return r.name
}
func (r requestParams) MarshalJSON() ([]byte, error) {
	return r.payload, nil
}
func (r *requestParams) UnmarshalJSON(v interface{}) error {
	return nil
}

func (s *Scheduler) Retry(ctx context.Context, id string, fn RequestFn) error {
	state, err := s.repo.Find(ctx, id)
	if err != nil {
		return err
	}
	return s.Do(ctx, state, fn, &requestParams{
		id:      state.ID,
		name:    state.Name,
		payload: state.RequestParams,
	})
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

	// Do work (how to wrap this in pg-advisory?)
	// Store the State in context?
	res, err := fn.Do(ctx, req)

	// If the server crash here, we won't know unless we call back the source.
	// This should be handle in the supervisor.
	if err != nil {
		return s.updateFailure(ctx, state, res, err.Error())
	}

	// If the server crash here, the status will remain pending. It is up to the supervisor to reconcile.
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
