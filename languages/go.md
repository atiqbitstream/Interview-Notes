# Go

## Overview
Go is a statically typed, compiled language designed for simplicity and efficiency.

## Key Features
- **Fast Compilation**: Quick build times
- **Concurrent**: Goroutines and channels
- **Simple**: Clean syntax, minimal features
- **Garbage Collected**: Automatic memory management
- **Static Typing**: Type safety

## Basic Syntax
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

## Variables and Types
```go
// Variable declaration
var name string = "John"
var age int = 30

// Short declaration
message := "Hello"
count := 42

// Multiple variables
x, y := 10, 20

// Constants
const Pi = 3.14
```

## Data Types
```go
// Basic types
var integer int = 42
var float float64 = 3.14
var boolean bool = true
var text string = "Hello"

// Arrays (fixed size)
var arr [5]int = [5]int{1, 2, 3, 4, 5}

// Slices (dynamic)
slice := []int{1, 2, 3}
slice = append(slice, 4)

// Maps
ages := make(map[string]int)
ages["Alice"] = 25
ages["Bob"] = 30

// Structs
type Person struct {
    Name string
    Age  int
}
person := Person{Name: "John", Age: 30}
```

## Functions
```go
func add(a int, b int) int {
    return a + b
}

// Multiple return values
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("division by zero")
    }
    return a / b, nil
}

result, err := divide(10, 2)
if err != nil {
    fmt.Println("Error:", err)
}
```

## Methods
```go
type Rectangle struct {
    Width  float64
    Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Pointer receiver
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}
```

## Interfaces
```go
type Shape interface {
    Area() float64
}

type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return 3.14 * c.Radius * c.Radius
}
```

## Goroutines and Channels
```go
// Goroutine
go func() {
    fmt.Println("Running concurrently")
}()

// Channels
ch := make(chan int)

go func() {
    ch <- 42  // Send
}()

value := <-ch  // Receive

// Buffered channel
ch := make(chan int, 2)
ch <- 1
ch <- 2
```

## Error Handling
```go
func readFile(filename string) (string, error) {
    data, err := ioutil.ReadFile(filename)
    if err != nil {
        return "", err
    }
    return string(data), nil
}
```

## Best Practices
1. Format with `gofmt`
2. Handle all errors
3. Use interfaces for flexibility
4. Keep packages small and focused
5. Use goroutines for concurrency
6. Close channels when done
