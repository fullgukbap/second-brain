
```go
// direction
type Direction int

const (
	Up Direction = iota
	Down
	Left
	Right
)
```

```go
// vector
type Vector struct {
	X int 
	Y int
}
```


```go
// player
type Player struct {
	Position Vector
	Speed int
}
```


```go 
// system
type System struct {
	Players map[string]Player
}
```

