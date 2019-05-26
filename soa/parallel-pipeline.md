```go
package main

import (
	"errors"
	"fmt"
	"sync"
	"time"
)

type ParallelPipeline struct {
	done  chan struct{}
	wg    sync.WaitGroup
	errCh chan error
}

func NewParallelPipeline() *ParallelPipeline {
	return &ParallelPipeline{
		done:  make(chan struct{}),
		errCh: make(chan error),
	}
}

type Task func() error

func (p *ParallelPipeline) Execute(tasks ...Task) error {
	p.wg.Add(len(tasks))
	for _, task := range tasks {
		go func(task Task) {
			defer p.wg.Done()
			select {
			case <-p.done:
				return
			case p.errCh <- task():
			}
		}(task)
	}

	for err := range p.errCh {
		if err != nil {
			close(p.done)
			close(p.errCh)
			return err
		}
	}
	return nil
}
func main() {
	validateCard := func() error {
		time.Sleep(2 * time.Second)
		return errors.New("invalid card")
	}

	checkFraud := func() error {
		time.Sleep(1 * time.Second)
		return errors.New("fraud")
	}

	pp := NewParallelPipeline()
	err := pp.Execute(checkFraud, validateCard)
	fmt.Println(err)
}
```
