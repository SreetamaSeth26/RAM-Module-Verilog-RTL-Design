# Synchronous RAM — Verilog RTL

A synchronous 16x8-bit RAM module written in Verilog, simulated on EDA Playground and synthesized using Yosys + netlistsvg.

---

## Module Overview

**`ram.v`**

- 16 locations, each 8 bits wide
- Address bus: 4 bits (`addr`)
- Data bus: 8 bits (`data_in` / `data_out`)
- All operations are synchronous to the rising edge of `clk`

| Signal | Direction | Width | Description |
|---|---|---|---|
| `clk` | input | 1 | Clock |
| `write_enable` | input | 1 | High to write |
| `enable` | input | 1 | High to read |
| `addr` | input | 4 | Memory address |
| `data_in` | input | 8 | Data to write |
| `data_out` | output | 8 | Data read out |

**Write condition:** `write_enable = 1`, `enable = 0`  
**Read condition:** `enable = 1`, `write_enable = 0`

---

## Simulation

Simulated on [EDA Playground](https://edaplayground.com) using **Icarus Verilog**.

### Testbench sequence

1. Write `0x50` to address `5`
2. Write `0x66` to address `7`
3. Read from address `5` — expect `0x50`
4. Read from address `7` — expect `0x66`

### Output

```
Time :- 30 Address = 5 || Data = 50
Time :- 40 Address = 7 || Data = 66
```

Both reads return the correct values.

---

## Synthesis

Synthesized using **Yosys**, with the netlist visualized as an SVG using **netlistsvg**.

### Steps

Open a terminal and run:

```bash
cd ~/Desktop
yosys
```

Inside Yosys:

```
read_verilog ram.v
prep -top ram
write_json ram.json
exit
```

Then generate the SVG:

```bash
netlistsvg ram.json -o ram.svg
open ram.svg
```

---

## Moore FSM Sequence Detector (`1010`)

A Moore FSM that detects the sequence `1010` on a serial input. The output goes high only when the complete sequence has been received.

### State diagram

| State | Meaning | Output |
|---|---|---|
| S0 | Idle / reset | 0 |
| S1 | Received `1` | 0 |
| S2 | Received `10` | 0 |
| S3 | Received `101` | 0 |
| S4 | Received `1010` | 1 |

### Module interface

| Signal | Direction | Description |
|---|---|---|
| `clk` | input | Clock |
| `rst` | input | Synchronous reset |
| `seq_in` | input | Serial input bit |
| `detected` | output | High when `1010` is detected |

### Simulation

Simulated on EDA Playground with Icarus Verilog:

```bash
iverilog -Wall -g2012 moore.sv testbench.sv && vvp a.out
```

### Synthesis

```bash
cd ~/Desktop
yosys
```

Inside Yosys:

```
read_verilog moore.v
prep -top moore
write_json moore.json
exit
```

Generate the netlist diagram:

```bash
netlistsvg moore.json -o moore.svg
open moore.svg
```

---

## Tools Used

| Tool | Purpose |
|---|---|
| EDA Playground | Browser-based simulation environment |
| Icarus Verilog (`iverilog`, `vvp`) | Compilation and simulation |
| Yosys | Synthesis and JSON netlist export |
| netlistsvg | Netlist SVG visualization |
