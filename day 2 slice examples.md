### **Slices in Go**
A **slice** is a dynamically sized, more flexible alternative to an array in Go. Unlike arrays, slices do not have a fixed size, making them a preferred choice in Go.

---

### **1. Basic Slice Declaration and Initialization**
```go
package main

import "fmt"

func main() {
    // Declare and initialize a slice using a slice literal
    numbers := []int{10, 20, 30, 40, 50}

    fmt.Println("Slice:", numbers)
    fmt.Println("Length:", len(numbers))
    fmt.Println("Capacity:", cap(numbers)) // Shows the slice's capacity
}
```
---
### **2. Creating a Slice Using `make()`**
```go
package main

import "fmt"

func main() {
    // Create a slice with make (length: 3, capacity: 5)
    numbers := make([]int, 3, 5)

    fmt.Println("Slice:", numbers)
    fmt.Println("Length:", len(numbers))  // 3
    fmt.Println("Capacity:", cap(numbers)) // 5

    // Assign values
    numbers[0], numbers[1], numbers[2] = 100, 200, 300
    fmt.Println("Updated Slice:", numbers)
}
```
---
### **3. Appending Elements to a Slice**
```go
package main

import "fmt"

func main() {
    numbers := []int{1, 2, 3}
    
    // Append new elements to the slice
    numbers = append(numbers, 4, 5, 6)

    fmt.Println("Slice after append:", numbers)
}
```
---
### **4. Slicing an Existing Slice**
```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40, 50, 60}

    // Slice the slice from index 1 to 4 (excluding index 4)
    part := numbers[1:4]

    fmt.Println("Original Slice:", numbers)
    fmt.Println("Sliced Part:", part) // Output: [20, 30, 40]
}
```
---
### **5. Copying a Slice**
```go
package main

import "fmt"

func main() {
    source := []int{1, 2, 3, 4, 5}
    destination := make([]int, len(source)) // Create a slice of the same length

    // Copy elements from source to destination
    copy(destination, source)

    fmt.Println("Source:", source)
    fmt.Println("Copied Slice:", destination)
}
```
---
### **6. Iterating Over a Slice**
```go
package main

import "fmt"

func main() {
    numbers := []int{10, 20, 30, 40}

    // Using range to iterate over the slice
    for index, value := range numbers {
        fmt.Printf("Index: %d, Value: %d\n", index, value)
    }
}
```
---
### **7. Multi-Dimensional Slice**
```go
package main

import "fmt"

func main() {
    matrix := [][]int{
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9},
    }

    // Print multi-dimensional slice
    fmt.Println("2D Slice:")
    for _, row := range matrix {
        fmt.Println(row)
    }
}
```
---
### **Key Differences Between Slices and Arrays**
| Feature  | Array  | Slice  |
|----------|--------|--------|
| **Size** | Fixed | Dynamic |
| **Resizability** | Cannot grow/shrink | Can grow/shrink |
| **Usage** | Less common | Preferred for flexibility |
| **Memory Efficiency** | Requires exact size | Efficient with `append()` |
