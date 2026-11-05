# Computer Architecture -- Learning Notes

These notes compile the topics covered in this chat: 1. Sequential
Circuits 2. Instruction Formats 3. Addressing Modes 4. Peripheral
Devices

------------------------------------------------------------------------

# 1. Sequential Circuits in Computer Architecture

## 1.1 What is a Sequential Circuit?

A **sequential circuit** is a digital circuit whose output depends on: -
Current inputs - Previous state (stored information)

Unlike a combinational circuit, a sequential circuit can remember
information.

Examples include registers, counters, shift registers, and control
circuits.

### Combinational vs. Sequential Circuits

  -----------------------------------------------------------------------
  Feature                 Combinational circuit   Sequential circuit
  ----------------------- ----------------------- -----------------------
  Output depends on       Current inputs          Current inputs and
                                                  previous state

  Memory                  No                      Yes

  Feedback                Generally no            Often present

  Clock                   Not required            Required for
                                                  synchronous circuits

  Examples                Adder, MUX, decoder     Register, counter,
                                                  flip-flop
  -----------------------------------------------------------------------

## 1.2 Main Components

A sequential circuit generally contains: - **Combinational logic:**
Performs logical operations. - **Memory elements:** Store previous
information. - **Feedback path:** Returns stored information to the
circuit. - **Clock:** Controls when state changes in synchronous
circuits.

## 1.3 Types of Sequential Circuits

### Synchronous Sequential Circuits

These use a clock signal to control when the circuit's state changes.

Examples: - Registers - Synchronous counters - CPU state registers

### Asynchronous Sequential Circuits

These do not use a common clock to control every state change. Their
state can change in response to input changes.

Examples: - Asynchronous counters - Certain control and handshake
circuits

Synchronous circuits are widely used in processors because clock-based
timing helps coordinate operations.

## 1.4 Clock Signal

A clock is a periodic digital signal that alternates between 0 and 1.

Important transitions: - **Rising edge:** Changes from 0 to 1. -
**Falling edge:** Changes from 1 to 0.

A flip-flop may store data on a rising edge or falling edge, depending
on its design.

Example of a rising-edge-triggered D flip-flop:

  Clock event       D input   Stored value Q
  --------------- --------- ----------------
  Rising edge 1           1                1
  Rising edge 2           0                0
  Rising edge 3           1                1

## 1.5 Flip-Flops

A **flip-flop** is a digital memory element that stores exactly one bit
(0 or 1).

### SR Flip-Flop

SR means Set-Reset.

    S   R Next state Q
  --- --- ------------------------------------------------
    0   0 No change
    0   1 0 (Reset)
    1   0 1 (Set)
    1   1 Invalid for the basic active-high SR flip-flop

### JK Flip-Flop

    J   K Next state Q
  --- --- --------------
    0   0 No change
    0   1 0 (Reset)
    1   0 1 (Set)
    1   1 Toggle

### D Flip-Flop

At the active clock edge, the output takes the value of D.

    D   Next state Q
  --- --------------
    0              0
    1              1

D flip-flops are commonly used in registers.

### T Flip-Flop

    T Next state Q
  --- --------------
    0 No change
    1 Toggle

T flip-flops are useful in counter circuits.

## 1.6 Registers

A **register** is a group of flip-flops used to store multiple bits.

For example, a 4-bit register contains four flip-flops and can store
`1011`.

  Flip-flop     Stored bit
  ----------- ------------
  FF3                    1
  FF2                    0
  FF1                    1
  FF0                    1

CPU registers temporarily store data, addresses, instructions, and
intermediate results.

Examples: - General-purpose registers - Program Counter (PC) -
Instruction Register (IR) - Memory Address Register (MAR) - Memory Data
Register (MDR)

## 1.7 Counters

A **counter** is a sequential circuit that counts clock pulses.

