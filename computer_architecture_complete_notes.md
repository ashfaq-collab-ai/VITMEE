# Computer Architecture — Complete Learning Notes

**Study guide compiled from the lessons in this chat**

This guide brings together the topics we studied, in their learning order. It is written as beginner-friendly revision material, with definitions, formulas, examples, comparisons, and practice questions.

---

## Contents

1. Introduction to Computer Architecture
2. Boolean Algebra
3. Computer Arithmetic
4. Flip-Flops
5. Combinational Circuits
6. Instruction Formats
7. Addressing Modes
8. Peripheral Devices and I/O
9. Memory Types and Organization
10. Interrupts and Exceptions
11. Von Neumann Architecture
12. System Bus
13. Instruction Cycle
14. Revision Questions
15. Suggested Next Topics

---

# 1. Introduction to Computer Architecture

## 1.1 What is computer architecture?

Computer architecture describes the design and functional behavior of a computer system. It explains the major components of a computer, how they communicate, and how instructions are executed.

A computer generally has four broad functions:

1. **Input** — receives data and instructions.
2. **Processing** — performs calculations and logical operations.
3. **Output** — presents the results.
4. **Storage** — stores data and programs.

Example: When you type two numbers into a calculator, the keyboard or touchscreen supplies input, the processor calculates the sum, and the display shows the result.

## 1.2 Main components

### CPU (Central Processing Unit)

The CPU executes program instructions. Its important parts include:

- **ALU (Arithmetic Logic Unit):** performs arithmetic operations such as addition and subtraction, and logical operations such as AND, OR, and comparisons.
- **Control Unit (CU):** coordinates the activities of the processor. It fetches and decodes instructions and directs the other components.
- **Registers:** very small, very fast storage locations inside the CPU. They temporarily hold data, instructions, addresses, and intermediate results.

### Memory

Memory stores programs and data.

- **RAM:** temporary, generally volatile working memory.
- **ROM:** non-volatile memory used for firmware and other persistent instructions.
- **Cache:** small, fast memory that keeps frequently or recently used data close to the CPU.
- **Secondary storage:** SSDs and hard drives store data persistently.

### Input and output devices

- Input: keyboard, mouse, scanner, microphone.
- Output: monitor, printer, speakers.
- Some devices, such as a touchscreen, can perform both input and output.

### System bus

A bus is a communication pathway connecting computer components. The three commonly described bus groups are:

- **Data bus:** carries data.
- **Address bus:** identifies the source or destination location.
- **Control bus:** carries control and timing signals.

## 1.3 Architecture vs. organization

| Computer architecture | Computer organization |
|---|---|
| Describes what the computer provides to a programmer | Describes how the computer is implemented |
| Includes instruction set, data types, and addressing modes | Includes control signals, memory technology, and hardware connections |
| More concerned with visible behavior | More concerned with internal implementation |

**Analogy:** Architecture is like the plan of what a building must provide; organization is how its materials and systems are arranged to make it work.

---

# 2. Boolean Algebra

## 2.1 What is Boolean algebra?

Boolean algebra is a mathematical system used to represent and simplify logic. A Boolean variable has only two possible values:

- `0` = false / LOW
- `1` = true / HIGH

It is the foundation of digital logic circuits.

## 2.2 Basic logic operations

Let `A` and `B` be Boolean inputs.

| A | B | AND (`A·B`) | OR (`A+B`) | XOR (`A⊕B`) |
|---:|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 1 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 |

NOT reverses the input:

| A | NOT A (`A'`) |
|---:|---:|
| 0 | 1 |
| 1 | 0 |

Other important operations:

- **NAND:** NOT of AND — `(A·B)'`
- **NOR:** NOT of OR — `(A+B)'`
- **XNOR:** NOT of XOR — `(A⊕B)'`; it is 1 when the inputs are equal.

| A | B | NAND | NOR | XNOR |
|---:|---:|---:|---:|---:|
| 0 | 0 | 1 | 1 | 1 |
| 0 | 1 | 1 | 0 | 0 |
| 1 | 0 | 1 | 0 | 0 |
| 1 | 1 | 0 | 0 | 1 |

