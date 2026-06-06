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

## Note
Decimal notation is used for memory addresses (4'd5, 4'd7) for readability, while hexadecimal notation is used for data values (8'h50, 8'h66) since it is a compact representation of binary data.

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
## Schematic

The synthesized netlist was generated using **Yosys** and visualized using **netlistsvg**. The schematic confirms successful inference of the memory block and associated read/write control logic.



---