A 3-bit binary up-counter counts from 0 to 7:

    Clock pulse   Q2   Q1   Q0   Decimal
  ------------- ---- ---- ---- ---------
        Initial    0    0    0         0
              1    0    0    1         1
              2    0    1    0         2
              3    0    1    1         3
              4    1    0    0         4
              5    1    0    1         5
              6    1    1    0         6
              7    1    1    1         7
              8    0    0    0         0

Number of states in an n-bit binary counter:

`Number of states = 2^n`

Examples: - 1-bit: 2 states - 2-bit: 4 states - 3-bit: 8 states - 4-bit:
16 states

Types: - **Up counter:** Counts upward. - **Down counter:** Counts
downward. - **Up/down counter:** Counts in either direction. - **Ring
counter:** A shift register whose last output feeds the first input. -
**Johnson counter:** A shift register whose inverted last output feeds
the first input.

## 1.8 Shift Registers

A **shift register** stores binary data and shifts it left or right when
clock pulses arrive.

Types:

  -----------------------------------------------------------------------
  Type                    Full form               Purpose
  ----------------------- ----------------------- -----------------------
  SISO                    Serial In Serial Out    Data enters and leaves
                                                  one bit at a time

  SIPO                    Serial In Parallel Out  Serial input,
                                                  multiple-bit output

  PISO                    Parallel In Serial Out  Multiple-bit input,
                                                  serial output

  PIPO                    Parallel In Parallel    Multiple-bit input and
                          Out                     output
  -----------------------------------------------------------------------

Applications: - Serial-to-parallel conversion - Parallel-to-serial
conversion - Temporary data storage - Digital communication

## 1.9 Finite State Machines (FSM)

A **Finite State Machine** is a sequential system that moves among a
limited number of states according to its inputs and current state.

Example: A vending machine may have these states: 1. `IDLE` -- Waiting
for money. 2. `MONEY_RECEIVED` -- Money has been inserted. 3. `DISPENSE`
-- Product is delivered.

Two important FSM types:

-   **Moore machine:** Output depends only on the current state.
-   **Mealy machine:** Output depends on the current state and current
    inputs.

FSMs are used in processor control units, traffic-light controllers, and
digital devices.

## 1.10 Timing Concepts

-   **Setup time:** Minimum time input data must remain stable before
    the active clock edge.
-   **Hold time:** Minimum time input data must remain stable after the
    active clock edge.
-   **Propagation delay:** Time taken for a change at an input or clock
    to produce a corresponding output change.
-   **Metastability:** An unpredictable state that can occur when timing
    requirements are violated.

## 1.11 Applications in Computer Architecture

  -----------------------------------------------------------------------
  Component                           Purpose
  ----------------------------------- -----------------------------------
  CPU registers                       Temporarily store data

  Program Counter (PC)                Stores the address of the next
                                      instruction

  Instruction Register (IR)           Stores the current instruction

  Counters                            Count clock pulses or events

  Control Unit                        Controls instruction execution
                                      using control logic and state

  Memory elements                     Store binary information

  Shift registers                     Move data between positions
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 2. Instruction Formats in Computer Architecture

## 2.1 What is an Instruction?

An **instruction** is a command given to the CPU to perform an
operation.

Examples:

``` asm
ADD R1, R2
SUB R1, R2
MOV R1, R2
LOAD R1, 1000
STORE R1, 2000
```

An instruction is represented internally as a sequence of binary bits.

## 2.2 What is an Instruction Format?

An **instruction format** defines how the bits of a machine instruction
are arranged.

A typical instruction may contain:

  Field             Meaning
  ----------------- ---------------------------------------------
  Opcode            Specifies the operation, such as ADD or SUB
  Operand           Specifies data or its location
  Register field    Identifies a CPU register
  Addressing mode   Specifies how to locate the operand

Illustrative 16-bit format:

``` text
| Opcode | Register | Address |
| 4 bits | 4 bits  | 8 bits  |
```

