# 50 kV, 1 mA High-Voltage Flyback Converter – MATLAB/Simulink

## Overview

This project presents a **MATLAB/Simulink model of an isolated flyback-type high-voltage DC converter** designed for a target output of:

| Parameter | Target |
|---|---:|
| Input voltage | 12 V DC |
| Output voltage | 50 kV DC |
| Output current | 1 mA |
| Output power | 50 W |
| Switching frequency | 5 kHz |
| Initial duty ratio | 30% |
| Transformer ratio (idealized model) | 12 V : 50 kV |
| Simulation mode | Discrete |
| Powergui sample time | 100 ns |
| Output load equivalent | 50 MΩ |

> **Important:** 50 kV is a lethal high voltage. The Simulink model is intended for simulation and academic study. A physical implementation requires appropriate insulation, creepage/clearance, current limiting, discharge circuitry, enclosure, interlocks, and properly rated high-voltage components.

---

## 1. Project Objective

The objective is to model and study a low-power high-voltage DC converter capable of producing approximately **50 kV at 1 mA** from a **12 V DC input**.

The required output power is:

\[
P_{out}=V_{out}I_{out}
\]

\[
P_{out}=50,000\times0.001=50~W
\]

Therefore, the converter is designed around a **50 W output-power level**.

For a 50 kV, 1 mA load, the equivalent resistive load is:

\[
R_L=\frac{V_{out}}{I_{out}}
\]

\[
R_L=\frac{50,000}{0.001}=50~M\Omega
\]

Thus, a **50 MΩ load** represents the required 50 kV / 1 mA operating point.

---

## 2. Simulink Model Structure

The model contains the following major sections:

1. **12 V DC source**
2. **Input current measurement**
3. **Primary series inductance**
4. **IGBT/Diode switching device**
5. **Pulse Generator**
6. **High-voltage transformer**
7. **Secondary high-voltage rectifier diode**
8. **High-voltage output capacitor**
9. **Output load**
10. **Output current measurement**
11. **Output voltage measurement**
12. **Scopes for monitoring waveforms**
13. **`powergui` configured for discrete simulation**

The overall energy flow is:

```text
12 V DC Source
      │
      ▼
Current Measurement
      │
      ▼
Primary Inductance
      │
      ▼
IGBT / Diode
      │
      ▼
High-Frequency Transformer
      │
      ▼
HV Rectifier Diode
      │
      ▼
HV Output Capacitor
      │
      ├──────────► 50 MΩ Load
      │
      ▼
50 kV DC Output
```

---

## 3. Main Parameters Used in the Current Simulink File

The uploaded `.slx` model was inspected and the following values are currently present.

### DC Input

- DC Voltage Source = **12 V**
- Input current is measured using a Current Measurement block.

### Switching

- Pulse Generator:
  - Period = `1/5000 s`
  - Switching frequency = **5 kHz**
  - Pulse width = **30%**
- Initial ON time:

\[
T_s=\frac{1}{5000}=200~\mu s
\]

\[
T_{on}=0.30T_s=60~\mu s
\]

\[
T_{off}=140~\mu s
\]

### Primary Inductance

The current model uses a Series RLC Branch configured as an inductor:

\[
L_p=1~mH
\]

with approximately:

\[
R_p=1~\Omega
\]

### Transformer

The model uses a Simscape Electrical **Linear Transformer** with:

- Winding 1 voltage setting = **12 V**
- Winding 2 voltage setting = **50,000 V**
- Nominal power setting = **50 to 5000 VA**
- Magnetizing branch:
  - \(R_m=1~M\Omega\)
  - \(L_m=700~\mu H\)

The idealized voltage ratio represented by the model is:

\[
\frac{N_s}{N_p}\approx\frac{50,000}{12}
\approx4166.7
\]

This extremely large ratio should be treated as a **simulation abstraction**, not as a recommended physical single-stage transformer winding ratio.

### Secondary Rectifier

The model contains a diode after the transformer secondary.

Current diode settings include approximately:

- Forward voltage = **0.8 V**
- ON resistance = **1 mΩ**
- Snubber disabled

For a real 50 kV converter, the physical rectifier would need a carefully selected HV diode/diode-string arrangement with adequate reverse-voltage, current, insulation, transient and creepage/clearance ratings.

### Output Capacitor

The current model contains a capacitor branch with:

