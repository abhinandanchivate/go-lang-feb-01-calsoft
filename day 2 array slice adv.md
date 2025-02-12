### **Advanced Slice Examples in Go**
Here are some advanced examples demonstrating **slice optimizations**, **using slices in functions**, and **memory management** techniques.

---

### **1. Passing Slices to Functions**
Slices in Go are passed **by reference**, meaning modifications inside the function affect the original slice.

```go
package main

import "fmt"

// Function that modifies a slice
func modifySlice(s []int) {
    for i := range s {
        s[i] *= 2 // Double each element
    }
}

func main() {
    numbers := []int{1, 2, 3, 4, 5}
    
    fmt.Println("Before modification:", numbers)
    
    modifySlice(numbers) // Modifies the original slice
    
    fmt.Println("After modification:", numbers)
}
```
🔹 **Output:**  
```
Before modification: [1 2 3 4 5]  
After modification: [2 4 6 8 10]  
```
---
### **2. Efficient Slice Growth**
Go **doubles the capacity** of a slice when it exceeds its limit, leading to optimized memory allocation.

```go
package main

import "fmt"

func main() {
    var numbers []int

    // Append elements and track capacity
    for i := 1; i <= 10; i++ {
        numbers = append(numbers, i)
        fmt.Printf("Length: %d, Capacity: %d\n", len(numbers), cap(numbers))
    }
}
```
🔹 **Output:** (Example capacity increase pattern)
```
Length: 1, Capacity: 1  
Length: 2, Capacity: 2  
Length: 3, Capacity: 4  
Length: 4, Capacity: 4  
Length: 5, Capacity: 8  
Length: 6, Capacity: 8  
Length: 7, Capacity: 8  
Length: 8, Capacity: 8  
Length: 9, Capacity: 16  
```
🔹 **Optimization Tip:** Use `make()` to preallocate memory and avoid frequent reallocation.

```go
numbers := make([]int, 0, 10) // Pre-allocate capacity of 10
```
---
### **3. Avoiding Slice Memory Leaks**
Slices share the **same underlying array**, meaning if you create a sub-slice from a large slice, the original array remains in memory.

🔴 **Problem: Memory leak due to slicing**
```go
package main

import "fmt"

func main() {
    data := make([]int, 1000000) // Large slice
    smallSlice := data[:10] // Creating a small slice

    fmt.Println("Small slice:", smallSlice)

    // Even though we use only 10 elements, the full array is still in memory!
}
```
🔹 **Solution: Copying Data to a New Slice**
```go
smallSliceCopy := append([]int{}, smallSlice...) // Creates a new slice with only 10 elements
```
---
### **4. Removing an Element from a Slice**
Since slices don’t have a built-in `remove` method, you can implement it manually.

```go
package main

import "fmt"

// Remove an element at index `i`
func removeElement(s []int, i int) []int {
    return append(s[:i], s[i+1:]...)
}

func main() {
    numbers := []int{1, 2, 3, 4, 5}

    numbers = removeElement(numbers, 2) // Remove element at index 2 (value 3)

    fmt.Println("Slice after removal:", numbers)
}
```
🔹 **Output:**  
```
Slice after removal: [1 2 4 5]
```
---
### **5. Inserting an Element into a Slice**
Go doesn't provide a direct way to insert an element at a specific index, so we do it manually.

```go
package main

import "fmt"

// Insert element at index i
func insertElement(s []int, index int, value int) []int {
    s = append(s[:index+1], s[index:]...) // Create space for the new element
    s[index] = value                      // Insert the new element
    return s
}

func main() {
    numbers := []int{1, 2, 4, 5}

    numbers = insertElement(numbers, 2, 3) // Insert 3 at index 2

    fmt.Println("Slice after insertion:", numbers)
}
```
🔹 **Output:**  
```
Slice after insertion: [1 2 3 4 5]
```
---
### **6. Efficiently Checking if a Slice Contains an Element**
```go
package main

import "fmt"

// Check if a slice contains a value
func contains(slice []int, value int) bool {
    for _, v := range slice {
        if v == value {
            return true
        }
    }
    return false
}

func main() {
    numbers := []int{10, 20, 30, 40}

    fmt.Println("Contains 20?", contains(numbers, 20)) // true
    fmt.Println("Contains 50?", contains(numbers, 50)) // false
}
```
---
### **7. Sorting a Slice**
Go provides `sort` from the `sort` package to sort slices.

```go
package main

import (
    "fmt"
    "sort"
)

func main() {
    numbers := []int{5, 2, 8, 1, 9}

    sort.Ints(numbers) // Sort in ascending order

    fmt.Println("Sorted Slice:", numbers)
}
```
🔹 **Output:**  
```
Sorted Slice: [1 2 5 8 9]
```
---
### **8. Converting an Array to a Slice**
```go
package main

import "fmt"

func main() {
    arr := [5]int{1, 2, 3, 4, 5} // Array
    slice := arr[:] // Convert to slice

    fmt.Println("Slice from array:", slice)
}
```
---
## **Key Takeaways**
✅ **Slices are passed by reference**, so modifying a slice inside a function affects the original.  
✅ **Avoid memory leaks** by copying sub-slices when necessary.  
✅ **Use `append()`** to dynamically grow slices efficiently.  
✅ **Slices do not have built-in remove or insert functions**, but you can implement them manually.  
✅ **Use `sort` package** for sorting slices.