Actual formats vary by processor architecture.

## 2.3 Opcode

The **opcode (operation code)** tells the CPU which operation to
perform.

Examples: - `ADD` -- Addition - `SUB` -- Subtraction - `MUL` --
Multiplication - `DIV` -- Division - `LOAD` -- Load data into a
register - `STORE` -- Store data into memory - `MOV` -- Move data -
`JMP` -- Jump to another instruction

For `ADD R1, R2`, `ADD` is the operation and `R1`, `R2` identify
operands.

## 2.4 Instruction Formats Based on Number of Address Fields

A common classification is based on the number of explicit address or
operand fields.

### A. Three-Address Instruction

Contains three explicit fields: destination and two source operands.

Example:

``` asm
ADD R1, R2, R3
```

Meaning:

``` text
R1 = R2 + R3
```

If R2 = 10 and R3 = 20, then R1 = 30.

Advantages: - Source operands are preserved. - Complex calculations may
require fewer instructions.

Disadvantage: - More bits are needed for the address fields.

### B. Two-Address Instruction

Contains two explicit address fields. One operand is commonly both a
source and destination.

Example:

``` asm
ADD R1, R2
```

Meaning:

``` text
R1 = R1 + R2
```

If R1 = 10 and R2 = 20, then R1 becomes 30.

Advantages: - Shorter than a comparable three-address instruction.

Disadvantage: - The original destination operand is overwritten.

### C. One-Address Instruction

Contains one explicit address field and commonly uses an implicit
accumulator (AC).

Example:

``` asm
LOAD A
ADD B
STORE C
```

If A = 10 and B = 20:

  Instruction     Accumulator
  ------------- -------------
  LOAD A                   10
  ADD B                    30
  STORE C                  30

Meaning: - `AC = A` - `AC = AC + B` - `C = AC`

Advantages: - Short instruction format. - Simple hardware design.

Disadvantages: - Operations depend heavily on the accumulator. - Complex
calculations may require more instructions.

### D. Zero-Address Instruction

Contains no explicit operand address fields. It is commonly associated
with stack-based machines.

Example:

``` asm
PUSH 10
PUSH 20
ADD
```

The stack is LIFO (Last In, First Out). `ADD` takes the top two values,
adds them, and pushes the result.

  Step      Stack (top first)
  --------- -------------------
  PUSH 10   10
  PUSH 20   20, 10
  ADD       30

Advantages: - Arithmetic instructions can be very short. - Operands are
implicit.

Disadvantages: - Requires stack operations. - Direct access to
particular operands is less flexible.

### Comparison

  Format            Explicit address fields Example
  --------------- ------------------------- ------------------
  Three-address                           3 `ADD R1, R2, R3`
  Two-address                             2 `ADD R1, R2`
  One-address                             1 `ADD B`
  Zero-address                            0 `ADD`

The number refers to explicit address/operand fields, not necessarily
the number of values involved in the operation.

## 2.5 Same Expression in Different Formats

Expression:

`X = (A + B) * (C - D)`

### Three-address format

``` asm
ADD R1, A, B
SUB R2, C, D
MUL X, R1, R2
```

### Two-address format

``` asm
MOV R1, A
ADD R1, B
MOV R2, C
SUB R2, D
MUL R1, R2
MOV X, R1
```

### One-address format

``` asm
LOAD A
ADD B
STORE TEMP
LOAD C
SUB D
MUL TEMP
STORE X
```

### Zero-address format

``` asm
PUSH A
PUSH B
ADD
PUSH C
PUSH D
SUB
MUL
POP X
```

The same calculation can be performed using different instruction
formats, but the number and style of instructions change.

## 2.6 Fixed-Length and Variable-Length Formats

### Fixed-Length

Every instruction has the same number of bits.

Example: A hypothetical architecture in which every instruction is 32
bits.

Advantages: - Easier decoding - Simpler instruction alignment -
Predictable instruction size