\[
C_{out}=240\times10^{-7}F
\]

\[
C_{out}=2.4~\mu F
\]

This value should be treated as a starting simulation value and verified against the actual ripple requirement and charging dynamics.

### Output Load

For the required operating point:

\[
R_L=50~M\Omega
\]

because:

\[
I_{out}=\frac{50,000}{50\times10^6}=1~mA
\]

If the existing load block is not set to 50 MΩ, change it to 50 MΩ before evaluating the 1 mA target.

---

## 4. Output Power

The required output is:

\[
V_{out}=50~kV
\]

\[
I_{out}=1~mA
\]

Therefore:

\[
P_{out}=50~W
\]

This is a relatively low-power but **extremely high-voltage** converter.

If the assumed efficiency is \(\eta\), the approximate input power is:

\[
P_{in}=\frac{P_{out}}{\eta}
\]

For example, at 80% efficiency:

\[
P_{in}=\frac{50}{0.8}=62.5~W
\]

The approximate average input current at 12 V is:

\[
I_{in,avg}=\frac{62.5}{12}\approx5.21~A
\]

These values are useful for checking the simulation results.

---

## 5. Important Design Relationships

### Output Load

\[
R_L=\frac{V_o}{I_o}
\]

For 50 kV and 1 mA:

\[
R_L=50~M\Omega
\]

### Output Power

\[
P_o=V_oI_o
\]

\[
P_o=50~W
\]

### Switching Period

\[
T_s=\frac{1}{f_s}
\]

For 5 kHz:

\[
T_s=200~\mu s
\]

### Duty Ratio

For the current pulse generator:

\[
D=0.30
\]

\[
T_{on}=DT_s=60~\mu s
\]

### Ideal Flyback Voltage Relationship

A simplified flyback relationship is commonly expressed using input voltage, duty ratio and transformer turns ratio. In an actual design, leakage inductance, diode drop, parasitics, operating mode and reflected voltage must be included.

Flyback design procedures normally begin with the input range, output voltage/current, switching frequency and transformer requirements, followed by primary inductance, peak current, switch stress and rectifier stress calculations. citeturn0search3turn0search12

---

## 6. Waveforms to Monitor

The model includes measurement blocks and scopes for observing:

### Input/Primary Side

- Input current
- Primary/switch current
- Switch voltage
- Primary current ripple

### Secondary Side

- Rectifier current
- Output capacitor voltage
- Output current
- Output voltage

The most important final checks are:

```text
Vout ≈ 50 kV
Iout ≈ 1 mA
Pout ≈ 50 W
```

The output should also be checked for:

- Voltage ripple
- Startup overshoot
- Steady-state regulation
- Switching spikes
- Transformer current
- IGBT voltage/current stress
- Rectifier reverse-voltage stress

---

## 7. Recommended Simulation Procedure

### Step 1 – Set the input

Set:

```text
Vin = 12 V DC
```

### Step 2 – Set switching frequency

Set:

```text
fs = 5 kHz
```

### Step 3 – Set initial duty ratio

Start with:

```text
D = 30%
```

Do not assume that 30% will automatically produce exactly 50 kV. The duty ratio must be tuned according to the actual transformer model, inductance, losses and output loading.

### Step 4 – Set the output load

Use:

```text
Rload = 50 MΩ
```

This corresponds to:

```text
50 kV / 50 MΩ = 1 mA
```

### Step 5 – Run the simulation

Observe:

- Output voltage
- Output current
- Input current
- IGBT current
- IGBT voltage

### Step 6 – Tune the duty ratio

If the output voltage is below 50 kV, increase the duty ratio gradually.

If the output voltage is above 50 kV, reduce the duty ratio.

Avoid large duty-cycle changes because switch and transformer stresses can increase significantly.

---

## 8. Design Verification Table

| Quantity | Target | Simulation Check |
|---|---:|---:|
| Input voltage | 12 V | Measure |
| Switching frequency | 5 kHz | 5 kHz |
| Duty ratio | 30% initial | Tune |
| Output voltage | 50 kV | Measure |
| Output current | 1 mA | Measure |
| Output power | 50 W | Calculate |
| Load resistance | 50 MΩ | Set/verify |
| Output capacitor | 2.4 µF initial | Verify ripple |
| Primary inductance | 1 mH initial | Verify current |
| Transformer ratio | 12 : 50,000 idealized | Verify model |
| Simulation step | 100 ns | Set in powergui |

