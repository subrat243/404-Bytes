# Race Conditions (THM)

Race conditions occur when the outcome of a program depends on the timing or order of operations. In a vulnerable system, multiple actions occurring at the same time can lead to incorrect or unexpected results.  
For example:

- A $10 gift card being reused multiple times to purchase a $100 item.
    
- A discount code being applied multiple times.
    
- A banking transaction being processed more than once.
    

This happens when multiple threads or processes access and modify shared data without proper synchronization.

---

# Part 1: Multi-Threading Fundamentals

Understanding race conditions requires understanding the basics of how programs run.

---

## Programs

A program is a static set of instructions that perform a task. It does nothing until executed.

Example: A coffee recipe.  
The recipe describes the steps, but no coffee is produced unless someone follows them.

Example in computing: A minimal Flask “Hello, World!” application.

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<h1>Hello, World!</h1>'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

This code defines how the server behaves but does nothing until it is run.

---

## Processes

A process is a running instance of a program.  
It is dynamic and has:

- Program code
    
- Memory
    
- CPU scheduling and runtime state
    

A process moves through different states:

1. New
    
2. Ready
    
3. Running
    
4. Waiting
    
5. Terminated
    

Example: Running the Flask server creates a process listening on port 8080.  
Most of the time, it stays in the "Waiting" state until a client sends a request.

---

## Threads

A thread is a lightweight execution unit inside a process.  
Threads share:

- Memory
    
- Resources
    
- Code
    

Analogy:  
A coffee shop with an espresso machine that has two portafilters.  
Each portafilter can prepare a shot at the same time.  
The machine is the process, and each portafilter is a thread.

Servers often use multithreading to handle many client requests at the same time.

Example: Running a Flask app via Gunicorn with 4 workers and 2 threads each:

```
gunicorn --workers=4 --threads=2 -b 0.0.0.0:8080 app:app
```

---

# Part 2: What Are Race Conditions?

A race condition occurs when:

- Two or more threads access shared data.
    
- They perform a check or computation.
    
- One modifies the data before the other completes the operation.
    
- The final result becomes inconsistent.
    

This is particularly dangerous in:

- Banking systems
    
- Coupon and gift card systems
    
- Inventory systems
    
- Authentication and voting mechanisms
    

---

## Real-World Analogy

Two hosts in a restaurant simultaneously take reservations for the same table (Table 17).  
Both check the reservation book and see the table is free because the "Reserved" tag has not yet been placed.

Since the system didn’t lock the table during the operation, both reserve the same table.  
This is a race condition caused by lack of synchronization.

---

# Examples of Race Conditions

## Example A: Incorrect Balance Update

- Initial balance = $100
    
- Thread 1 attempts to withdraw $45
    
- Thread 2 attempts to withdraw $35
    
- Both check the balance before either updates it
    

Thread 1 may update the balance to $55.  
Thread 2 may update the balance to $65 based on stale data.  
Final result depends on timing, which is unpredictable.

This leads to inconsistent accounting.

---

## Example B: Bypassing Insufficient Funds Check

- Initial balance = $75
    
- Thread 1 checks $75 and withdraws $50
    
- Thread 2 checks $75 and withdraws $50
    
- Both permitted based on outdated information
    

The system allows a transaction that should have failed.  
This is a Time-of-Check to Time-of-Use (TOCTOU) issue.

---

# Code Example Demonstrating a Race Condition

```python
import threading

x = 0  # Shared variable

def increase_by_10():
    global x
    for i in range(1, 11):
        x += 1
        print(f"Thread {threading.current_thread().name}: {i}0% complete, x = {x}")

thread1 = threading.Thread(target=increase_by_10, name="Thread-1")
thread2 = threading.Thread(target=increase_by_10, name="Thread-2")

thread1.start()
thread2.start()

thread1.join()
thread2.join()

print("Both threads have finished completely.")
```

