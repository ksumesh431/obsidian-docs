basic syntax https://www.youtube.com/watch?v=Cn5vrUDYtyc

https://youtu.be/un6ZyFkqFKo?si=_uHYD1geRvsOSIoL
---

In Golang, there is no need to import .go files that are in the same directory because they are all part of single package

one directory can contain only one package. 

---


``` Go
if result := someFunction(); result == "expectedValue" { 
// Code to be executed if the result is as expected 
} else { 
// Code to be executed if the result is not as expected 
}
 ```


In this example, `someFunction()` is called, and its return value is assigned to the variable `result`. The expression `result == "expectedValue"` is then evaluated. If it is true, the code inside the block is executed. If it is false, the code inside the `else` block is executed.

---

# For loop

``` golang
func Hellos(names []string) (map[string]string, error) {

    // A map to associate names with messages.

    messages := make(map[string]string)

    // Loop through the received slice of names, calling

    // the Hello function to get a message for each name.

    for _, name := range names {

        message, err := Hello(name)

        if err != nil {

            return nil, err

        }

        // In the map, associate the retrieved message with

        // the name.

        messages[name] = message

    }

    return messages, nil

}

```

1. The `for` keyword is used to start the range loop.
    
2. The underscore `_` is used as a placeholder for the index of the elements in the `names` slice. Since we are only interested in the values and not the indices, we use the underscore to indicate that we are not using the index.
    
3. The `range` keyword is followed by the expression `names`. This expression specifies the collection or sequence over which the loop iterates. In this case, it is the `names` slice.
    
4. The `:=` syntax is used to declare and assign the value of the current element in each iteration. The variable `name` is declared and will hold the value of each element in the `names` slice.
    
5. The loop body is enclosed in curly braces `{}`. Inside the loop body, you can perform any operations or logic that you want to execute for each element in the `names` slice.
    
6. In this specific example, the loop iterates over each element in the `names` slice. On each iteration, the value of the current element is assigned to the `name` variable.
    
## example loop which use somehtign else isnetad of the _ placegolde for the loop



``` 
for index, name := range names {
	// Loop body
	fmt.Printf("Index: %d, Name: %s\n", index, name) 
}
```

In this example, the loop iterates over the `names` slice and assigns the index and value of each element to the variables `index` and `name`, respectively. The loop body can then perform any desired operations using these variables.

Inside the loop body, I've added a `fmt.Printf` statement to print the index and name of each element. You can replace this with your own logic or operations as needed.


## Example 2

![](Images/Pasted%20image%2020240330222730.png)


---



# Structs vs interfaces

**Scenario: Shape manipulation in a drawing program**

Imagine you're building a basic drawing program. You want to represent different shapes like squares, circles, and triangles. Each shape might have its own properties (like side length for a square or radius for a circle) but they all share some common functionalities like calculating area.

Here's how interfaces and structs can help:

1. **IShape Interface:**


``` Go
package main

import "math"

// IShape interface defines a contract for shapes
type IShape interface {
  Area() float64
}
```


This interface defines a single method called `Area()`. Any struct representing a shape needs to implement this method to calculate its area.

2. **Shape Struct (Base Struct):**

```Go
// Shape struct holds common properties for shapes
type Shape struct {
  Color string
}
```


This base `Shape` struct can hold properties common to all shapes, like color in this example.

3. **Specific Shape Structs:**

```Go
// Square struct implements IShape
type Square struct {
  Shape
  SideLength float64
}

func (s *Square) Area() float64 {
  return s.SideLength * s.SideLength
}

// Circle struct implements IShape
type Circle struct {
  Shape
  Radius float64
}

func (c *Circle) Area() float64 {
  return math.Pi * c.Radius * c.Radius
}
```

Here, we have separate structs for `Square` and `Circle` that inherit from the base `Shape` struct. Each struct defines its specific properties (e.g., `SideLength` for `Square` and `Radius` for `Circle`). They both implement the `IShape` interface by providing their own implementation of the `Area()` method.

4. **Using Shapes with Interface:**

```Go
func GetTotalArea(shapes []IShape) float64 {
  totalArea := 0.0
  for _, shape := range shapes {
    totalArea += shape.Area()
  }
  return totalArea
}

func main() {
  shapes := []IShape{
    &Square{SideLength: 5},
    &Circle{Radius: 3},
  }
  totalArea := GetTotalArea(shapes)
  fmt.Println("Total area of shapes:", totalArea)
}
```

The `GetTotalArea` function takes a slice of `IShape`. This allows it to work with any struct that implements the `IShape` interface, regardless of whether it's a square, circle, or potentially another shape type added later. The function iterates through the shapes and calls the `Area()` method on each one, leveraging the interface to ensure the method exists.

**Benefits of using Interfaces and Structs:**

- **Flexibility:** You can easily add new shapes (e.g., Triangle) by creating a new struct that implements the `IShape` interface without modifying existing code.
- **Maintainability:** The logic for calculating area is encapsulated within each shape struct. This makes the code more modular and easier to understand.
- **Reusability:** The `GetTotalArea` function can be reused with any set of shapes as long as they implement `IShape`.

This is a basic example, but it demonstrates how interfaces and structs can promote code flexibility, maintainability, and reusability in real-world scenarios.