## 2.3 Important Boolean laws

Here `+` means OR, `·` means AND, and `'` means NOT.

| Law | OR form | AND form |
|---|---|---|
| Identity | `A + 0 = A` | `A · 1 = A` |
| Null / domination | `A + 1 = 1` | `A · 0 = 0` |
| Idempotent | `A + A = A` | `A · A = A` |
| Complement | `A + A' = 1` | `A · A' = 0` |
| Commutative | `A+B = B+A` | `AB = BA` |
| Associative | `(A+B)+C = A+(B+C)` | `(AB)C = A(BC)` |
| Distributive | `A(B+C)=AB+AC` | `A+BC=(A+B)(A+C)` |

Double negation: `(A')' = A`.

## 2.4 De Morgan's theorems

1. `(A·B)' = A' + B'`
2. `(A+B)' = A'·B'`

These rules are useful when changing AND operations into OR operations, or vice versa, while applying inversion.

## 2.5 Simplification example

Simplify:

`Y = A + A·B`

Using the distributive law:

`Y = A(1+B)`

Since `1+B = 1`:

`Y = A·1 = A`

Therefore:

`Y = A`

Boolean simplification reduces the number of gates and can make a circuit smaller, faster, and cheaper.

## 2.6 Applications

Boolean algebra is used in logic gates, ALUs, control circuits, memory circuits, adders, and digital system design.

---

# 3. Computer Arithmetic

## 3.1 Why computer arithmetic matters

Computers represent numbers using binary digits (bits). The ALU performs arithmetic operations on these binary values.

## 3.2 Unsigned binary numbers

An unsigned number represents zero and positive values only.

For `n` bits, the range is:

`0` to `2^n − 1`

For 4 bits, the range is 0 to 15.

Example:

`1011₂ = 8 + 0 + 2 + 1 = 11₁₀`

## 3.3 Signed numbers and two's complement

A common representation of signed integers is **two's complement**.

For `n` bits, its range is:

`−2^(n−1)` to `2^(n−1) − 1`

For 4 bits, the range is −8 to +7.

To find the two's complement of a binary number:

1. Invert every bit.
2. Add 1.

Example: represent −5 using 4 bits.

- +5 = `0101`
- Invert = `1010`
- Add 1 = `1011`

So −5 is `1011` in 4-bit two's complement.

## 3.4 Binary addition

Basic rules:

| Operation | Result |
|---|---|
| `0 + 0` | `0` |
| `0 + 1` | `1` |
| `1 + 0` | `1` |
| `1 + 1` | `10` (write 0, carry 1) |

Example:

```text
   0101   (5)
 + 0011   (3)
 ------
   1000   (8)
```

## 3.5 Binary subtraction

Subtraction can be performed by adding the two's complement of the number being subtracted.

Example: `7 − 3` using 4 bits.

```text
  7 = 0111
  3 = 0011
 -3 = 1101  (two's complement of 0011)

  0111
+ 1101
------
1 0100
```

Discard the carry beyond the 4-bit result: `0100` = 4.

## 3.6 Binary multiplication

Binary multiplication follows the same shift-and-add idea as decimal multiplication.

Example:

```text
    101   (5)
  ×  10   (2)
  -----
    000
   101
  -----
   1010   (10)
```

## 3.7 Binary division

Binary long division follows the same general process as decimal long division: compare, subtract, and bring down the next bit.

Example: `1100₂ ÷ 10₂ = 110₂` (12 ÷ 2 = 6).

## 3.8 Overflow

Overflow occurs when a result is outside the range that can be represented with the available number of bits.

- **Unsigned addition:** a carry out of the most significant bit indicates overflow.
- **Signed two's-complement addition:** overflow occurs when two numbers with the same sign produce a result with the opposite sign.

Example with 4-bit signed numbers:

`0111 (+7) + 0001 (+1) = 1000`

The mathematical answer is +8, but 4-bit signed two's complement only represents −8 through +7. The result has overflowed.

## 3.9 Floating-point numbers and IEEE 754

Floating-point representation stores numbers with a sign, exponent, and fraction (significand).

IEEE 754 single precision uses 32 bits:

