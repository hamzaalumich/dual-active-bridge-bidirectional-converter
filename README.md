# Isolated Bidirectional Dual Active Bridge Converter

A MATLAB/Simulink model of a **2 kW isolated bidirectional DC-DC converter** designed for controlled energy transfer between **400 V and 45 V DC systems**.

The project uses a **Dual Active Bridge (DAB)** topology with closed-loop phase-shift control to regulate the magnitude and direction of power flow. It demonstrates power-electronics design, control-algorithm development, PWM generation, model-based software engineering, subsystem integration, and simulation-based validation.

---

## Project Overview

Bidirectional DC-DC converters allow electrical energy to flow in either direction between two DC systems. They are widely used in:

- Electric and hybrid-electric vehicles
- Battery energy-storage systems
- DC microgrids
- Smart-grid applications
- Vehicle-to-grid systems
- High-voltage battery charging
- Renewable-energy systems

This project models an isolated DAB converter that transfers up to **2 kW** between a high-voltage 400 V side and a low-voltage 45 V side.

The converter includes:

- Two actively controlled full bridges
- A high-frequency isolation transformer
- A coupling inductor
- DC-link capacitors
- Closed-loop PI control
- Phase-shift modulation
- PWM gate-signal generation
- Voltage, current, power, and efficiency measurements
- Bidirectional charging and discharging operation

---

## Project Objectives

The main design objectives were to:

- Transfer up to **2 kW** in both directions
- Operate between **400 V and 45 V** DC buses
- Provide galvanic isolation between voltage domains
- Achieve efficiency greater than **95%**
- Maintain voltage ripple below **3%**
- Maintain current ripple below **4%**
- Regulate transferred power using closed-loop control
- Generate synchronized and complementary switching signals
- Validate operation through MATLAB/Simulink simulation

---

## System Architecture

The Simulink model is organized into modular subsystems:

```text
400 V DC Source
      │
      ▼
High-Voltage DC Link
      │
      ▼
Primary Full Bridge
      │
      ▼
Coupling Inductor
      │
      ▼
High-Frequency Isolation Transformer
      │
      ▼
Secondary Full Bridge
      │
      ▼
Low-Voltage DC Link
      │
      ▼
45 V DC Source
```

Additional control and measurement subsystems provide:

- Reference-power generation
- Power-feedback calculation
- PI control
- Phase-shift command generation
- PWM generation
- Gate-signal synchronization
- Voltage and current sensing
- Input and output power calculation
- Efficiency and load-percentage calculation
- Simulation waveform visualization

---

## Dual Active Bridge Operation

The DAB converter consists of two full bridges connected through a high-frequency transformer and a coupling inductance.

Each bridge generates a high-frequency square-wave voltage. Power transfer is controlled by introducing a phase shift between the primary-side and secondary-side bridge voltages.

The direction of power flow depends on which bridge voltage leads:

- When the primary bridge leads, power flows from the **400 V side to the 45 V side**.
- When the secondary bridge leads, power flows from the **45 V side to the 400 V side**.

The transferred power is related to the voltage levels, coupling reactance, switching frequency, and phase-shift angle:

```text
P ∝ (V1 × V2 / XL) × f(phase shift)
```

Increasing the phase-shift magnitude increases transferred power within the converter's operating range. Reversing the phase-shift sign reverses the direction of power flow.

---

## Closed-Loop Control Algorithm

The controller regulates transferred power by continuously comparing measured output power with a reference-power command.

The control process is:

```text
Reference Power
      │
      ▼
Power Error Calculation
      │
      ▼
PI Controller
      │
      ▼
Phase-Shift Command
      │
      ▼
Saturation and Protection
      │
      ▼
PWM Generation
      │
      ▼
Full-Bridge Gate Signals
      │
      ▼
DAB Power Stage
      │
      ▼
Measured Power Feedback
```

The power error is calculated as:

```text
Power Error = Reference Power - Measured Power
```

The PI controller converts this error into the required phase-shift command:

```text
Phase Shift = Kp × Error + Ki × Integral(Error)
```

The implemented controller uses:

```text
Kp = 0.1
Ki = 600
```

The phase-shift command is limited to approximately **±35 degrees** to keep the requested power within the converter's rated operating range.

This closed-loop structure enables the model to:

