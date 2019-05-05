## Modelling Jira Workflow
```go
package main

import (
	"fmt"
)

type Status string

const (
	Start      Status = ""
	Open       Status = "open"
	Resolved   Status = "resolved"
	Closed     Status = "closed"
	InProgress Status = "in progress"
	Reopened   Status = "reopened"
)

func (s Status) String() string {
	return string(s)
}

type Transition func() Status

var (
	createIssue = func() Status {
		return Open
	}
	startProgress = func() Status {
		return InProgress
	}
	resolveIssue = func() Status {
		return Resolved
	}
	closeIssue = func() Status {
		return Closed
	}
	reopenIssue = func() Status {
		return Open
	}
	stopProgress = func() Status {
		return Open
	}
)

// Each Status should lead to another Transition, which will generate another Status
// It is possible to map the status change by just creating a map of Status to Statuses
type Options map[Status][]Transition

type Transitions map[Status][]Status

var transitions = Transitions{
	Start:      []Status{Open},                         // Create Issue
	Open:       []Status{InProgress, Resolved, Closed}, // Start progress, resolve issue, close issue
	Resolved:   []Status{Closed, Reopened},             // Close issue, reopen issue
	Reopened:   []Status{Closed, InProgress, Resolved}, // Close Issue, start progress, resolve issue
	Closed:     []Status{},
	InProgress: []Status{Open, Resolved, Closed}, // Stop progress, resolve issue, close issue
}

func main() {
	var opts = Options{
		Start:      []Transition{createIssue},
		Open:       []Transition{startProgress, resolveIssue, closeIssue},
		Resolved:   []Transition{closeIssue, reopenIssue},
		Reopened:   []Transition{closeIssue, startProgress, resolveIssue},
		Closed:     []Transition{},
		InProgress: []Transition{stopProgress, resolveIssue, closeIssue},
	}
	fmt.Println(opts)
	fmt.Println(opts[opts[Start][0]()][0]())
	fmt.Println(transitions)
}
```

References:

- https://confluence.atlassian.com/adminjiraserver072/working-with-workflows-828787890.html
