# Asynchronous FIFO Design in Verilog

This project implements an **Asynchronous FIFO (First-In, First-Out) using Verilog HDL**. It is designed to transfer data between two independent clock domains, where the read and write operations are driven by separate clocks.

The design uses binary pointers for memory addressing and Gray-coded pointers for clock-domain crossing. A two-stage flip-flop synchronizer transfers the Gray-coded pointers between the read and write clock domains, helping reduce the risk of metastability affecting the receiving logic.

## Design Overview

The FIFO is built using a memory block, separate read and write pointer logic, and synchronizer modules.

- **FIFO.v** – Top-level module connecting the memory, pointer logic, and synchronizers.
- **FIFO_memory.v** – Stores the data and handles memory read and write operations.
- **wptr_full.v** – Updates the write pointer and generates the `wfull` flag.
- **rptr_empty.v** – Updates the read pointer and generates the `rempty` flag.
- **two_ff_sync.v** – Two-stage synchronizer used to pass pointer information between clock domains.
- **FIFO_tb.v** – Testbench for simulating FIFO operation with independent read and write clocks.

## How the Design Works

The write pointer is updated in the write clock domain, while the read pointer is updated in the read clock domain. Binary versions of these pointers select memory locations. Their Gray-code versions are passed through two-stage synchronizers before being used by the logic in the opposite clock domain.

The synchronized pointers are used to determine the FIFO status:

- `wfull` indicates that the FIFO cannot accept another write.
- `rempty` indicates that there is no data available to read.

These flags help prevent writing when the FIFO is full and reading when it is empty.

## Architecture

The following diagrams show the overall FIFO structure, memory block, and pointer synchronizer.

### FIFO Architecture

![FIFO Architecture](Async_FIFO_designing/imgs/FIFO_Schematic.png)

### FIFO Memory

![FIFO Memory](Async_FIFO_designing/imgs/fifo_mem.png)

### Two-Stage Synchronizer

![Two-Stage Synchronizer](Async_FIFO_designing/imgs/2ff_sync.png)

## Parameters

The top-level FIFO module is parameterized so that the data width and address width can be configured.

| Parameter | Default | Description |
|---|---:|---|
| `DSIZE` | 8 | Width of the data bus in bits |
| `ASIZE` | 4 | Address width; determines the FIFO depth |
| `DEPTH` | `2^ASIZE` | Number of entries in the FIFO memory |

The testbench overrides these values with `DSIZE = 8` and `ASIZE = 3`, giving a depth of 8 entries.

## Simulation

The testbench generates separate read and write clocks with different periods and exercises the FIFO through three scenarios:

1. Writing data into the FIFO and reading data back.
2. Filling the FIFO and attempting additional writes.
3. Attempting reads after the FIFO has been emptied.

### Waveforms

**Simulation 1**

![Simulation 1](Async_FIFO_designing/imgs/Sim/sim1.png)

**Simulation 2**

![Simulation 2](Async_FIFO_designing/imgs/Sim/sim2.png)

**Simulation 3**

![Simulation 3](Async_FIFO_designing/imgs/Sim/sim3.png)

## Running the Simulation

The design can be compiled and simulated using [Icarus Verilog](https://github.com/steveicarus/iverilog). Run the following commands from the project root directory:

```bash
iverilog -g2012 -o fifo_sim \
  RTL/FIFO.v \
  RTL/FIFO_memory.v \
  RTL/rptr_empty.v \
  RTL/wptr_full.v \
  RTL/two_ff_sync.v \
  RTL/FIFO_tb.v

vvp fifo_sim
```

To inspect waveforms in GTKWave, add waveform dumping to the testbench (using `$dumpfile` and `$dumpvars`), rerun the simulation, and open the generated `.vcd` file:

```bash
gtkwave fifo.vcd
```

## Tools and Concepts

- Verilog HDL
- RTL and digital design
- Asynchronous FIFO
- Clock Domain Crossing (CDC)
- Gray-code pointer synchronization
- Icarus Verilog
- GTKWave