- Track changing power references
- Regulate bidirectional energy transfer
- Reduce steady-state power error
- Automatically select the required phase-shift direction
- Maintain stable charging and discharging operation

---

## PWM and Gate-Signal Logic

The phase-shift command is processed by the pulse-generation subsystem to create synchronized PWM signals for the two full bridges.

The implemented logic includes:

- Sawtooth carrier-wave generation
- Phase-shift comparison
- PWM state generation
- Set-reset flip-flops
- Signal synchronization
- Complementary gate-signal generation
- NOT-gate logic
- Prevention of invalid simultaneous switching states

Diagonal switch pairs operate together within each full bridge.

For example:

```text
Switches 1 and 4: Positive bridge-voltage state
Switches 2 and 3: Negative bridge-voltage state
```

The same switching principle is applied to the secondary bridge. The relative timing between both bridges determines the power-transfer direction and magnitude.

---

## Power-Stage Design

### Electrical Ratings

| Parameter | Design Value |
|---|---:|
| Rated power | 2 kW |
| High-voltage bus | 400 V |
| Low-voltage bus | 45 V |
| Switching frequency | 50 kHz |
| Transformer ratio | 400:45 |
| Coupling inductance | 100 µH |
| Transformer leakage inductance | 15 µH |
| Transformer magnetizing inductance | 16 mH |
| MOSFET on-state resistance | 11 mΩ |
| High-side source resistance | 20 mΩ |
| Low-side source resistance | 60 mΩ |

---

## DC-Link Design

DC-link capacitors are included on both sides of the converter to:

- Stabilize the DC-bus voltages
- Reduce voltage ripple
- Store short-term energy
- Filter switching noise
- Improve transient behavior

The minimum capacitance was estimated using:

```text
C ≥ Iripple / (4 × f × ΔV)
```

The design calculation produced an approximate minimum requirement of:

```text
C ≥ 667 µF
```

A capacitor rated above the 400 V high-voltage bus was selected for the design.

---

## Coupling Inductor Design

The coupling inductance limits current and controls the amount of power transferred for a given phase-shift angle.

The minimum inductance was estimated from:

```text
L ≥ (V1 × V2) / (2π × f × Pmax)
```

Using the project specifications:

```text
V1   = 400 V
V2   = 45 V
f    = 50 kHz
Pmax = 2 kW
```

The calculated minimum coupling inductance was approximately:

```text
L ≥ 30 µH
```

A **100 µH** coupling inductor was used in the simulation model.

---

## Transformer Design

The high-frequency transformer provides:

- Galvanic isolation
- Voltage conversion between 400 V and 45 V
- Energy transfer between the two active bridges
- Safety separation between voltage domains

Selected transformer parameters include:

| Parameter | Value |
|---|---:|
| Rated power | 2 kW |
| Voltage ratio | 400:45 |
| Switching frequency | 50 kHz |
| Primary resistance | 10 mΩ |
| Leakage inductance | 15 µH |
| Magnetizing inductance | 16 mH |

---

## Measurement and Sensing

The measurement subsystem monitors:

- Primary-side voltage
- Secondary-side voltage
- Coupling-inductor current
- Input current
- Output current
- Input power
- Output power
- Converter efficiency
- Load percentage
- Phase-shift command

Power is calculated from measured voltage and current:

```text
Power = Voltage × Current
```

Converter efficiency is calculated by identifying the source and receiving sides according to the current power-flow direction:

```text
Efficiency = Output Power / Input Power × 100%
```

This allows efficiency to be evaluated during both forward and reverse operation.

---

## Simulation Scenarios

The reference-power command changes during the simulation to test the converter across different operating conditions.

The model evaluates:

- Zero-power operation
- Increasing forward power
- Full-load forward transfer
- Reduced-load operation
- Power-direction reversal
- Full-load reverse transfer
- Charging behavior
- Discharging behavior
- Controller response during reference changes

The sign of the power reference determines the requested direction of energy transfer.

---

## Simulation Results

The simulation demonstrated:

| Performance Metric | Result |
|---|---:|
| Maximum transferred power | 2 kW |
| Power-flow capability | Bidirectional |
| High-voltage bus | 400 V |
| Low-voltage bus | 45 V |
| Full-load efficiency | Greater than 95% |
| Voltage ripple | Below 3% |
| Current ripple | Below 4% |
| Control method | Closed-loop PI phase-shift control |

