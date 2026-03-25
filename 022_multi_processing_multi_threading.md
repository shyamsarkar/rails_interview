# Multithreading vs Multiprocessing (Ruby)

---

## Q. What is the difference between Multithreading and Multiprocessing?

**Answer:**

Multithreading and Multiprocessing are two approaches to achieve concurrency (running multiple tasks simultaneously).

---

## 🔹 1. Multithreading

Multithreading runs multiple threads within a single process.  
All threads share the same memory space.

👉 In Ruby (MRI), it is affected by the Global Interpreter Lock (GIL).

---

### ✅ Example

```ruby
threads = []
5.times do |i|
  threads << Thread.new do
    sleep((rand*10).to_i)
    puts "Thread #{i} finished"
  end
end
threads.each(&:join)
```

---

### ✅ Pros

- Lightweight (shared memory)
- Faster context switching
- Easy data sharing
- Good for I/O-bound tasks (API calls, DB queries)

---

### ❌ Cons

- GIL prevents true parallelism (CPU tasks)
- Thread safety issues (race conditions)
- Harder to debug
- Not ideal for CPU-heavy work

---

## 🔹 2. Multiprocessing

Multiprocessing runs multiple processes, each with its own memory and Ruby interpreter.

👉 Achieves true parallelism (no GIL issue).

---

### ✅ Example

```ruby
pids = []
5.times do |i|
  pids << fork do
    sleep(rand(1..3))
    puts "Process #{i} finished (PID: #{Process.pid})"
  end
end

pids.each { |pid| Process.wait(pid) }
```

---

### ✅ Pros

- True parallelism (uses multiple CPU cores)
- Better isolation (no shared memory issues)
- More stable (one crash doesn’t affect others)
- Best for CPU-bound tasks

---

### ❌ Cons

- Higher memory usage
- Slower startup
- Complex communication (IPC)
- More resource-intensive

---

## ⚖️ Key Differences

| Feature        | Multithreading            | Multiprocessing            |
|----------------|---------------------------|----------------------------|
| Memory         | Shared                    | Separate                   |
| Parallelism    | Limited (GIL in MRI)      | True parallelism           |
| Performance    | Good for I/O-bound        | Good for CPU-bound         |
| Isolation      | Low                       | High                       |
| Complexity     | Medium                    | High                       |

---

## 🧰 Tools & Libraries

### Multithreading
- Thread (built-in)
- concurrent-ruby
- Sidekiq (uses threads)

### Multiprocessing
- Process.fork (built-in)
- Parallel gem
- Resque (uses processes)

---

## 🧠 One-liner

- **Multithreading** → Best for I/O-bound tasks  
- **Multiprocessing** → Best for CPU-bound tasks  

---

## ⚡ Interview Tip

👉 In Ruby (MRI):
- Use **threads** for API calls, DB queries  
- Use **processes** for heavy computations  

👉 Example:
- Puma → threads + workers  
- Sidekiq → multithreading

```ruby
3.times do |i|
  fork do
    5.times do |thread|
      sleep(rand(1..5))
      puts "process=#{i} pid=#{Process.pid} thread=#{thread}"
    end
  end
end
```