Each thread increments the same shared variable `x`.  
Running the program multiple times produces different outputs.  
This demonstrates that thread execution order is never guaranteed.

---

# Causes of Race Conditions

1. **Parallel Execution**  
    Web servers handle multiple requests simultaneously.  
    If those requests modify shared state (discount count, wallet balance), race conditions occur.
    
2. **Database Operations**  
    Problems arise when two clients perform read-modify-write operations at the same time.
    
3. **Third-Party Service Calls**  
    APIs or libraries that are not thread-safe may behave unpredictably under concurrent requests.
    
4. **Shared In-Memory Data**  
    In-memory counters, session data, or application caches accessed by multiple threads can cause inconsistency.
    

---

# Exploiting Race Conditions

_(Scenario and Example)_

Attackers exploit race conditions by sending multiple parallel requests during a critical operation.

Typical examples:

- Redeeming a coupon multiple times by firing parallel HTTP requests.
    
- Bypassing one-time actions such as:
    
    - voting once
        
    - rating once
        
    - claiming a gift or reward
        
- Adding the same product multiple times even when stock is limited.
    
- Transferring funds repeatedly before balance updates.
    

Attackers use tools like:

- Burp Suite Intruder
    
- Burp Repeater (parallel mode)
    
- Turbo-intruder
    
- Custom scripts to send thousands of requests within milliseconds
    

The goal is to hit the system between the “check” and the “update”.

---

# Detection

Race conditions are difficult to detect because they rarely appear in normal usage.  
However, testing should focus on operations that enforce restrictions such as:

- Use once
    
- Redeem once
    
- Withdraw only if balance allows
    
- One action every few minutes
    
- Rate or vote only once
    

Penetration testers attempt to bypass these restrictions using concurrent requests.

Tools used:

- Burp Suite Repeater (multiple tabs firing simultaneously)
    
- Burp Intruder (parallel requests)
    
- Custom Python or Bash scripts
    
- Browser-based parallel requests
    

Logs may reveal:

- Duplicate redemptions
    
- Out-of-stock items being purchased
    
- Negative balances
    
- Multiple votes from the same account
    

---

# Mitigation Strategies

## 1. Synchronization Mechanisms

Use locks or mutexes to ensure only one thread accesses shared data at a time.

Example:

```python
lock = threading.Lock()
with lock:
    x += 1
```

## 2. Atomic Operations

Ensure operations execute as an indivisible unit.  
Atomicity guarantees the operation cannot be interrupted.

Examples:

- Atomic SQL UPDATE statements
    
- Atomic counters
    
- Database-level atomic operations
    

## 3. Database Transactions

Use ACID transactions to group related operations into a single unit.

Example with row-level locking:

```sql
BEGIN;

SELECT balance FROM accounts WHERE id=1 FOR UPDATE;

UPDATE accounts
SET balance = balance - 50
WHERE id=1;

COMMIT;
```

This ensures no other session can change the balance until the transaction completes.

## 4. Logical Validations After Update

Even after updating, re-validate the operation.

Example: Re-check stock level after decrement.

## 5. Unique Tokens for One-Time Actions

For actions such as:

- vote once
    
- redeem once
    
- apply coupon once
    

Use one-time tokens stored and validated server-side.

## 6. Rate Limiting

Reduce likelihood of successful race attacks by limiting the number of concurrent operations per user/IP.

---

# Summary

Race conditions occur when two or more operations interact with shared data in a way that their final outcome depends on timing.  
They are dangerous because attackers can exploit slight timing windows to bypass important business logic such as payment validation, coupon redemption, stock checks, and balance checks.

Understanding programs, processes, threads, and shared resources is essential to recognizing race conditions.  
Proper synchronization, atomicity, transactions, and careful design are key to preventing these vulnerabilities.

---

If you want, I can also provide:

- A simplified short-note version
    
- A diagram-based explanation
    
- A PDF-ready formatting version
    

Just tell me.