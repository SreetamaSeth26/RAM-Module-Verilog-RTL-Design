# RAM Module – Verilog RTL Design

A simple synchronous-read, combinational-write RAM implemented in Verilog. The module supports 16 addressable locations, each 8 bits wide, controlled by separate write-enable and output-enable signals.

---

## Module Overview

**File:** `ram.v`

| Parameter | Value |
|-----------|-------|
| Address width | 4 bits (16 locations) |
| Data width | 8 bits |
| Write trigger | Combinational (`always @(*)`) |
| Read output | Continuous assignment |

### Port Description

| Port | Direction | Width | Description |
|------|-----------|-------|-------------|
| `write_enable` | input | 1 | High to enable write |
| `enable` | input | 1 | High to enable read output |
| `addr` | input | 4 | Memory address |
| `data_in` | input | 8 | Data to write |
| `data_out` | output | 8 | Data read from memory |

### Behavior

- **Write:** When `write_enable = 1` and `enable = 0`, data is written to `mem[addr]`.
- **Read:** When `write_enable = 0` and `enable = 1`, `data_out` reflects `mem[addr]`.
- **Idle:** When neither condition is met, `data_out` is driven to high-impedance (`8'hzz`).

---

## RTL Notes

The `always @(*)` block with a memory array write will infer combinational logic. This means writes happen instantly without a clock edge, which works for behavioral simulation but is not typical of synthesizable RAM. For synthesis targeting real hardware, a clocked (`always @(posedge clk)`) write should be used instead.


Decimal notation is used for memory addresses (`4'd5`, `4'd7`) for readability, while hexadecimal notation is used for data values (`8'h50`, `8'h66`) since it is a compact representation of binary data.


---

## Simulation

Simulated on [EDA Playground](https://edaplayground.com) using Icarus Verilog.

### Testbench Sequence

1. Write `0x50` to address `5`
2. Write `0x66` to address `7`
3. Read from address `5` — expect `0x50`
4. Read from address `7` — expect `0x66`

### Output

```
Time :- 25 Address =  5 || Data = 50
Time :- 40 Address =  7 || Data = 66
```

---

## Synthesis and Netlist Visualization

Synthesized using **Yosys** and visualized with **netlistsvg**.

### Steps

```bash
# Open Yosys
yosys

# Inside Yosys
read_verilog ram.v
prep -top ram
write_json ram.json
exit

# Generate SVG netlist
netlistsvg ram.json -o ram.svg

# View the diagram
open ram.svg
```

This produces a gate-level netlist diagram showing how the design is mapped after synthesis.

---