- 1 sign bit
- 8 exponent bits
- 23 fraction bits

It is used for values with fractional parts and a wide range of magnitudes. Floating-point calculations can have rounding errors because not every real number can be represented exactly in a finite number of bits.

## 3.10 BCD

**Binary-Coded Decimal (BCD)** represents each decimal digit separately using four bits.

Example: decimal 59:

- 5 = `0101`
- 9 = `1001`
- BCD = `0101 1001`

BCD is not the same as ordinary binary representation of the whole number.

---

# 4. Flip-Flops

## 4.1 What is a flip-flop?

A flip-flop is a sequential logic circuit that stores one bit of information. Unlike a combinational circuit, its output depends on its current inputs and its previous state.

Flip-flops are commonly controlled by a clock signal.

## 4.2 SR flip-flop

S means Set; R means Reset.

| S | R | Next state |
|---:|---:|---|
| 0 | 0 | Hold previous state |
| 0 | 1 | Reset (`Q=0`) |
| 1 | 0 | Set (`Q=1`) |
| 1 | 1 | Invalid / forbidden for the basic NOR-based SR implementation |

The exact forbidden condition depends on the SR circuit implementation.

## 4.3 JK flip-flop

The JK flip-flop improves on the SR design by defining the case where both inputs are 1.

| J | K | Next state |
|---:|---:|---|
| 0 | 0 | Hold |
| 0 | 1 | Reset |
| 1 | 0 | Set |
| 1 | 1 | Toggle |

Toggle means the output changes from 0 to 1 or from 1 to 0.

## 4.4 D flip-flop

D means Data or Delay. On the active clock edge, the D input is copied to the output.

`Q(next) = D`

| D | Next state |
|---:|---|
| 0 | 0 |
| 1 | 1 |

D flip-flops are widely used to build registers.

## 4.5 T flip-flop

T means Toggle.

| T | Next state |
|---:|---|
| 0 | Hold |
| 1 | Toggle |

T flip-flops are useful in counter designs.

## 4.6 Clock and timing terms

- **Clock:** a repeating timing signal used to coordinate sequential circuits.
- **Rising edge:** transition from 0 to 1.
- **Falling edge:** transition from 1 to 0.
- **Setup time:** minimum time the input must be stable before the active clock edge.
- **Hold time:** minimum time the input must remain stable after the active clock edge.
- **Propagation delay:** time taken for a change at the input/clock to affect the output.

## 4.7 Latch vs. flip-flop

| Latch | Flip-flop |
|---|---|
| Usually level-sensitive | Usually edge-triggered |
| Responds while enable is active | Responds at a clock edge |
| Can be simpler | Common in clocked synchronous designs |

## 4.8 Applications

Flip-flops are building blocks for registers, counters, shift registers, and state machines. Collections of storage elements are used throughout processor and memory systems.

---

# 5. Combinational Circuits

## 5.1 What is a combinational circuit?

A combinational circuit produces outputs based only on its current inputs. It does not store a previous state.

Examples include adders, subtractors, multiplexers, decoders, and comparators.

| Combinational circuit | Sequential circuit |
|---|---|
| Output depends on current inputs | Output depends on current inputs and stored state |
| No memory element is required | Uses memory elements such as flip-flops |
| Example: adder | Example: counter |

## 5.2 Half adder

A half adder adds two one-bit inputs, A and B.

- Sum: `S = A ⊕ B`
- Carry: `C = A · B`

| A | B | Sum | Carry |
|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

A half adder has no carry-in input.

## 5.3 Full adder

A full adder adds A, B, and carry-in (`Cin`).

- `Sum = A ⊕ B ⊕ Cin`
- `Cout = AB + ACin + BCin`

| A | B | Cin | Sum | Cout |
|---:|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

Multiple full adders can be connected to add multi-bit numbers.

## 5.4 Half subtractor

A half subtractor calculates `A − B`.

- Difference: `D = A ⊕ B`
- Borrow: `Borrow = A' · B`

| A | B | Difference | Borrow |
|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 |

## 5.5 Full subtractor

A full subtractor calculates `A − B − Bin`, where `Bin` is borrow-in.