---

## 9. Practical Engineering Considerations

The current Simulink model is useful for **concept validation**, but a physical 50 kV / 1 mA converter requires considerably more engineering.

### Transformer

A practical transformer should be designed for:

- Core flux density
- Primary peak current
- Secondary insulation
- Inter-winding capacitance
- Leakage inductance
- Creepage and clearance
- Layer insulation
- Corona prevention
- Thermal performance

TI and other converter-design references emphasize that transformer selection/design is one of the key steps in flyback development, followed by switch and rectifier stress calculations. citeturn0search3

### HV Rectifier

The rectifier must withstand the maximum reverse voltage, including switching transients. A suitable voltage margin is required; the diode selection should be based on both voltage and current stresses rather than nominal output voltage alone. citeturn0search9turn0search2

### Output Capacitor

The capacitor must be rated for:

- At least the required DC voltage
- Transient voltage
- Ripple current
- Insulation requirements
- Suitable capacitance tolerance

### Protection

A practical system should include:

- Primary current limiting
- Over-voltage protection
- Snubber/clamp network
- Controlled startup
- Output discharge path
- HV interlock
- Enclosure
- Emergency shutdown

---

## 10. Limitations of the Current Model

The uploaded model should currently be considered a **simulation prototype**, not a final hardware design.

Important limitations include:

1. The transformer is represented by an idealized Linear Transformer block.
2. The 12 V to 50 kV voltage ratio is extremely large for a single physical transformer.
3. Transformer leakage inductance and parasitic capacitances may not represent a real 50 kV transformer.
4. The present 1 mH primary inductance is a starting simulation value and should be redesigned from the required energy and peak-current constraints.
5. The output capacitor should be selected from the desired ripple and transient specifications.
6. The output load must be explicitly set to 50 MΩ to represent 1 mA at 50 kV.
7. The IGBT and diode models need voltage/current ratings appropriate to the final operating conditions.
8. Closed-loop feedback is not yet represented as a complete regulated 50 kV control system.

---

## 11. Suggested Next Improvements

For a more realistic final-year/project model, the following upgrades are recommended:

- [ ] Replace the idealized transformer with a physically parameterized flyback transformer.
- [ ] Calculate \(L_m\) from the required stored energy.
- [ ] Calculate primary peak and RMS currents.
- [ ] Select the IGBT/MOSFET voltage and current ratings.
- [ ] Calculate switch turn-off voltage including leakage inductance.
- [ ] Design an RCD/TVS/clamp or active-clamp network.
- [ ] Calculate secondary diode PIV.
- [ ] Design the HV rectifier with adequate voltage margin.
- [ ] Calculate \(C_{out}\) from the required ripple.
- [ ] Set \(R_L=50~M\Omega\).
- [ ] Add voltage-feedback control.
- [ ] Add soft-start.
- [ ] Add over-current and over-voltage protection.
- [ ] Compare simulated and calculated values.
- [ ] Perform efficiency and loss calculations.

---

## 12. References

1. Texas Instruments, *Designing a Flyback DC/DC Converter – Flyback Converter Design Procedure*. citeturn0search3
2. onsemi/Fairchild, *AN4137 – Design Guidelines for Off-line Flyback Converters*. citeturn0search12
3. Analog Devices, *Designing the MAX17291 Flyback Converter*. citeturn0search1
4. Monolithic Power Systems, *How to Design a Flyback Converter in Seven Steps*. citeturn0search9

---

## 13. Project Summary

**Project:** 12 V DC to 50 kV DC High-Voltage Flyback Converter

**Target:** 50 kV @ 1 mA

**Output power:** 50 W

**Initial simulation:** MATLAB/Simulink + Simscape Electrical

**Topology:** Isolated flyback-type converter with transformer, IGBT switching, HV diode rectification and capacitive filtering

**Primary objective:** Study the conversion of low-voltage DC into a regulated, isolated, low-current high-voltage DC output.

---

### Disclaimer

This README documents a simulation model for academic and engineering-study purposes. A 50 kV circuit can cause fatal electric shock and can also present stored-energy, arcing and fire hazards. Do not construct or test the hardware without appropriate high-voltage engineering practices, rated equipment, isolation, interlocks and supervision.