Disadvantage: - Some instructions may use bits inefficiently.

Many RISC architectures use regular instruction lengths. RISC-V's base
instruction formats use 32-bit instructions, with an optional compressed
16-bit extension.

### Variable-Length

Instructions can have different lengths.

Example:

``` text
Instruction 1:  8 bits
Instruction 2: 16 bits
Instruction 3: 32 bits
Instruction 4: 48 bits
```

Advantages: - Can save memory space. - Allows different amounts of
information in instructions.

Disadvantages: - Decoding is more complex. - Finding the next
instruction boundary can be harder.

Example: x86 uses variable-length instructions.

## 2.7 RISC vs. CISC

  -----------------------------------------------------------------------
  Feature                 RISC                    CISC
  ----------------------- ----------------------- -----------------------
  Full form               Reduced Instruction Set Complex Instruction Set
                          Computer                Computer

  Instruction format      Often simpler and more  Often more complex
                          regular                 

  Instruction length      Often fixed or a        Often variable
                          limited set of lengths  

  Instructions            Generally simpler       Can support complex
                          operations              operations

  Examples                ARM, RISC-V             x86
  -----------------------------------------------------------------------

These are common design tendencies, not absolute rules. RISC does not
always mean fixed-length, and CISC does not always mean variable-length.

## 2.8 Why Instruction Formats Matter

Instruction formats help the CPU: 1. Identify the operation using the
opcode. 2. Identify source operands. 3. Identify the destination. 4.
Determine how operands should be accessed. 5. Decode and execute the
instruction.

Example:

``` asm
ADD R1, R2, R3
```

The CPU decodes the instruction, reads R2 and R3, performs addition in
the ALU, and writes the result into R1.

------------------------------------------------------------------------

# 3. Addressing Modes in Computer Architecture

## 3.1 What is an Addressing Mode?

An **addressing mode** is the method used by the CPU to identify the
location of an operand required by an instruction.

Addressing modes tell the CPU where to find the data needed to execute
an instruction.

For example:

``` asm
ADD R1, R2
LOAD R1, 500
```

The CPU must determine whether a number is an actual value, a register,
or a memory address.

## 3.2 Important Terms

-   **Operand:** Data on which an operation is performed.
-   **Effective Address (EA):** The actual memory address where an
    operand is located.
-   **Memory address:** A location in memory where data is stored.

Example memory:

  Memory address     Value
  ---------------- -------
  100                   25
  200                   50
  300                   75

## 3.3 Common Addressing Modes

### A. Immediate Addressing

The actual data is provided directly inside the instruction.

Example:

``` asm
MOV R1, #50
```

Meaning:

``` text
R1 = 50
```

Advantages: - Fast operand access. - Simple execution.

Disadvantage: - Constant value is limited by the bits available in the
instruction.

### B. Direct Addressing

The instruction contains the memory address where the operand is stored.

Example:

``` asm
LOAD R1, 500
```

If `Memory[500] = 100`, then:

``` text
R1 = Memory[500] = 100
```

Effective address:

`EA = A`

Advantages: - Simple to understand. - Easy effective-address
calculation.

Disadvantage: - Address range is limited by the instruction's address
field.

### C. Indirect Addressing

The instruction points to a memory location that contains the actual
operand address.

Example:

``` asm
LOAD R1, (500)
```

Assume:

  Memory address     Value
  ---------------- -------
  500                  800
  800                  100

Steps: 1. Read Memory\[500\], which contains 800. 2. Treat 800 as the
actual operand address. 3. Read Memory\[800\], which contains 100. 4.
Load 100 into R1.

Effective address:

`EA = M[A]`

Advantages: - Flexible memory access. - Useful for pointers and dynamic
data structures.

Disadvantage: - Requires additional memory access.

### D. Register Addressing

The operand is stored directly in a CPU register.

Example:

``` asm
ADD R1, R2
```

Meaning:

`R1 = R1 + R2`

If R1 = 10 and R2 = 20, then R1 becomes 30.

Advantages: - Very fast. - No separate data-memory access is needed to
fetch the operands.

Disadvantage: - Number of registers is limited.

### E. Register Indirect Addressing

A register contains the memory address of the actual operand.

Example:

``` asm
LOAD R1, (R2)
```

Assume: - R2 = 500 - Memory\[500\] = 100

Then R1 = 100.

Effective address:

`EA = contents of R2`

Advantages: - Useful for pointers and arrays. - Address can be changed
by modifying the register.

Disadvantage: - Requires a memory access to retrieve the operand.

### F. Indexed Addressing

The CPU adds an index register to an address or offset from the
instruction.

Formula:

`EA = Address + Index Register`

Example:

``` asm
LOAD R1, 100(R2)
```

Assume: - R2 = 20 - Address/offset = 100

Then:

`EA = 100 + 20 = 120`

If Memory\[120\] = 75, then R1 = 75.

Advantages: - Useful for accessing array elements. - Helps access
consecutive memory locations.

Disadvantage: - Requires effective-address calculation.

### G. Base Register Addressing

The CPU adds a displacement to a base register.

Formula:

`EA = Base Register + Displacement`

Example:

``` asm
LOAD R1, 40(R3)
```

Assume: - R3 = 1000 - Displacement = 40

Then:

`EA = 1000 + 40 = 1040`

If Memory\[1040\] = 250, then R1 = 250.

Advantages: - Useful for accessing data in a memory region. - Helps with
relocation and memory management.

Disadvantage: - Requires address calculation.

### Indexed vs. Base Register Addressing

  -----------------------------------------------------------------------
  Indexed addressing                  Base register addressing
  ----------------------------------- -----------------------------------
  Often used for array elements       Often used for a memory region or
                                      data structure

  Index register represents an offset Base register holds a starting
  or index                            address

  EA = Address + Index                EA = Base + Displacement
  -----------------------------------------------------------------------

Exact terminology and implementation vary by architecture.

### H. Relative Addressing

Commonly used in branch and jump instructions.

A common formula is:

`EA = PC + Displacement`

Where: - PC = Program Counter - Displacement = Offset in the instruction

Example: - PC = 1000 - Displacement = 40 - Target = 1040

The exact PC value used (current or next instruction address) depends on
the architecture.

Advantages: - Useful for conditional and unconditional branches. -
Supports position-independent code.

Disadvantage: - Displacement has a limited range.

### I. Implied Addressing

The operand is not explicitly mentioned because it is understood from
the instruction.

Examples:

``` asm
CLC
NOP
```

`CLC` clears the carry flag in architectures that support it. `NOP`
performs no operation. Some architectures have instructions that
implicitly operate on an accumulator.

Advantages: - Short instructions. - No explicit operand field required.

Disadvantage: - Less flexible because the operand is predetermined by
the instruction.

## 3.4 Comparison of Addressing Modes

  Addressing mode     Where is the operand?    Main idea
  ------------------- ------------------------ -----------------------------------------
  Immediate           Inside instruction       Actual value is given
  Direct              Memory                   Instruction gives memory address
  Indirect            Memory                   Address field points to another address
  Register            CPU register             Operand is in register
  Register indirect   Memory                   Register contains memory address
  Indexed             Memory                   Address + index register
  Base register       Memory                   Base register + displacement
  Relative            Branch target            PC + displacement
  Implied             Predetermined location   Operand is understood

## 3.5 Direct vs. Indirect vs. Register Indirect

Assume:

  Location          Stored value
  --------------- --------------
  Memory\[500\]              800
  Memory\[800\]              100
  R2                         500

### Direct

``` asm
LOAD R1, 500
```

Reads Memory\[500\].

Result: `R1 = 800`

### Indirect

