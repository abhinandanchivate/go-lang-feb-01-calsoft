

### **Optimized Web Scraper using Goroutines and Channels in Go**
This Go program fetches data from multiple URLs concurrently while efficiently managing synchronization and communication.

#### **Key Features:**
- **Goroutines**: Fetch data concurrently from multiple URLs.
- **Channels**: Used for result collection and error handling.
- **sync.WaitGroup**: Ensures all Goroutines complete before exiting.

---

### **Go Implementation**


---

### **Explanation of the Implementation**
#### **1. Using Goroutines for Parallel Execution**
Each URL is fetched in a separate Goroutine using:
```go
go fetchData(url, ch, &wg)
```
This ensures that multiple URLs are processed concurrently.

#### **2. Buffered Channel for Communication**
We use a **buffered channel** to store responses:
```go
ch := make(chan Response, len(urls))
```
This avoids **blocking** and allows smooth data transfer between Goroutines.

#### **3. Synchronization with sync.WaitGroup**
To ensure **all Goroutines finish execution**, we use:
```go
wg.Add(1)  // Before launching a Goroutine
wg.Done()  // After completion
wg.Wait()  // Ensures all are finished before exiting
```

#### **4. Graceful Channel Closure**
Since multiple Goroutines send data to the channel, we close it **only after all Goroutines complete**:
```go
go func() {
    wg.Wait()
    close(ch)
}()
```
This prevents **panic errors** from sending to a closed channel.

---

### **Benefits of This Approach**
✅ **Efficient Parallel Execution** → Multiple URLs fetched simultaneously.  
✅ **Prevents Blocking Issues** → Buffered channels manage data transfer efficiently.  
✅ **Proper Synchronization** → sync.WaitGroup ensures orderly execution.  
✅ **Graceful Handling of Errors** → Errors are captured and displayed for debugging.

---

### **Output Example**
```
Fetched data from https://jsonplaceholder.typicode.com/posts/1: { "userId": 1, "id": 1, "title": "sunt au...
Fetched data from https://jsonplaceholder.typicode.com/posts/2: { "userId": 1, "id": 2, "title": "qui est ...
Fetched data from https://jsonplaceholder.typicode.com/posts/3: { "userId": 1, "id": 3, "title": "ea mole...
```

