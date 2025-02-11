To integrate custom modules and submodules locally in Go, follow these steps carefully. This method ensures that you don't need to publish the submodules to a remote repository.

---

### **1. Set Up the Main Module Locally**
Create a new directory for your project:

```sh
mkdir -p ~/go-workspace/mainmodule
cd ~/go-workspace/mainmodule
```

Initialize the Go module:

```sh
go mod init mainmodule
```

---

### **2. Create a Submodule Locally**
Inside `~/go-workspace/mainmodule`, create a submodule:

```sh
mkdir -p submodules/mysubmodule
cd submodules/mysubmodule
```

Initialize the submodule:

```sh
go mod init mainmodule/submodules/mysubmodule
```

Create the submodule logic in `mysubmodule.go`:

```go
package mysubmodule

import "fmt"

func Hello() {
    fmt.Println("Hello from MySubmodule!")
}
```

---

### **3. Use the Submodule in the Main Module**
Go back to the main module directory:

```sh
cd ~/go-workspace/mainmodule
```

Modify `go.mod` to **replace** the submodule with the local path:

```sh
go mod edit -replace mainmodule/submodules/mysubmodule=./submodules/mysubmodule
```

Now, create `main.go` in the main module:

```go
package main

import (
    "fmt"
    "mainmodule/submodules/mysubmodule"
)

func main() {
    fmt.Println("Main Module")
    mysubmodule.Hello()
}
```

---

### **4. Resolve Dependencies Locally**
Run:

```sh
go mod tidy
```

This ensures Go picks up the local submodule instead of searching for it online.

---

### **5. Run the Program**
```sh
go run main.go
```

Expected output:

```
Main Module
Hello from MySubmodule!
```

---

### **6. Verify the Local Setup**
To check if the local module replacement works, run:

```sh
go list -m all
```

You should see:

```
mainmodule
mainmodule/submodules/mysubmodule => ./submodules/mysubmodule
```

This confirms that Go is using the local submodule.

---

