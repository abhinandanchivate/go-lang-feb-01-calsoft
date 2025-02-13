## **Conditional Execution Using `defer`, `panic`, and `recover` in Go**

### **1. Understanding `defer`, `panic`, and `recover`**
These three mechanisms allow Go to handle **error-prone execution flows** effectively:

| **Keyword** | **Purpose** |
|-------------|------------|
| `defer` | Schedules a function call to run **after the surrounding function completes**, useful for cleanup operations. |
| `panic` | Terminates the program execution immediately, but **deferred functions still run** before exit. |
| `recover` | Catches a `panic`, **prevents the program from crashing**, and allows graceful error handling. |

---

## **2. `defer` - Executing Cleanup Operations**

### **🔹 Concept**
- `defer` schedules a function to run **at the end of the enclosing function**.
- Typically used for **resource cleanup** (e.g., closing files, unlocking mutexes, database connections).

### **🔹 Example: Ensuring File Closure**
```go
package main

import (
    "fmt"
    "os"
)

func main() {
    file, err := os.Open("example.txt")
    if err != nil {
        fmt.Println("Error opening file:", err)
        return
    }
    defer file.Close() // Ensures file is closed before function exits

    fmt.Println("File opened successfully")
}
```
Execution Sequence:
Step 1: os.Open("example.txt") attempts to open the file.
Step 2: If an error occurs (err != nil), the program enters the if block.
Step 3: Prints "Error opening file: <error_message>" if the file does not exist or cannot be accessed.
Step 4: Calls return, exiting the main function immediately if an error occurs.
Step 5: If no error occurs, defer file.Close() is executed, scheduling file.Close() to run at the end of main().
Step 6: "File opened successfully" is printed.
🔹 **Why use `defer`?**
- Ensures `file.Close()` is **executed even if an early return or error occurs**.
- Improves **code readability** (close operation is placed near open operation).

---

## **3. `panic` - Handling Critical Errors**
### **🔹 Concept**
- `panic` **stops normal execution** and **runs deferred functions** before exiting.
- Used for **unexpected, unrecoverable errors** (e.g., out-of-bounds access, nil pointer dereference).

### **🔹 Example: Manually Triggering a Panic**
```go
package main

import "fmt"

func main() {
    fmt.Println("Start of program")

    panic("Something went wrong!") // Terminates program

    fmt.Println("This will never execute")
}
```
🔹 **Why use `panic`?**
- Indicates a **severe error** that should not happen.
- Used for **assertions** and scenarios where continuing execution **is dangerous**.

---

## **4. `recover` - Preventing Program Crash**
### **🔹 Concept**
- `recover` **catches a panic** and **prevents the program from terminating**.
- Must be called inside a `defer` function.

### **🔹 Example: Recovering from a Panic**
```go
package main

import "fmt"

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from panic:", r)
        }
    }()

    fmt.Println("Before panic")
    panic("Critical Error!") // Execution jumps to deferred function
    fmt.Println("After panic") // This will not execute
}
```
🔹 **Why use `recover`?**
- Allows a program to **gracefully handle unexpected failures**.
- Used in **web servers, databases**, and **long-running applications** to **prevent complete crashes**.

---

## **5. Combining `defer`, `panic`, and `recover`**
### **Example: Graceful Error Handling in a Function**
```go
package main

import "fmt"

func safeDivide(a, b int) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from:", r)
        }
    }()

    if b == 0 {
        panic("Division by zero is not allowed")
    }

    fmt.Println("Result:", a/b)
}

func main() {
    safeDivide(10, 2)  // Works normally
    safeDivide(5, 0)   // Triggers panic, but recovered
    fmt.Println("Program continues execution")
}
```
🔹 **Key Takeaways**:
- If `b == 0`, `panic` is triggered.
- `recover()` catches the panic and prevents the program from crashing.

---

## **6. Where to Use `defer`, `panic`, and `recover`?**
| **Use Case** | **When to Use?** |
|-------------|----------------|
| **`defer`** | - Closing files, releasing locks, database connections cleanup. <br> - Ensuring function execution order. |
| **`panic`** | - Unrecoverable errors: corrupt memory, missing critical resources. <br> - Unexpected system failures. |
| **`recover`** | - Prevent crashes in critical applications. <br> - Handling panics inside web servers, API handlers, goroutines. |

---

## **7. Practical Example: Error Handling in a Web Server**
### **Example: Preventing Web Server Crash with `recover`**
```go
package main

import (
    "fmt"
    "net/http"
)

func safeHandler(w http.ResponseWriter, r *http.Request) {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println("Recovered from panic:", err)
            http.Error(w, "Internal Server Error", http.StatusInternalServerError)
        }
    }()

    panic("Unexpected server error!") // Simulate crash
}

func main() {
    http.HandleFunc("/", safeHandler)
    fmt.Println("Server started on :8080")
    http.ListenAndServe(":8080", nil)
}
```
🔹 **Why is this important?**
- Prevents **one failed request** from crashing the entire server.
- Ensures **high availability**.

---

## **8. Final Summary**
| **Concept**  | **What It Does?** | **Best Use Cases** |
|--------------|------------------|--------------------|
| `defer` | Executes a function **at the end of the current function** | Resource cleanup, logging, finalization. |
| `panic` | **Immediately stops execution** and runs deferred functions before exit | Fatal errors, assertions, invalid states. |
| `recover` | Catches and handles **panic errors** to prevent crashes | Web servers, long-running apps, Goroutines. |

---
