Case Study: Optimizing a Web Scraper using Goroutines and Channels in Go
Overview

A company wants to build a high-performance web scraper that can fetch data from multiple URLs concurrently while efficiently managing communication between different parts of the system. The key challenges are:

Fetching data from multiple websites efficiently.
Avoiding bottlenecks and ensuring smooth communication between goroutines.
Synchronizing results and handling errors properly.
To solve these challenges, goroutines and channels are used.

Problem Statement
The goal is to develop a web scraper that:

Fetches data concurrently from a list of URLs.
Uses channels to collect results and handle errors.
Ensures proper synchronization using sync.WaitGroup to prevent premature termination.
Solution Approach
Worker Goroutines: Multiple goroutines fetch data concurrently.
Buffered Channels: Used for communication between goroutines.
WaitGroup: Ensures that the main function waits for all goroutines to complete before exiting.
Explanation
Goroutines for Parallel Execution:
The fetchData() function runs in a separate goroutine for each URL.
The main function doesn’t block while waiting for each request.
Buffered Channel for Communication:
The ch channel collects responses from goroutines.
It prevents blocking issues since it's buffered.
Synchronization using sync.WaitGroup:
wg.Add(1) before starting a goroutine.
wg.Done() ensures that we track when each goroutine completes.
wg.Wait() makes sure the channel closes only after all goroutines finish.
Graceful Channel Closure:
We use go func() { wg.Wait(); close(ch) }() to ensure channels close only when all goroutines complete.
Benefits of Using Goroutines and Channels
Concurrency: Multiple URLs are fetched in parallel, improving performance.
Efficient Communication: The buffered channel prevents blocking issues.
Safe Synchronization: sync.WaitGroup ensures goroutines complete before proceeding.