``` asm
LOAD R1, (500)
```

Reads Memory\[500\] to get address 800, then reads Memory\[800\].

Result: `R1 = 100`

### Register Indirect

``` asm
LOAD R1, (R2)
```

R2 contains address 500. CPU reads Memory\[500\].

Result: `R1 = 800`

The key difference is where the effective address comes from.

## 3.6 Addressing Modes in Array Access

Consider:

``` java
int numbers[] = {10, 20, 30, 40, 50};
```

To access `numbers[2]`, the CPU needs to calculate the element's memory
location.

Conceptually:

`EA = Base Address + (Index × Element Size)`

Example: - Base address = 1000 - Index = 2 - Integer size = 4 bytes

`EA = 1000 + (2 × 4) = 1008`

Addressing techniques similar to indexed addressing can be used to
access array elements.

## 3.7 Important Effective-Address Formulas

  Addressing mode     Effective address
  ------------------- ------------------------------------
  Immediate           No separate operand memory address
  Direct              `EA = A`
  Indirect            `EA = M[A]`
  Register indirect   `EA = R`
  Indexed             `EA = A + IX`
  Base register       `EA = BR + D`
  Relative            `EA = PC + D`

Where: - A = Address field - M\[A\] = Contents of memory at address A -
R = Register contents - IX = Index register - BR = Base register - D =
Displacement

These are common forms; exact syntax and PC behavior vary across
processor architectures.

------------------------------------------------------------------------

# 4. Peripheral Devices in Computer Architecture

## 4.1 What are Peripheral Devices?

A **peripheral device** is hardware connected to a computer to provide
input, receive output, store data, or communicate with other devices.

Examples: - Keyboard - Mouse - Monitor - Printer - Scanner - Hard disk -
USB drive - Speakers - Webcam

Peripheral devices help a computer interact with the outside world.

## 4.2 Types of Peripheral Devices

Common categories: 1. Input devices 2. Output devices 3. Storage devices
4. Communication devices

Some devices can belong to more than one category.

## 4.3 Input Devices

An **input device** sends data and instructions to a computer.

Examples:

-   **Keyboard:** Enters text, numbers, and commands.
-   **Mouse:** Controls the cursor; supports clicking, scrolling, and
    dragging.
-   **Scanner:** Converts physical documents and images into digital
    data.
-   **Microphone:** Captures sound as audio input.
-   **Webcam:** Captures images and videos.
-   Other examples: touchscreen, joystick, barcode reader, fingerprint
    reader, graphics tablet.

## 4.4 Output Devices

An **output device** receives processed information from a computer and
presents it to the user.

Examples:

-   **Monitor:** Displays text, images, videos, and applications. Types
    include LCD, LED, and OLED.
-   **Printer:** Produces physical copies.
    -   Inkjet: Uses liquid ink.
    -   Laser: Uses toner and laser technology.
    -   Dot matrix: Uses pins and an ink ribbon.
-   **Speakers:** Convert audio output into sound.
-   **Projector:** Displays output on a large screen or wall.
-   Other examples: headphones, plotters, Braille displays, 3D printers.

## 4.5 Storage Devices

A **storage device** stores data, programs, and files for future use.
Most secondary storage retains data when power is switched off.

### Hard Disk Drive (HDD)

-   Uses magnetic storage.
-   Contains rotating magnetic disks.
-   Stores large amounts of data.
-   Generally slower than SSDs.

### Solid State Drive (SSD)

-   Uses flash memory.
-   Has no moving mechanical parts.
-   Generally faster than traditional HDDs.

### USB Flash Drive

-   Portable storage device.
-   Connects through a USB port.
-   Transfers files between computers.

### Memory Card

-   Small portable storage device.
-   Commonly used in cameras and mobile devices.

