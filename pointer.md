Stack Memory (Main Function)
----------------------------
nums (Array Variable)
----------------------------
| Index | Address | Value  |
|-------|---------|--------|
|   0   | 0x1000  |   1    |   ---> Before modification
|   1   | 0x1004  |   2    |
|   2   | 0x1008  |   3    |
----------------------------

Function Call (modifyArray)
----------------------------
Pointer to nums
----------------------------
arr (Pointer) ---> 0x1000
arr[0] = 100   (modifies nums[0] at 0x1000)
----------------------------

After modification:
----------------------------
| Index | Address | Value  |
|-------|---------|--------|
|   0   | 0x1000  |  100   |   ---> After modification
|   1   | 0x1004  |   2    |
|   2   | 0x1008  |   3    |
----------------------------



### **Execution Flow of the Go Program with Internal Memory Representation**

#### **Program:**
```go
package main

import "fmt"

func modifyArray(arr *[3]int) {
    arr[0] = 100  // Modifying the first element
}

func main() {
    nums := [3]int{1, 2, 3}
    fmt.Println("Before:", nums)
    
    modifyArray(&nums) // Pass array pointer
    
    fmt.Println("After:", nums)
}
```

---

### **Step-by-Step Execution Flow**

1. **Program Starts:**
   - The `main()` function is executed.
   - An array `nums` of size 3 is allocated in the stack.
   - Initial values of `nums`: `{1, 2, 3}`.

   **Memory Layout at this point:**
   ```
   Stack Memory:
   nums[0] -> Address 0x1000 | Value: 1
   nums[1] -> Address 0x1004 | Value: 2
   nums[2] -> Address 0x1008 | Value: 3
   ```

2. **Before Modification (Inside `main`)**
   - `fmt.Println("Before:", nums)` prints:
     ```
     Before: [1 2 3]
     ```
   - `modifyArray(&nums)` is called, passing a pointer to `nums`.

3. **Function Call (`modifyArray`)**
   - The pointer `arr` now points to `nums`.
   - `arr[0] = 100` modifies the first element at memory address `0x1000`.
   - Now, `nums[0]` is updated to `100`.

   **Updated Memory Layout:**
   ```
   Stack Memory:
   nums[0] -> Address 0x1000 | Value: 100  <-- Modified
   nums[1] -> Address 0x1004 | Value: 2
   nums[2] -> Address 0x1008 | Value: 3
   ```

4. **Function Returns (`modifyArray` finishes execution)**
   - Since we modified `nums` via a pointer, `nums` retains its updated value in `main()`.
   - Control goes back to `main()`.

5. **After Modification (Inside `main`)**
   - `fmt.Println("After:", nums)` prints:
     ```
     After: [100 2 3]
     ```

6. **Program Ends**
   - The execution finishes with the modified array `[100, 2, 3]`.

---

### **Full Internal Memory Diagram with Execution Flow**

```
+----------------------------+
|       Stack Memory         |
+----------------------------+
|   nums array (main)        |
|   ---------------          |
|   Address  |  Value        |
|   ---------|--------       |
|   0x1000   |  1   → 100    |  <-- Modified
|   0x1004   |  2            |
|   0x1008   |  3            |
+----------------------------+
| Call Stack: modifyArray()  |
| arr -> Pointer to nums     |
+----------------------------+
| Execution Flow             |
| 1. nums initialized        |
| 2. Before print statement  |
| 3. modifyArray(&nums) call |
| 4. arr[0] modified         |
| 5. modifyArray returns     |
| 6. After print statement   |
| 7. Program exits           |
+----------------------------+
```

---

### **Key Takeaways**
1. **Pass-by-Pointer:**
   - The function `modifyArray` receives a pointer, so changes affect the original array.
   - Instead of copying the array, the function operates on the same memory location.

2. **Stack Memory Usage:**
   - Both `nums` and the pointer `arr` exist in the stack memory.
   - `arr` points to `nums`, so modifications are reflected in `nums`.

3. **Execution Order:**
   - `main()` initializes `nums` and prints the original values.
   - `modifyArray()` updates `nums[0]` via a pointer.
   - Returning to `main()`, the modified array is printed.

