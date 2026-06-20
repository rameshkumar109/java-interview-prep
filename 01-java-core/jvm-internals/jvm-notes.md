## 📌 Topic: JVM Architecture
**Date: 19-Jun-2026**

---

### 1. What is JVM?
JVM = Java Virtual Machine
- JVM **executes** bytecode (.class file)
- JVM does NOT compile — that is javac compiler's job
- Every OS has its own JVM — Windows, Linux, Mac

---

### 2. How Java Code Runs — Full Flow

```
YourCode.java
     ↓
  javac (compiler)
     ↓
YourCode.class (bytecode)
     ↓
  JVM (reads and executes bytecode)
     ↓
Machine Code (OS understands this)
```

---

### 3. WORA Principle
**Write Once Run Anywhere**
- Same .class file runs on any OS
- Because every OS has its own JVM
- JVM acts as middle layer between bytecode and OS

---

### 4. JVM 3 Main Parts

```
JVM
 ├── 1. ClassLoader
 ├── 2. Runtime Data Areas (Memory)
 └── 3. Execution Engine
```

**ClassLoader** → loads .class files into memory

**Runtime Data Areas** → stores data while program runs
- Heap → objects and instance variables
- Stack → method calls and local variables
- Method Area → class level data, static variables
- PC Register → tracks current instruction
- Native Method Stack → non-Java code

**Execution Engine** → actually runs the bytecode
- Interpreter → runs line by line (slow)
- JIT Compiler → compiles hot code to machine code (fast)
- Garbage Collector → cleans unused objects from Heap

---

### 5. Heap vs Stack — Key Difference

| | Heap | Stack |
|---|---|---|
| Stores | Objects, instance variables | Method calls, local variables |
| Size | Large | Small |
| Shared? | Shared across threads | Each thread has own stack |
| GC? | Yes — GC cleans heap | Auto cleaned when method ends |
| Error | OutOfMemoryError | StackOverflowError |

---

### 6. ClassLoader Types

```
Bootstrap ClassLoader    → loads core Java classes (java.lang.*)
     ↓
Extension ClassLoader    → loads ext/ folder classes
     ↓
Application ClassLoader  → loads your project classes
```

---

### 7. JIT Compiler — Why Java is Fast
- JVM first **interprets** bytecode line by line (slow)
- JIT detects **hot code** (frequently run methods)
- JIT compiles hot code directly to **machine code** (fast)
- Next time same code runs → uses machine code directly

---

### 8. Garbage Collection — Simple Flow
```
Object created → stored in Heap (Young Generation)
     ↓
Survives Minor GC → moves to Old Generation
     ↓
Old Generation full → Major GC runs
     ↓
Unreachable objects → deleted → memory freed
```

---

### 9. Interview One-Liner Answers

**Q: What is JVM?**
> JVM is a virtual machine that executes Java bytecode. In HP BCP, our Spring Boot app runs inside JVM on AWS EKS — JVM manages memory, threads, and garbage collection automatically.

**Q: Difference between JDK, JRE, JVM?**
> JDK = JRE + compiler tools (for developers)
> JRE = JVM + libraries (for running Java apps)
> JVM = engine that executes bytecode

**Q: What is WORA?**
> Write Once Run Anywhere — same .class file runs on any OS because each OS has its own JVM implementation.

**Q: What causes StackOverflowError?**
> Infinite recursion — method keeps calling itself, stack fills up and overflows.

**Q: What causes OutOfMemoryError?**
> Too many objects in Heap that GC cannot clean — usually a memory leak.

---

### 10. My Real Project Connection
- HP BCP runs on **AWS EKS** → each pod runs a JVM instance
- Spring Boot app → JVM manages thread pool for HTTP requests
- Kafka consumer threads → each runs in JVM thread with its own Stack
- PostgreSQL connection objects → stored in Heap, managed by HikariCP

---
