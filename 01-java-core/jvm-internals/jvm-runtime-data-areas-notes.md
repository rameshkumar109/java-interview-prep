# 📌 Topic: JVM Runtime Data Areas
**Date: 20-Jun-2026**

## 1. All 5 Runtime Data Areas

JVM Memory
 ├── 1. Heap
 ├── 2. Stack
 ├── 3. Method Area / Metaspace
 ├── 4. PC Register
 └── 5. Native Method Stack

## 2. Heap — Largest Memory Area

Heap
 ├── Young Generation
 │    ├── Eden Space   → new objects created here first
 │    ├── Survivor S0  → objects that survived 1st GC
 │    └── Survivor S1  → objects that survived 2nd GC
 └── Old Generation    → long-lived objects move here

- Stores: objects, instance variables, arrays
- Shared across all threads
- GC runs here
- Error: OutOfMemoryError: Java heap space

## 3. Stack — One Per Thread

- Stores: method calls, local variables, 
  references to Heap objects
- Each thread has its own Stack
- Each method call = new Stack Frame created
- Method ends = Stack Frame destroyed automatically
- Error: StackOverflowError (infinite recursion)

### Stack Frame contains:
- Local variables
- Method parameters
- Reference to object in Heap
- Return value

### ChatterBox Example:
public Message sendMessage(String content, Long roomId) {
    // content → Stack (local)
    // roomId  → Stack (local)
    // msg     → Stack (reference only!)
    Message msg = new Message(content); // object → Heap
    return msg;
} // method ends → Stack Frame destroyed

## 4. Method Area / Metaspace — Shared

- Stores: class metadata, static variables, 
  static methods, constant pool
- Shared across all threads
- In Java 8+: called Metaspace (uses native memory)
- Error: OutOfMemoryError: Metaspace

### Example:
public class KafkaConsumerService {
    static int messageCount = 0;       // → Method Area
    public static void process() { }   // → Method Area
}

## 5. PC Register — One Per Thread

- Stores address of currently executing instruction
- Each thread has its own PC Register
- JVM manages this automatically

## 6. Native Method Stack — One Per Thread

- Used when Java calls non-Java code (C/C++)
- Example: System.currentTimeMillis() 
  internally calls native code

## 7. Complete Memory Picture

Shared across all threads:
 ├── Heap (Young Gen + Old Gen)
 └── Method Area / Metaspace

Per Thread (each thread gets its own):
 ├── Stack
 ├── PC Register
 └── Native Method Stack

## 8. Heap vs Stack vs Method Area

|              | Heap              | Stack                    | Method Area         |
|--------------|-------------------|--------------------------|---------------------|
| Stores       | Objects, arrays   | Method calls, local vars | Class data, static  |
| Shared?      | Yes               | No (per thread)          | Yes                 |
| GC runs?     | Yes               | No                       | No                  |
| Error        | OutOfMemoryError  | StackOverflowError       | OutOfMemoryError    |

## 9. GC — Minor vs Major

Minor GC → cleans Young Generation (fast, frequent)
Major GC → cleans Old Generation (slow, rare)

Object lifecycle:
New object → Eden Space
     ↓ (survives Minor GC)
Survivor S0/S1
     ↓ (survives multiple GCs)
Old Generation
     ↓ (Major GC runs)
Deleted if unreachable

## 10. Interview One-Liner Answers

Q: Where are objects stored?
→ Heap memory — Eden space first, then Old Generation 
  if long-lived. In ChatterBox, every Message and User 
  object goes to Heap.

Q: What is Metaspace?
→ Replaced PermGen in Java 8. Stores class metadata 
  and static variables using native memory. In ChatterBox,
  all Spring Boot component class definitions 
  are stored in Metaspace.

Q: Why does each thread have its own Stack?
→ Each thread executes independently. In ChatterBox, 
  Kafka consumer thread and HTTP request thread run 
  simultaneously — each needs its own Stack.

Q: Difference between Minor GC and Major GC?
→ Minor GC cleans Young Generation — fast and frequent.
  Major GC cleans Old Generation — slow and rare.
  In HP BCP we monitored GC pauses on Grafana.

## 11. My Real Project Connection

- HP BCP on AWS EKS → each pod = one JVM instance
- Spring Boot HTTP threads → each has own Stack
- Kafka consumer threads → each has own Stack
- Message/User objects → stored in Heap
- KafkaConsumerService static vars → Method Area
- HikariCP connection pool objects → Heap

## 12. Common Interview Mistakes

❌ "Static variables are in Stack" 
   → Wrong! They are in Method Area
❌ "Each thread shares one Stack" 
   → Wrong! Each thread has own Stack
❌ "Metaspace is part of Heap" 
   → Wrong! Metaspace uses native memory
❌ "Objects are in Stack" 
   → Wrong! References in Stack, objects in Heap