- `Difference = A ⊕ B ⊕ Bin`
- `Bout = A'B + A'Bin + B·Bin`

| A | B | Bin | Difference | Bout |
|---:|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 1 |
| 0 | 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 0 |
| 1 | 1 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 | 1 |

## 5.6 Multiplexer (MUX)

A multiplexer selects one of several inputs and sends the selected input to one output. It is often called a data selector.

For a 2-to-1 MUX:

- `S=0` selects `I0`.
- `S=1` selects `I1`.
- Equation: `Y = S'·I0 + S·I1`

| S | Output Y |
|---:|---|
| 0 | I0 |
| 1 | I1 |

A 4-to-1 MUX has four data inputs and two select bits.

## 5.7 Demultiplexer (DEMUX)

A demultiplexer takes one input and routes it to one of several outputs according to select lines. It is often called a data distributor.

For a 1-to-2 DEMUX:

- `S=0`: input goes to `Y0`.
- `S=1`: input goes to `Y1`.

## 5.8 Encoder and decoder

- **Encoder:** converts one active input among many into a coded binary output. A priority encoder resolves situations where more than one input is active by giving one input priority.
- **Decoder:** converts an input code into one of several output lines. For example, a 2-to-4 decoder has 2 input bits and 4 possible output lines.

## 5.9 Comparator

A digital comparator compares two binary values and indicates whether:

- `A > B`
- `A = B`
- `A < B`

## 5.10 Applications

Combinational circuits are used in ALUs, arithmetic units, data selection, address decoding, control logic, and digital communication systems.

---

# 6. Instruction Formats

## 6.1 What is an instruction?

A machine instruction is a binary-coded command that tells the CPU to perform an operation.

An instruction commonly contains fields such as:

- **Opcode:** specifies the operation, such as ADD, SUB, LOAD, or STORE.
- **Register field(s):** identify registers used by the instruction.
- **Addressing-mode field:** indicates how to locate an operand.
- **Operand/address field(s):** provide data or identify where data can be found.

The exact fields depend on the processor's instruction set architecture (ISA).

## 6.2 Number of address fields

Instructions are often described by the number of explicit address/operand fields.

### Three-address instruction

Example:

`ADD R1, R2, R3`

Meaning: `R1 = R2 + R3`

### Two-address instruction

Example:

`ADD R1, R2`

Meaning: `R1 = R1 + R2`

### One-address instruction

Often uses an implied accumulator.

Example:

```text
LOAD A
ADD B
STORE X
```

Meaning: load A into the accumulator, add B, then store the result in X.

### Zero-address instruction

Common in stack-based instruction designs. Operands are implied to be on the stack.

Example:

```text
PUSH A
PUSH B
ADD
POP X
```

The ADD operation takes its operands from the stack.

## 6.3 Example expression

For:

`X = (A + B) × (C − D)`

A three-address-style sequence might be:

```text
ADD R1, A, B
SUB R2, C, D
MUL X, R1, R2
```

The exact syntax depends on the ISA.

## 6.4 Fixed-length vs. variable-length instructions

| Fixed-length | Variable-length |
|---|---|
| Every instruction has the same length | Instructions can have different lengths |
| Often simpler to fetch and decode | Can use code space more flexibly |
| Common in many RISC designs | Common in many CISC designs |

RISC and CISC are broad design traditions; real processors can combine ideas from both.

---

# 7. Addressing Modes

## 7.1 What is an addressing mode?

An addressing mode specifies how an instruction identifies or obtains its operand.

An operand may be inside the instruction, in a register, or in memory.

## 7.2 Immediate addressing

The value is included directly in the instruction.

Example:

`MOV R1, #10`

Meaning: put the value 10 into R1.

## 7.3 Direct addressing

The instruction contains the memory address of the operand.

Example:

`LOAD R1, [1000]`

Meaning: load into R1 the value stored at memory address 1000.

## 7.4 Indirect addressing

The instruction identifies a location that contains the effective address of the operand.

Conceptual example:

- Memory location 1000 contains 2000.
- The operand is stored at memory location 2000.

## 7.5 Register addressing