### HDD vs. SSD

  Feature        HDD                SSD
  -------------- ------------------ ------------------
  Technology     Magnetic disks     Flash memory
  Speed          Generally slower   Generally faster
  Moving parts   Yes                No
  Noise          Can make noise     Silent
  Cost per GB    Usually lower      Usually higher

## 4.6 Communication Devices

A **communication device** allows a computer to exchange data with other
computers or networks.

Examples:

-   **Network Interface Card (NIC):** Connects a computer to a wired or
    wireless network.
-   **Modem:** Converts signals to enable communication over a
    transmission medium; commonly used in broadband connections.
-   **Wi-Fi adapter:** Allows wireless network connectivity.
-   **Bluetooth adapter:** Enables short-range wireless communication,
    such as connecting headphones or keyboards.

## 4.7 How Peripheral Devices Communicate with the CPU

Peripheral devices generally communicate through **I/O interfaces and
controllers**.

Main components: 1. **CPU:** Executes instructions and controls
operations. 2. **Memory:** Stores programs and data. 3. **System bus:**
Carries information between computer components. 4. **I/O controller:**
Manages communication between the computer and a peripheral. 5.
**Peripheral device:** Performs input, output, storage, or communication
functions.

Example: When a keyboard key is pressed: 1. The keyboard detects the key
press. 2. Its controller sends information through the connection. 3.
The computer's input controller receives the information. 4. The CPU is
notified or retrieves the data. 5. The operating system and application
process the input.

## 4.8 I/O Data Transfer Techniques

The CPU and peripheral devices need methods to exchange data.

### A. Programmed I/O

The CPU repeatedly checks the status of a peripheral device. This
repeated checking is called **polling**.

Example: - CPU asks a printer whether it is ready. - If it is not ready,
CPU checks again.

Advantages: - Simple to implement. - Easy to understand.

Disadvantages: - Wastes CPU time while waiting. - Not efficient for slow
devices.

### B. Interrupt-Driven I/O

A peripheral device sends an interrupt signal to the CPU when it needs
attention or an event occurs.

Example: 1. A keyboard detects a key press. 2. Keyboard controller
signals an interrupt. 3. CPU temporarily handles the interrupt. 4. CPU
resumes its previous work.

Advantages: - CPU does not need to continuously check the device. - More
efficient than polling in many situations.

### C. Direct Memory Access (DMA)

**DMA** means Direct Memory Access.

It allows a device or DMA controller to transfer data between the device
and main memory without requiring the CPU to handle every individual
data transfer.

Example: Reading a large file from a storage device into RAM.

How it works: 1. CPU configures the DMA transfer. 2. DMA controller
manages the transfer. 3. Data moves between the peripheral device and
RAM. 4. DMA controller can notify the CPU when the transfer is complete.

Advantages: - Reduces CPU involvement in large data transfers. -
Improves overall system efficiency.

### Comparison of I/O Techniques

  ------------------------------------------------------------------------
  Feature           Programmed I/O    Interrupt-driven   DMA
                                      I/O                
  ----------------- ----------------- ------------------ -----------------
  CPU involvement   High              Moderate           Lower during bulk
                                                         transfer

  Data transfer     CPU handles it    CPU handles it     DMA controller
                                      after interrupt    manages it

  CPU waiting       Often polls       Usually avoids     Usually avoids
                                      continuous polling per-item handling

  Best suited for   Simple I/O        Event-based I/O    Large data
                                                         transfers
  ------------------------------------------------------------------------

## 4.9 I/O Ports and Interfaces

An **I/O port** is a connection or access point through which a computer
communicates with peripheral devices.

Common interfaces: - **USB:** Connects keyboards, mice, storage devices,
and many other peripherals. - **HDMI:** Carries digital video and
audio. - **Ethernet:** Provides wired network communication. - **Audio
jack:** Connects headphones, microphones, and speakers.

## 4.10 Memory-Mapped I/O and Isolated I/O

### Memory-Mapped I/O

-   I/O device registers are assigned addresses within the memory
    address space.
