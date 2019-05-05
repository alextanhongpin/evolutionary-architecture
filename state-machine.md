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

## MySQL v1 Implementation

```sql
create database test;
use test;

CREATE TABLE IF NOT EXISTS status_type (
	id VARCHAR(255),
	description TEXT,
	PRIMARY KEY (id)
);

INSERT INTO status_type 
(id, description) VALUES
('process', 'the workflow process'),
('transition', 'the action that cause the status to change'),
('state', 'the current state');


CREATE TABLE IF NOT EXISTS status (
	id VARCHAR(255),
	status_type_id VARCHAR(255) NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY (status_type_id) REFERENCES status_type(id)
);

INSERT INTO STATUS
(id, status_type_id) VALUES 
('start', 'state'),
('open', 'state'),
('in_progress', 'state'),
('resolved', 'state'),
('closed', 'state'),
('reopened', 'state'),
('jira workflow', 'process'),
('start_progress', 'transition'),
('resolve_issue', 'transition'),
('close_issue', 'transition'),
('reopen_issue', 'transition'),
('create_issue', 'transition'),
('stop_progress', 'transition')
('end', 'state'),
('none', 'transition');


CREATE TABLE IF NOT EXISTS status_transition (
	id INT UNSIGNED AUTO_INCREMENT,
	from_status VARCHAR(255) NOT NULL,
	to_status VARCHAR(255) NOT NULL,
	transition VARCHAR(255) NOT NULL,
	description TEXT,
	PRIMARY KEY (id),
	FOREIGN KEY (from_status) REFERENCES status(id),
	FOREIGN KEY (to_status) REFERENCES status(id),
	FOREIGN KEY (transition) REFERENCES status(id)
);

INSERT INTO status_transition 
(from_status, to_status, transition) VALUES 
('start', 'open', 'create_issue'),
('open', 'in_progress', 'start_progress'),
('open', 'resolved', 'resolve_issue'),
('open', 'closed', 'close_issue'),
('resolved', 'closed', 'close_issue'),
('resolved', 'reopened', 'reopen_issue'),
('reopened', 'closed', 'close_issue'),
('reopened', 'in_progress', 'start_progress'),
('reopened', 'resolved', 'resolve_issue'),
('closed', 'end', 'none'),
('in_progress', 'open', 'stop_progress'),
('in_progress', 'resolved', 'resolve_issue'),
('in_progress', 'closed', 'close_issue');

SELECT 	to_status 
FROM 	status_transition
WHERE 	transition = 'resolve_issue'
AND 	from_status = 'open';

SELECT 	* 
FROM 	status_transition 
WHERE 	from_status = 'open';
```

References:

- https://confluence.atlassian.com/adminjiraserver072/working-with-workflows-828787890.html