The operand is in a CPU register.

Example:

`ADD R1, R2`

## 7.6 Register-indirect addressing

A register contains the memory address of the operand.

Example:

`LOAD R1, [R2]`

Meaning: use the address stored in R2 to find the value to load.

## 7.7 Indexed addressing

An index value is added to a base address to calculate the effective address.

`Effective address = Base address + Index`

Useful for arrays.

## 7.8 Relative addressing

The effective address is calculated relative to the program counter.

`Effective address = PC + displacement`

Useful for branches and position-independent code.

## 7.9 Base-register addressing

A base register holds a starting address, and an offset is added.

`Effective address = Base register + offset`

## 7.10 Quick comparison

| Mode | Where the operand is / how it is found |
|---|---|
| Immediate | Value is in the instruction |
| Direct | Address is in the instruction |
| Indirect | Address is found through another memory location |
| Register | Value is in a register |
| Register-indirect | Register contains a memory address |
| Indexed | Base/address plus index |
| Relative | PC plus displacement |
| Base-register | Base register plus offset |

---

# 8. Peripheral Devices and I/O

## 8.1 What are peripheral devices?

Peripheral devices are hardware components that allow a computer to communicate with the outside world or store information beyond its main memory.

### Input devices

Keyboard, mouse, scanner, microphone, camera.

### Output devices

Monitor, printer, speakers, projector.

### Storage devices

Hard disk drive (HDD), solid-state drive (SSD), USB flash drive, memory card.

### Communication devices

Network interface card (NIC), Wi-Fi adapter, Bluetooth adapter, modem.

## 8.2 I/O controllers

An I/O controller or device controller manages communication between the CPU/memory and a peripheral device. It may contain control, status, and data registers and may handle device-specific details.

## 8.3 Programmed I/O (polling)

The CPU repeatedly checks a device's status until it is ready.

- Simple to implement.
- Can waste CPU time while waiting.

## 8.4 Interrupt-driven I/O

The CPU starts an I/O operation and continues other work. The device sends an interrupt when it needs attention or has completed an operation.

- Avoids continuous checking.
- Requires interrupt-handling support.

## 8.5 Direct Memory Access (DMA)

DMA allows a controller to transfer a block of data between a device and main memory with limited CPU involvement in the individual data transfers.

Typical sequence:

1. CPU configures the DMA transfer.
2. DMA controller performs the transfer.
3. DMA controller notifies the CPU, often using an interrupt.

## 8.6 I/O addressing

- **Memory-mapped I/O:** device registers occupy addresses in the memory address space and are accessed using memory operations.
- **Port-mapped (isolated) I/O:** a separate I/O address space and special I/O instructions are used on architectures that support it.

## 8.7 HDD vs. SSD

| HDD | SSD |
|---|---|
| Uses spinning magnetic platters and moving parts | Uses flash memory; no moving parts |
| Usually slower for random access | Usually much faster for random access |
| Often lower cost per unit of capacity | Often higher cost per unit of capacity |
| More sensitive to physical shock | Generally more shock-resistant |

---

# 9. Memory Types and Organization

## 9.1 What is memory?

Memory stores instructions and data required by a computer. Different types are designed to balance speed, capacity, cost, and persistence.

## 9.2 Memory hierarchy

A simplified hierarchy from fastest/smallest to slowest/largest:

1. CPU registers
2. Cache
3. Main memory (RAM)
4. Secondary storage (SSD/HDD)

Generally, faster memory is more expensive per bit and has lower capacity.

## 9.3 RAM

RAM is read/write working memory. It is generally volatile: its contents are lost when power is removed.

### SRAM

- Stores bits using flip-flop-like circuits.
- Very fast and does not need periodic refresh while powered.
- More expensive and less dense than DRAM.
- Commonly used for cache.

### DRAM

- Stores bits using capacitors and transistors.
- Requires periodic refreshing.
- Denser and usually less expensive per bit than SRAM.
- Commonly used for main memory.

## 9.4 ROM and related types

ROM is non-volatile memory used for persistent information such as firmware.

Common terms:

- **PROM:** programmable once.
- **EPROM:** can be erased using ultraviolet light and reprogrammed.
- **EEPROM:** electrically erasable and reprogrammable.
- **Flash memory:** a widely used form of electrically erasable non-volatile memory, typically erased in blocks.

## 9.5 Cache memory

Cache is small, fast memory that keeps copies of data/instructions likely to be used soon.

Common levels:

- **L1:** typically smallest and fastest; often private to a core.
- **L2:** usually larger than L1.
- **L3:** often larger and may be shared among cores.

Cache works well because programs often exhibit:

- **Temporal locality:** recently used data/instructions may be used again soon.
- **Spatial locality:** nearby memory locations may be used soon.

A cache hit means the requested data is found in cache. A cache miss means it must be fetched from a lower level.

## 9.6 Capacity and address lines

If a memory has `n` address bits, it can identify `2^n` distinct addresses.

Example: 10 address bits can identify `2^10 = 1024` locations.

If each location stores 1 byte, the capacity is 1024 bytes (1 KiB).

## 9.7 Read and write

- **Read:** retrieve data from a memory location.
- **Write:** store data at a memory location.

Address lines identify a location; data lines carry the value being read or written; control signals specify the operation.

## 9.8 Byte-addressable and word-addressable memory

- **Byte-addressable:** each address identifies one byte.
- **Word-addressable:** each address identifies one word, where a word is the processor/memory system's defined unit of data.

## 9.9 Endianness

Endianness describes the order in which bytes of a multi-byte value are stored.

For the 32-bit value `0x12345678`:

- **Big-endian:** lowest address stores `0x12`, then `0x34`, `0x56`, `0x78`.
- **Little-endian:** lowest address stores `0x78`, then `0x56`, `0x34`, `0x12`.

Endianness affects data representation and interchange between systems.

## 9.10 Virtual memory and page faults

Virtual memory gives each process a virtual address space. The operating system and hardware translate virtual addresses to physical memory addresses.

Memory is commonly managed in fixed-size blocks called pages.

A **page fault** occurs when a process accesses a virtual page that is not currently mapped as required in physical memory. The operating system handles the fault; this may involve loading the page from storage or reporting an invalid access.

## 9.11 Memory interleaving

Memory interleaving spreads consecutive addresses across multiple memory banks. This can allow overlapping or parallel memory operations and improve throughput, depending on the system.

---

# 10. Interrupts and Exceptions

## 10.1 What is an interrupt?

An interrupt is a signal or event that causes the CPU to pause its normal instruction flow and execute a handler to deal with the event.

Examples include a keyboard event, a timer event, or a device completing an I/O operation.

## 10.2 Interrupt Service Routine (ISR)

An ISR is the routine that handles an interrupt.

A simplified sequence:

1. CPU receives an interrupt.
2. CPU completes or safely suspends the current instruction, according to the architecture.
3. CPU saves the necessary execution state.
4. CPU transfers control to the appropriate handler.
5. Handler services the event.
6. CPU restores state and resumes execution, if appropriate.

## 10.3 Hardware and software interrupts

- **Hardware interrupt:** generated by an external hardware device or controller.
- **Software interrupt:** deliberately generated by an instruction or software mechanism, often to request a service.

## 10.4 Maskable and non-maskable interrupts

- **Maskable interrupt:** can be disabled or deferred by the processor's interrupt-control mechanism.
- **Non-maskable interrupt (NMI):** intended for important events that should not be blocked by the ordinary interrupt mask.

## 10.5 Exceptions

An exception is an event detected during instruction execution.

Common classifications include:

- **Fault:** often reported before the instruction completes; some faults allow the instruction to be retried after handling.
- **Trap:** often reported after the instruction completes; can be used for debugging or system services.
- **Abort:** severe error where reliable restart may not be possible.

Exact definitions and behavior depend on the processor architecture.

## 10.6 Interrupt vector and priority

An **interrupt vector table (IVT)** or equivalent structure maps interrupt/exception identifiers to handler locations.

An interrupt controller can help manage requests, priorities, and delivery to the CPU.

---

# 11. Von Neumann Architecture

## 11.1 What is Von Neumann architecture?