-   CPU accesses them using ordinary memory read/write instructions.

### Isolated I/O (Port-Mapped I/O)

-   I/O devices use a separate address space.
-   CPU uses special I/O instructions to communicate with them in
    architectures that support this approach.

## 4.11 Real-World Example: Printing a Document

1.  User clicks the Print button.
2.  Operating system sends print data to the printer system.
3.  Printer driver and I/O subsystem manage communication.
4.  Printer receives the data.
5.  Printer processes the information.
6.  Printer produces the physical document.

This shows how software, CPU, memory, I/O controllers, and peripheral
devices work together.

## 4.12 Important Differences

### Input vs. Output vs. Storage vs. Communication

  Category        Purpose                  Examples
  --------------- ------------------------ ------------------
  Input           Sends data to computer   Keyboard, mouse
  Output          Presents results         Monitor, printer
  Storage         Stores information       HDD, SSD
  Communication   Exchanges data           NIC, modem

A touchscreen is both an input and output device.

### Peripheral Device vs. I/O Controller

  -----------------------------------------------------------------------
  Peripheral device                   I/O controller
  ----------------------------------- -----------------------------------
  Physical device connected to a      Hardware that manages communication
  computer                            with a device

  Example: Keyboard                   Example: USB host controller

  Provides input/output functionality Coordinates data transfer and
                                      device operations
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 5. Quick Revision

## Sequential Circuits

-   Sequential circuits depend on current inputs and previous state.
-   Synchronous circuits use a clock.
-   Asynchronous circuits do not require a common clock.
-   Flip-flops store one bit.
-   Registers store multiple bits.
-   Counters count clock pulses.
-   Shift registers shift binary data.
-   FSMs represent systems using states and transitions.
-   Setup time and hold time are important timing requirements.

## Instruction Formats

-   Instruction: Command given to CPU.
-   Instruction format: Arrangement of instruction bits.
-   Opcode: Specifies the operation.
-   Three-address: Three explicit operand/address fields.
-   Two-address: Two explicit operand/address fields.
-   One-address: One explicit address, often uses accumulator.
-   Zero-address: No explicit addresses; often stack-based.
-   Fixed-length: All instructions have the same length.
-   Variable-length: Instructions have different lengths.
-   RISC: Generally simpler, more regular instructions.
-   CISC: Generally more complex instructions.

## Addressing Modes

-   Immediate: Actual value is in the instruction.
-   Direct: Instruction specifies memory address.
-   Indirect: Address field points to a location containing the operand
    address.
-   Register: Operand is in a CPU register.
-   Register indirect: Register contains memory address.
-   Indexed: Address plus index register.
-   Base register: Base register plus displacement.
-   Relative: Program Counter plus displacement.
-   Implied: Operand is understood from the instruction.

## Peripheral Devices

-   Input devices send information to a computer.
-   Output devices present processed information.
-   Storage devices store data.
-   Communication devices exchange data.
-   I/O controllers manage device communication.
-   Programmed I/O uses polling.
-   Interrupt-driven I/O uses device interrupts.
-   DMA transfers data with less CPU involvement.
-   Memory-mapped I/O shares the memory address space.
-   Port-mapped I/O uses a separate I/O address space.

------------------------------------------------------------------------

# 6. Suggested Next Topics

After these lessons, a useful next sequence is:

1.  I/O Organization in Computer Architecture
2.  Interrupts and Exceptions
3.  DMA in more depth
4.  CPU Organization and Architecture
5.  ALU and Control Unit
6.  CPU Registers
7.  Instruction Cycle: Fetch, Decode, Execute
8.  Memory Organization
9.  Cache, RAM, ROM, and Memory Hierarchy

------------------------------------------------------------------------

**End of notes**

These notes are beginner-friendly summaries of the concepts covered in
the lessons. Assembly syntax and some implementation details vary
between processor architectures.
