# FPGA-Based Arithmetic Display System

**Verilog RTL • ZedBoard (Zynq-7000 XC7Z020) • Synchronous control • 8-bit arithmetic**

An FPGA arithmetic demonstration that uses physical switches as an 8-bit input, push buttons to capture two operands and select an operation, and eight board LEDs to display the binary result. The design brings together **register-based datapath control, a combinational ALU, input synchronization, and FPGA pin constraints** in a small, inspectable module.

## How it works

```text
8 DIP switches ────────────┬──► A register ─┐
                           └──► B register ─┤
Push buttons ─► 2-FF sync ─► edge detectors ├─► 8-bit ADD/SUB ─► 8 LEDs
                                    │       │
                                    └─► mode register
```

The switch value is sampled only when the corresponding load button is pressed; the operands remain stored when the switches change. A separate mode register selects addition or subtraction. The low eight result bits drive the LEDs, so the displayed answer uses **8-bit modular arithmetic** rather than exposing a carry/borrow indicator.

## Board controls

| Input | Action |
| --- | --- |
| `SW0`–`SW7` | Set an 8-bit unsigned value |
| `BTNL` | Capture switch value into operand A |
| `BTNR` | Capture switch value into operand B |
| `BTNU` | Select addition |
| `BTND` | Select subtraction |
| `BTNC` | Clear both operands and select addition |
| `LD0`–`LD7` | Display result, least significant bit at LD0 |

For example, capture A = 5 and B = 3, then select addition to display `00001000` (8), or subtraction to display `00000010` (2).

## RTL design highlights

- **Two-stage flip-flop synchronizers** on all five asynchronous push-button inputs, reducing metastability propagation risk.
- **Rising-edge detectors** turn a held button level into a single clock-event pulse.
- **Clocked operand and mode registers** separate user input capture from computation.
- **Combinational 9-bit intermediate ALU** performs unsigned addition/subtraction; `[7:0]` is presented on the LEDs.
- **100 MHz clock constraints** and physical mappings for ZedBoard clock, switches, buttons, and LEDs.

## Repository contents

| File | Purpose |
| --- | --- |
| [`RTL_File`](RTL_File) | Verilog `top` module: synchronizers, edge detection, operand registers, and ALU |
| [`XDC_File`](XDC_File) | Xilinx Design Constraints with clock and I/O pin mapping |
| `README.md` | Project overview and setup guidance |

The committed files are extensionless. When importing them into Vivado, copy or rename them locally to `top.v` and `zedboard.xdc` if needed.

## Getting started with Vivado

1. Create an RTL project targeting the **ZedBoard / XC7Z020** device.
2. Add `RTL_File` as a Verilog source and select `top` as the top module.
3. Add `XDC_File` as an XDC constraint file.
4. Review the clock, I/O standards, and pin assignments against **your board revision** before connecting hardware.
5. Run elaboration, synthesis, implementation, and bitstream generation; program the board and exercise the controls.

## Current limitations

- Button inputs are synchronized and edge-detected, but **mechanical switch debouncing is not implemented**. A bouncing press may register more than once.
- Only the low eight bits are displayed; there is no dedicated carry, borrow, overflow, or sign indicator.
- This repository does not include an automated testbench, simulation logs, or hardware validation report. Do not interpret this documentation as a claim that timing closure or hardware tests were completed.

**Tech:** Verilog, digital logic, Xilinx Vivado, Zynq-7000 FPGA.