Von Neumann architecture is a computer design model in which program instructions and data share the same main memory and commonly share communication paths to the processor.

Main components:

- CPU
- Main memory
- Input devices
- Output devices
- System interconnection/bus

## 11.2 Fetch-decode-execute cycle

A simplified instruction-processing cycle:

1. **Fetch:** retrieve the next instruction from memory.
2. **Decode:** interpret the instruction and determine what it requires.
3. **Execute:** perform the operation.
4. **Store/write back:** save the result if required.
5. **Repeat:** continue with the next instruction.

## 11.3 Von Neumann bottleneck

Because instructions and data use shared memory pathways in the classic model, they compete for transfer capacity. This can limit performance and is known as the Von Neumann bottleneck.

Modern systems use techniques such as caches, prefetching, wider transfers, and separate instruction/data caches to reduce the impact.

## 11.4 Von Neumann vs. Harvard architecture

| Von Neumann | Harvard |
|---|---|
| Instructions and data share a memory space in the classic model | Instructions and data use separate memory spaces |
| Often share the same pathway | Can use separate instruction and data pathways |
| Simpler unified memory model | Can allow simultaneous instruction and data access |
| Common general-purpose model | Common in some microcontrollers and digital signal processors |

Modern processors may use a modified Harvard design internally while presenting a unified memory model to software.

---

# 12. System Bus

## 12.1 What is a system bus?

A system bus is a set of communication pathways that transfers information among the CPU, memory, and I/O components.

## 12.2 Three main bus groups

### Data bus

Carries the actual data and instruction values.

- A wider data bus can transfer more bits in one transfer.
- Example: a 64-bit data path can carry 64 bits per transfer.

### Address bus

Carries the address of the memory location or I/O location being accessed.

- If an address bus has `n` bits, it can represent `2^n` distinct addresses.
- Example: 16 address bits can represent 65,536 distinct addresses.

### Control bus

Carries signals that coordinate operations, such as read, write, interrupt, clock, and bus-request/grant signals. The exact signals depend on the design.

## 12.3 Bus width

Bus width is the number of bits that can be carried in parallel on a bus.

- Data-bus width affects the amount of data transferred per transaction.
- Address-bus width affects the number of uniquely addressable locations, subject to the system's addressing design.

## 12.4 Synchronous vs. asynchronous buses

| Synchronous bus | Asynchronous bus |
|---|---|
| Uses a shared clock for timing | Uses handshaking/control signals rather than relying on one shared bus clock |
| Timing can be predictable | Can accommodate devices with different response times |
| Requires timing compatibility | Handshaking adds control complexity |

## 12.5 Internal and external buses

- **Internal interconnects:** connect components within a CPU or system-on-chip.
- **External/system interconnects:** connect the processor or chipset to memory and peripheral interfaces.

Real computer systems often use multiple specialized interconnects rather than one single bus for everything.

---

# 13. Instruction Cycle

## 13.1 What is the instruction cycle?

The instruction cycle is the sequence of steps the CPU follows to process an instruction. It is often described as fetch, decode, execute, and write back, with additional steps depending on the instruction and architecture.

## 13.2 Important registers

- **Program Counter (PC):** holds the address of the next instruction to fetch.
- **Instruction Register (IR):** holds the instruction currently being decoded/executed.
- **Memory Address Register (MAR):** holds a memory address for a memory operation in architectures that use this register model.
- **Memory Data Register (MDR):** holds data being transferred to or from memory in architectures that use this register model.
- **General-purpose registers:** hold operands and results.
- **Status/flag register:** holds condition flags such as zero, carry, sign, and overflow, depending on the CPU.

These names describe a common teaching model; not every modern processor exposes or implements every register in exactly this way.

## 13.3 Fetch phase

A simplified conceptual sequence:

1. PC contains the address of the next instruction.
2. The CPU sends that address to memory.
3. The instruction is read from memory.
4. The instruction is placed in the IR.
5. PC is advanced to the next instruction, unless the architecture handles this differently.

## 13.4 Decode phase

The control unit interprets the opcode and fields. It determines the operation, required operands, addressing mode, and resources needed.

## 13.5 Execute phase

