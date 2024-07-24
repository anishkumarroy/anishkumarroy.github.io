---
layout: note
title: Assembly Language
---

A basic assembly code - 
```nasm
<+0>:     endbr64 
<+4>:     push   rbp
<+5>:     mov    rbp,rsp
<+8>:     mov    DWORD PTR [rbp-0x4],edi
<+11>:    mov    QWORD PTR [rbp-0x10],rsi
<+15>:    mov    eax,0x30
<+20>:    pop    rbp
<+21>:    ret
```

**rbp - Base pointer register**  
- Helps create and maintain a stack frame for each function call. 
- A stack frame contains all the local variables and function parameters for a specific invocation of a function.

**rsp - Stack Pointer register**
- The RSP register always points to the top of the stack. 
- The stack grows downward in memory, so when data is pushed onto the stack, RSP is decremented
- When data is popped RSP is incremented.

**rdi - Designation Index Register**
- The RDI register is a general-purpose 64-bit register in the x86-64 architecture
- It is used to pass the first integer or pointer argument to a function.
- RDI can be used as a general-purpose register for arithmetic operations, data manipulation, and temporary storage.

**edi - Extended Destination Index**
- EDI is the lower 32 bits of RDI used for passing a 32-bit integer argument.

**rsi - Extended Source Index**
- RSI is the full 64-bit register used for passing an integer or pointer



### Visualization of the above Assembly code
`<+0>: endbr64`
- This instruction is part of Intel's Control-flow Enforcement Technology
```plaintext
+---------------------+
| Return Address      |
+---------------------+ <--- RSP
```

`<+4>: push rbp`
- This instruction saves the current base pointer (RBP) on the stack
```plaintext
+---------------------+
| Old RBP             |  <--- RSP
+---------------------+
| Return Address      |
+---------------------+
```

`<+5>: mov rbp, rsp`
- The stack pointer (RSP) is moved into the base pointer (RBP)
- It allows local variables to be referenced with a fixed offset from RBP.
```plaintext
+---------------------+
| Old RBP             |  <--- RBP
+---------------------+ <--- RSP
| Return Address      |
+---------------------+
```

`<+8>: mov DWORD PTR [rbp-0x4], edi`
- The value in EDI register (32-bit, typically used for the first integer argument)
- This is usually where the function's first parameter (passed in EDI) is stored in the stack frame.
```plaintext
+---------------------+
| Old RBP             |  <--- RBP
+---------------------+
| Return Address      |
+---------------------+
| int (from EDI)      |  <--- RBP-0x4
+---------------------+
```

`<+11>: mov QWORD PTR [rbp-0x10], rsi`
- The value in the RSI register (64-bit, typically used for the second integer or pointer argument) is moved into a local variable located at [rbp-0x10]
- This stores the second parameter passed to the function.
```plaintext
+---------------------+
| Old RBP             |  <--- RBP
+---------------------+
| Return Address      |
+---------------------+
| int (from EDI)      |  <--- RBP-0x4
+---------------------+
| long (from RSI)     |  <--- RBP-0x10
+---------------------+
```

`<+15>: mov eax, 0x30`
- This instruction doesn't affect the stack. It simply sets the register EAX to 0x30
- EAX is often used for the function return value.
```plaintext
+---------------------+
| Old RBP             |  <--- RBP
+---------------------+
| Return Address      |
+---------------------+
| int (from EDI)      |  <--- RBP-0x4
+---------------------+
| long (from RSI)     |  <--- RBP-0x10
+---------------------+
```

`<+20>: pop rbp`
- This instruction pops the top value from the stack (old RBP) back into the RBP register.
- It is a part of cleaning up the stack frame, before the function returns.
```plaintext
+---------------------+  <--- RSP
| Return Address      |
+---------------------+
| int (from EDI)      |  <--- RBP-0x4
+---------------------+
| long (from RSI)     |  <--- RBP-0x10
+---------------------+
```

`<+21>: ret`
- This instruction pops the return address off the stack and jumps to it.
- The stack pointer RSP is incremented by 8 bytes, effectively returning to the caller.
```plaintext
+---------------------+  <--- RSP
| int (from EDI)      |  <--- RBP-0x4
+---------------------+
| long (from RSI)     |  <--- RBP-0x10
+---------------------+
```

