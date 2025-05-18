### **Day 56: Mailboxes and Queues in SystemVerilog**

---

### **Objective:**

Understand how **mailboxes** and **queues** are used for inter-process communication and data storage in SystemVerilog testbenches, especially in transaction-level modeling.

---

## ✅ 1. Mailboxes in SystemVerilog

---

### What is a Mailbox?

A **mailbox** is a built-in SystemVerilog construct that allows communication between **concurrent processes** (e.g., between a driver and monitor or between threads).

It supports:

* **`put()`** – insert data into the mailbox
* **`get()`** – wait and remove data from the mailbox
* **`peek()`** – wait and read the next data without removing it
* Synchronization between threads

---

### Declaring a Mailbox

```systemverilog
mailbox mbox;               // unbounded
mailbox #(Transaction) mbox; // typed mailbox (optional)
```

---

### Creating a Mailbox

```systemverilog
mbox = new();        // default (unbounded)
mbox = new(10);      // bounded with capacity 10
```

---

### Using a Mailbox (Basic Example)

```systemverilog
// Process 1 - producer
fork
    Transaction t = new();
    t.addr = 8'hA5;
    mbox.put(t); // send to mailbox
join_none

// Process 2 - consumer
fork
    Transaction r;
    mbox.get(r);  // receive from mailbox
    $display("Received addr = %h", r.addr);
join_none
```

---

## ✅ 2. Queues in SystemVerilog

---

### What is a Queue?

A **queue** is a variable-size, ordered collection of elements, similar to an array, but with dynamic sizing.

---

### Declaring a Queue

```systemverilog
int q1[$];              // Queue of integers
Transaction t_queue[$]; // Queue of class objects
```

---

### Queue Operations

| Operation      | Description                    |
| -------------- | ------------------------------ |
| `push_front()` | Adds item at the beginning     |
| `push_back()`  | Adds item at the end (FIFO)    |
| `pop_front()`  | Removes and returns first item |
| `pop_back()`   | Removes and returns last item  |
| `delete()`     | Clears the queue               |

---

### Queue Example

```systemverilog
Transaction t1 = new(), t2 = new();
t1.addr = 8'hAA;
t2.addr = 8'h55;

Transaction q[$];      // Queue of Transaction

q.push_back(t1);
q.push_back(t2);

Transaction x = q.pop_front();  // x gets t1
$display("Popped addr = %h", x.addr);
```

---

## ✅ 3. Comparison: Mailbox vs Queue

| Feature     | Mailbox                        | Queue                        |
| ----------- | ------------------------------ | ---------------------------- |
| Type        | Built-in synchronization tool  | Pure data structure          |
| Blocking?   | Yes (`get()` blocks)           | No blocking                  |
| Use case    | Inter-thread/process messaging | Temporary in-process storage |
| Methods     | `put`, `get`, `peek`           | `push`, `pop`, index access  |
| Flexibility | Unbounded or bounded           | Fully dynamic                |

---

## ✅ 4. When to Use What?

* Use **mailboxes** when:

  * Communicating between driver and monitor
  * Synchronizing threads
  * Emulating producer-consumer behavior

* Use **queues** when:

  * You need temporary storage
  * Working within a single process
  * Handling LIFO/FIFO operations

---

### ✅ Summary

| Concept  | Purpose                              |
| -------- | ------------------------------------ |
| Mailbox  | Safe communication between processes |
| Queue    | Dynamic, ordered collection          |
| Use case | Testbench data flow, transactions    |

---

**Next Topic (Day 57):** Randomization in SystemVerilog – Theory and Examples

Let me know if you want a full **code example** using both mailboxes and queues in a testbench scenario.
