

```go
// movment 
type Direction int

type Movement struct {
	Direction string
}
```

```go
// vector
type Vector struct {
	X int 
	Y int
}

func NewVector(x, y int) Vector {
	return Vector{
		X: x,
		Y: y,
	}
}
```


```go
// player
type Player struct {
	Position Vector
	Speed int
}

func NewPlayer(position Vector, speed int) Player {
	return Player{
		Position: position,
		Speed: speed,
	}
}

```


```go 
// system
type System struct {
	Players map[string]Player
}

func NewSystem() System {
	return System{
		Players: make(map[string]Player),
	}
}

```