The CPU performs the required action. Examples:

- ALU adds two values.
- A load instruction reads memory.
- A store instruction writes memory.
- A branch instruction changes the PC if its condition is satisfied.

## 13.6 Write-back phase

If the instruction produces a result that must be saved, the result is written to a register or memory location.

Not every instruction needs a separate write-back phase.

## 13.7 Example: adding two values

Suppose the instruction is conceptually:

`ADD R1, R2, R3`

Meaning: `R1 = R2 + R3`.

1. Fetch the ADD instruction.
2. Decode the opcode and register fields.
3. Read values from R2 and R3.
4. ALU adds the values.
5. Write the result into R1.
6. Update relevant status flags if the architecture requires it.

## 13.8 Branches and interrupts

A branch can change the next instruction address in the PC. An interrupt or exception can temporarily transfer control to a handler, after which execution may resume.

---

# 14. Revision Questions

Try answering these without looking at the notes.

## Introduction
1. What is computer architecture?
2. What are the three major parts of a CPU discussed in this guide?
3. What is the difference between computer architecture and computer organization?
4. What are the roles of the data, address, and control buses?

## Boolean algebra
5. What is the output of `1 AND 0`?
6. What is the output of `1 XOR 1`?
7. State both De Morgan's theorems.
8. Simplify `A + AB`.

## Computer arithmetic
9. What is the unsigned range of an n-bit number?
10. What is the 4-bit two's-complement representation of −5?
11. What is overflow?
12. How many bits are used for the exponent in IEEE 754 single precision?

## Sequential and combinational circuits
13. What does a D flip-flop store?
14. What happens in a JK flip-flop when J=1 and K=1?
15. What is the main difference between combinational and sequential circuits?
16. Write the sum and carry equations of a half adder.
17. What is the purpose of a multiplexer?

## Instructions and addressing
18. What is an opcode?
19. How does immediate addressing differ from direct addressing?
20. What does a program counter store?

## Memory and I/O
21. What is the difference between SRAM and DRAM?
22. What is cache memory used for?
23. What is a page fault?
24. What is the difference between polling and interrupt-driven I/O?
25. What is DMA?

## Processor operation
26. What is the Von Neumann bottleneck?
27. What are the fetch, decode, and execute stages?
28. What is an ISR?
29. What is the difference between an interrupt and an exception?
30. What is the difference between synchronous and asynchronous buses?

---

# 15. Suggested Next Topics

The topics below are natural next steps after the lessons in this chat:

1. **Registers:** register organization, general-purpose registers, special-purpose registers, and register transfer operations.
2. **Counters:** asynchronous and synchronous counters, up/down counters, and applications.
3. **CPU organization:** ALU, control unit, datapath, and register organization in more detail.
4. **Control unit:** hardwired control and microprogrammed control.
5. **I/O organization:** polling, interrupts, DMA, and I/O interfaces in greater depth.
6. **Cache organization:** cache mapping, replacement, and write policies.
7. **Pipelining:** instruction pipelines, hazards, and performance.
8. **RISC vs. CISC:** instruction-set design and implementation trade-offs.
9. **Multiprocessors and multicore systems:** parallel processing and cache coherence basics.

---

## Quick formula sheet

| Topic | Formula / rule |
|---|---|
| n-bit unsigned range | `0` to `2^n − 1` |
| n-bit two's-complement range | `−2^(n−1)` to `2^(n−1) − 1` |
| Two's complement | Invert bits, then add 1 |
| Half-adder sum | `S = A ⊕ B` |
| Half-adder carry | `C = A·B` |
| Full-adder sum | `S = A ⊕ B ⊕ Cin` |
| Full-adder carry | `Cout = AB + ACin + BCin` |
| Half-subtractor difference | `D = A ⊕ B` |
| Half-subtractor borrow | `Borrow = A'·B` |
| 2:1 MUX | `Y = S'·I0 + S·I1` |
| Number of addresses with n address bits | `2^n` |
| Indexed effective address | `Base address + Index` |
| Relative effective address | `PC + displacement` |
| Base-register effective address | `Base register + offset` |

**End of study guide.**