The reported results show that the model:

- Tracks positive and negative power references
- Reverses power direction through the phase-shift command
- Maintains stable operation during charging and discharging
- Achieves the required efficiency target
- Meets the specified voltage-ripple limit
- Meets the specified current-ripple limit
- Produces synchronized switching waveforms
- Maintains controlled power transfer across different load levels

> The reported results are simulation results and do not represent measurements from a physical hardware prototype.

---

## Model-Based Software Development

Although the application is a power-electronics converter, the project also demonstrates model-based software and algorithm-development skills.

The model converts system requirements into executable control logic through:

- Hierarchical Simulink subsystem development
- Closed-loop feedback-control implementation
- Signal-flow architecture
- PWM algorithm development
- Switching-state logic
- Saturation and command limiting
- Feedback-signal processing
- Measurement-algorithm implementation
- Efficiency-calculation logic
- Simulation scenario development
- Model debugging and validation

The modular architecture separates the power stage, control logic, PWM generation, sensing, and result analysis into understandable and reusable components.

---

## Skills Demonstrated

### Power Electronics

- Dual Active Bridge converter design
- Isolated bidirectional DC-DC conversion
- Full-bridge switching
- High-frequency transformer modeling
- DC-link capacitor sizing
- Coupling-inductor sizing
- Semiconductor loss consideration
- Bidirectional energy transfer
- Efficiency and ripple analysis

### Controls and Algorithms

- Closed-loop PI control
- Phase-shift modulation
- Power-reference tracking
- Error-signal processing
- PWM generation
- Complementary switching logic
- Command saturation
- Bidirectional control logic
- Controller tuning
- Dynamic-response analysis

### Software and Model-Based Development

- MATLAB/Simulink
- Simscape Electrical
- Hierarchical subsystem design
- Executable system modeling
- Control-logic implementation
- Signal processing
- Simulation debugging
- Parameter management
- Model integration
- Test-scenario development
- Data visualization
- Simulation-based validation

---

## Technologies Used

- MATLAB
- Simulink
- Simscape Electrical
- Specialized Power Systems
- PI control
- Phase-shift modulation
- Pulse-width modulation
- Power-electronics simulation
- Model-based design

---

## Repository Structure

```text
dual-active-bridge-bidirectional-converter/
│
├── README.md
├── dab_bidirectional_converter_model.slx
├── DualActiveBridgeConverterData.m
├── docs/
│   ├── dab_converter_design_report.pdf
│   └── dab_converter_presentation.pdf
└── LICENSE
```

### File Descriptions

#### `dab_bidirectional_converter_model.slx`

Complete MATLAB/Simulink model containing the converter power stage, controller, PWM-generation logic, sensing system, and simulation outputs.

#### `DualActiveBridgeConverterData.m`

Initialization script containing parameters required by the Simulink model.

#### `docs/dab_converter_design_report.pdf`

Detailed technical report covering the design calculations, component parameters, control method, simulation architecture, and results.

#### `docs/dab_converter_presentation.pdf`

Presentation summarizing the converter design, control strategy, and simulation results.

---

## Requirements

The model was developed using:

- MATLAB R2024a
- Simulink
- Simscape Electrical
- Specialized Power Systems

A newer compatible MATLAB release may also work.

---

## How to Run

1. Clone or download the repository.

```bash
git clone https://github.com/hamzaalumich/dual-active-bridge-bidirectional-converter.git
```

2. Open MATLAB.

3. Navigate to the repository directory.

```matlab
cd dual-active-bridge-bidirectional-converter
```

4. Run the initialization script.

```matlab
DualActiveBridgeConverterData
```

5. Open the Simulink model.

```matlab
open_system('dab_bidirectional_converter_model.slx')
```

6. Run the simulation from Simulink.

7. Review the generated scopes and signals for:

- Reference and measured power
- Phase-shift command
- Primary and secondary bridge voltages
- Coupling-inductor current
- Input and output power
- Converter efficiency
- Voltage and current ripple

---

## Important Model Dependency

The Simulink model executes the following preload callback:

```matlab
DualActiveBridgeConverterData
```

Therefore, `DualActiveBridgeConverterData.m` must be included in the repository and available on the MATLAB path. Without this initialization script, some model parameters may be undefined when the model opens.

---

## License

This project is available under the MIT License.
