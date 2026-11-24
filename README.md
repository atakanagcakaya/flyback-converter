# Flyback DC-DC Converter

<p align="center">
  <img src="images/flyback_3d_angled.jpg" alt="Flyback converter PCB" width="85%">
</p>

This is an isolated flyback power supply that takes 18–36 V and gives +12 V, −12 V and +5 V.
I designed it from scratch: requirements, LTspice simulation, part selection, Altium schematic and a 4-layer PCB.

The board has not been built yet. The design is checked in simulation and the PCB passes DRC.

## Specs

| | |
|---|---|
| Input | 18–36 V |
| Outputs | +12 V 0.5 A, −12 V 0.5 A, +5 V 3 A |
| Power | about 27 W |
| Isolation | Input and outputs are galvanically isolated |
| Controller | TI UCC28C43-Q1, around 200 kHz |
| 5 V rail | AP64350Q buck running from +12 V |
| PCB | 4 layers, 90 × 65 mm |
| Tools | Altium Designer, LTspice |

## How It Works

```mermaid
flowchart LR
    VIN["18–36 V"] --> SW["MOSFET + controller"]
    SW --> T1["Transformer"]
    T1 --> P12["+12 V"]
    T1 --> N12["−12 V"]
    P12 --> BUCK["5 V buck"]
    BUCK --> P5["+5 V"]
    P12 --> FB["TL431 + optocoupler"]
    FB --> SW
```

The controller switches the MOSFET and stores energy in the transformer. The secondary windings make +12 V and −12 V.
A TL431 watches +12 V and sends feedback back to the primary side through an optocoupler, so the isolation is kept.
The +5 V rail comes from a small buck converter fed by +12 V.

## Protections

- Input fuse
- Undervoltage lockout: the converter only starts above about 16 V
- Overvoltage protection: if feedback is lost, +12 V is held below 13.5 V
- Short-circuit latch-off: the converter shuts down and stays off until power is cycled
- Cycle-by-cycle current limit
- Soft-start on both sides to avoid overshoot at power-up

## PCB

| Top layer | Ground layer |
|---|---|
| <img src="images/flyback_top_layer.png" width="100%"> | <img src="images/flyback_L2_gnd.png" width="100%"> |

- There is a 3 mm gap between the primary and secondary sides on every layer. Only the transformer and the optocoupler cross it.
- The ground layer is split: primary ground on the left, secondary ground on the right. They never touch.
- The switching loops are kept as small as possible to reduce noise.

| Primary side | Secondary side |
|---|---|
| <img src="images/flyback_3d_primary_detail.jpg" width="100%"> | <img src="images/flyback_3d_detail.jpg" width="100%"> |

## Schematics

| Input and power stage | Protections |
|---|---|
| <img src="images/flyback_sch_1_input_power.png" width="100%"> | <img src="images/flyback_sch_2_primary_protection.png" width="100%"> |

| Controller | Feedback and 5 V buck |
|---|---|
| <img src="images/flyback_sch_3_primary_control.png" width="100%"> | <img src="images/flyback_sch_4_secondary.png" width="100%"> |

## Simulation

The whole converter was simulated in LTspice: power stage, feedback loop, startup, protections, and the 18 V and 36 V input corners.
The file is in the `simulation` folder.

## Folders

- `hardware` — Altium project, libraries and 3D models
- `simulation` — LTspice file
- `images` — renders, layer images and schematics

## Author

Atakan Ağcakaya