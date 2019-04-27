Sample design for player
```go
package main

import (
	"fmt"
)

type Skill struct {
	ID          string
	Level       int
	Name        string
	Description string
	Effect      string
	ParentID    string // Skill can be leveled up from parent id
}

type Player struct {
	ID     string
	Name   string
	Type   string // Hero, Enemy, or Occupation etc
	Skills []Skill
	Stat   Stat
	Exp    int
	Level  int
}

func (p *Player) LevelUp(levels map[int]Level, stats map[int]Stat) {
	var nextLvl int
	// NOTE: Ranging through map will result in undeterministic results. Use slice!!!
	for lvl, lvlInfo := range levels {
		if p.Exp > lvlInfo.Exp {
			nextLvl = lvl
		}
		if p.Exp < lvlInfo.Exp {
			break
		}
	}
	p.Level = nextLvl
	p.Stat = stats[nextLvl]
	fmt.Println("level up to lvl", p.Level)
	fmt.Println("stats is now", p.Stat)
}

type Stat struct {
	Level int
	HP    int
	MP    int
	Atk   int
	Def   int
}

type Level struct {
	ID  int
	Exp int
}

func main() {
	levels := map[int]Level{
		1:   {1, 0},
		2:   {2, 100},
		3:   {3, 300},
		999: {4, 9999},
	}
	stats := map[int]Stat{
		1: Stat{1, 100, 100, 10, 20},
		2: Stat{2, 200, 200, 20, 40},
	}
	p := Player{
		ID:    "1",
		Name:  "john",
		Type:  "hero",
		Stat:  stats[1],
		Exp:   0,
		Level: 1,
	}
	p.Exp += 300
	p.LevelUp(levels, stats)
}
```
