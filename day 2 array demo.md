In Go, an **array** is a fixed-size collection of elements of the same type. Here’s a simple example demonstrating how to declare, initialize, and use an array in Go:

### **Basic Array Example**
```go
package main

import "fmt"

func main() {
    // Declare an array of integers with a fixed size of 5
    var numbers [5]int

    // Assign values to the array
    numbers[0] = 10
    numbers[1] = 20
    numbers[2] = 30
    numbers[3] = 40
    numbers[4] = 50

    // Print the array
    fmt.Println("Array:", numbers)

    // Access a specific element
    fmt.Println("Element at index 2:", numbers[2])

    // Get the length of the array
    fmt.Println("Array length:", len(numbers))
}
```

### **Array Initialization with Values**
```go
package main

import "fmt"

func main() {
    // Declare and initialize an array in one step
    numbers := [5]int{1, 2, 3, 4, 5}

    // Print all elements
    for i, value := range numbers {
        fmt.Printf("Index %d: %d\n", i, value)
    }
}
```

### **Using `[...]` to Let the Compiler Determine the Size**
```go
package main

import "fmt"

func main() {
    // Let Go determine the size of the array
    numbers := [...]int{10, 20, 30, 40}

    fmt.Println("Array:", numbers)
    fmt.Println("Array length:", len(numbers))
}
```

### **Multi-Dimensional Array**
```go
package main

import "fmt"

func main() {
    // Declare a 2D array (3 rows x 2 columns)
    matrix := [3][2]int{
        {1, 2},
        {3, 4},
        {5, 6},
    }

    // Print the 2D array
    fmt.Println("2D Array:")
    for i := 0; i < len(matrix); i++ {
        fmt.Println(matrix[i])
    }
}
```

### **Key Takeaways**
- Arrays have a **fixed** size.
- The size is part of the array type, so `[5]int` and `[10]int` are different types.
- Use **slices** (`[]int`) instead of arrays if you need a dynamic size.

Would you like an example using **slices** instead?
