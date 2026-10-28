# Elara Aerospace — CPS 1184 Signal-Conditioning Board  
## Project Guide for Ninad Deshpande

**Task:** Design a small PCB behind a Metallux CPS 1184 RM 2.54 ceramic pressure sensor.  
**Output:** 0.5–4.5 V analog signal for the customer data-acquisition system.  
**Deadline:** One week from the task email.  
**KiCad version:** 10.0  
**Main rule:** Make a small, correct, well-checked design. Do not add features before the basic design works.

---

## 1. Task in short

- The sensor is a Wheatstone bridge.
- Its full-scale output is 1.5 to 5.3 mV/V.
- Its offset is 0 ±0.2 mV/V.
- Its bridge resistance is 10 kOhm ±20%.
- The board gets 24 V DC from the customer system.
- The board must fit in 17.5 mm x 30 mm, or use a little more space only if necessary.
- The board sits behind the sensor on a 2.54 mm pin pitch.
- The cable leaves through a hollow M20x1.5 screw.
- The board must not push on the sensor.
- The cable must not pull on the board.
- The pressure range is 10 to 400 bar.
- The sensor works from -40 to +150 °C.
- The board is protected from direct cryogenic exposure, but some cold exposure is possible.
- The customer wants to capture pressure changes on a millisecond timescale.

**Deliverables:**

1. KiCad schematic.
2. KiCad PCB layout.
3. Short BOM.
4. One-page design explanation.
5. Reply email with the files.

---

## 2. Selected design

Use a **0.5–4.5 V ratiometric analog output**.

This is the best choice because:

- It is like the Metallux CPS 1184-Z amplified sensor.
- Most DAQ systems can read it easily.
- 0.5 V and 4.5 V limits help find cable, supply, or sensor faults.
- It is simpler than a 4–20 mA current loop.
- It fits a small board.

**No assembly calibration.**  
Design the circuit to work from good parts and correct calculations. State the expected accuracy as a worst-case estimate.

---

## 3. Signal chain

```text
24 V DC input
   |
   +-- Input protection
   |
   +-- 5 V low-noise regulator
   |
   +-- CPS 1184 bridge excitation
   |
   +-- Input RC filter
   |
   +-- Instrumentation amplifier
   |
   +-- Buffered 0.5 V reference / offset network
   |
   +-- 2nd-order 1 kHz low-pass filter
   |
   +-- Output buffer
   |
   +-- 0.5–4.5 V output to DAQ
```

---

## 4. Main calculations

Use 5 V bridge excitation.

The sensor gives:

\[
V_{diff} = sensor\ span\ (mV/V) \times 5\ V
\]

For the required 4 V output span:

\[
G = \frac{4.0\ V}{V_{diff}}
\]

| Sensor span | Bridge signal at 5 V excitation | Needed gain |
|---|---:|---:|
| 1.5 mV/V | 7.5 mV | 533 |
| 2.8 mV/V | 14.0 mV | 286 |
| 5.3 mV/V | 26.5 mV | 151 |

Use selectable gain. Do not use one fixed gain for all pressure ranges unless Elara confirms one sensor type.

Target transfer function:

\[
V_{out} = 0.5\ V + 4.0\ V \times \frac{P}{P_{FS}}
\]

Where:

- \(P\) = measured pressure.
- \(P_{FS}\) = full-scale pressure of the selected sensor.

For a 1 kHz bandwidth, the approximate 10–90% step response is:

\[
t_r \approx \frac{0.35}{1\ kHz} = 0.35\ ms
\]

This supports pressure changes on a millisecond timescale.

---

## 5. Requirements checklist

| ID | Requirement | Design answer | Evidence to show | Priority |
|---|---|---|---|---|
| R1 | Read CPS 1184 bridge signal | Instrumentation amplifier with selectable gain | Gain calculation and schematic | MUST |
| R2 | Work from 24 V DC | Input protection and 5 V regulator | Schematic and power calculation | MUST |
| R3 | Support 10–400 bar sensors | Gain and offset designed for sensor span | Gain/offset table | MUST |
| R4 | Give 0.5–4.5 V output | Buffered reference and output stage | Output-stage schematic | MUST |
| R5 | Protect the board | Reverse polarity, TVS, input filter, output protection | Protection schematic | MUST |
| R6 | Reduce noise | RC input filter and 1 kHz low-pass filter | Filter calculation | MUST |
| R7 | Capture millisecond pressure changes | 1 kHz analog bandwidth | Filter and response calculation | MUST |
| R8 | Fit in 17.5 x 30 mm | Small packages and careful placement | PCB layout | MUST |
| R9 | Mount safely | Spacers, no sensor load, cable strain relief | Mechanical notes and layout | MUST |
| R10 | Handle possible cold exposure | Parts rated for low temperature; no full thermal isolation assumed | Temperature section | MUST |
| R11 | Short BOM | List main parts and values | BOM table | MUST |
| R12 | Explain accuracy and noise | Error budget and noise estimate | One-page report | MUST |
| R13 | Use KiCad 10 | Make KiCad 10 project and PDF exports | Project files | MUST |
| R14 | Make the design easy to check | Test points and clear net names | Schematic annotations | SHOULD |
| R15 | Keep option for 4–20 mA | Explain in report why it is not used | Design-decision note | STRETCH |

---

## 6. Assumptions

| Item | Assumption | If wrong |
|---|---|---|
| Output | 0.5–4.5 V analog | Redesign output stage |
| Calibration | No calibration during assembly | Accuracy depends only on parts and sensor specification |
| PCB temperature | -40 to +85 °C, with margin for possible cold exposure | Do not assume full thermal isolation; check all part ratings |
| Bandwidth | 1 kHz analog bandwidth; about 0.35 ms step response | Supports millisecond-scale pressure changes |
| DAQ sampling rate | At least 5–10 kS/s | Needed to capture millisecond-scale events |
| Cable | Short shielded cable to DAQ | Long cable may need more filtering and protection |
| DAQ input | High-impedance voltage input | Check input impedance and protection |

---

## 7. Reply sent to Elara

```text
Subject: Re: Applicant task — CPS 1184 signal-conditioning board

Dear Mr. Leutner,

Thank you for the clarification.

I will continue with realistic assumptions that support easy production and
installation: the PCB will be designed for a practical industrial temperature
range with margin for possible cold exposure, and the signal conditioning will
preserve millisecond-scale pressure changes without making the board overly
complex.

I will include the selected bandwidth, temperature approach, and related
assumptions in the design documentation.

Best regards,
Ninad Deshpande
```

---

## 8. Work plan

Assume about 20 focused working hours. Change the hours if your available time is different.

| Phase | Hours | Work |
|---|---:|---|
| 1. Understand and calculate | 2–3 h | Read task and sensor data. Calculate gain, offset, power, and error budget. |
| 2. Choose parts | 2–3 h | Choose regulator, amplifier, op-amps, protection parts, and connector. |
| 3. Draw schematic in KiCad 10 | 3–4 h | Draw complete circuit. |
| 4. Make PCB layout | 4–5 h | Place parts, route board, check size and mechanical limits. |
| 5. Check design | 3–4 h | Run ERC/DRC. Check calculations, footprints, temperature, and mechanical fit. |
| 6. Write BOM and report | 2–3 h | Write BOM, design decisions, accuracy, noise, test, and limits. |
| 7. Final check and send | 1 h | Export PDFs, check files, send reply email. |

### Checkpoints

- **After phase 1:** You know the gain range, output range, power use, bandwidth, and temperature assumption.
- **After phase 3:** The schematic meets R1 to R7.
- **After phase 4:** The board fits, routes cleanly, and has cable strain relief.
- **Stop adding features:** At least 30 minutes before the deadline.
- **Never cut:** protection, verification, documentation, and mechanical safety.

---

## 9. Detailed schematic design — KiCad 10

This section gives a practical schematic you can draw in **KiCad 10.0**.

Design targets:

- Input supply: 24 V DC.
- Bridge excitation: 5 V DC.
- Output: 0.5–4.5 V ratiometric.
- Analog bandwidth: 1 kHz.
- Board temperature assumption: -40 to +85 °C, with margin for possible cold exposure.
- No assembly calibration.
- Selectable gain for sensor spans from 1.5 to 5.3 mV/V.

### 9.1 KiCad 10 setup

1. Open KiCad and select **File → New Project**.
2. Name the project:

```text
Elara_CPS1184_SignalConditioner
```

3. Open **Schematic Editor**.
4. In **Preferences → Manage Symbol Libraries**, make sure the default KiCad libraries are active.
5. Use these KiCad symbol libraries:

| Library | Use |
|---|---|
| `Device` | Resistors, capacitors, diodes, ferrite beads |
| `Regulator_Linear` | LDO regulator |
| `Amplifier_Instrumentation` | Instrumentation amplifier |
| `Amplifier_Operational` | Op-amps |
| `Connector_Generic` | Connectors and test points |
| `Transistor_FET` | Reverse-polarity MOSFET |
| `Diode` | TVS and protection diodes |

6. Use these power symbols:

| Power symbol | Net name |
|---|---|
| `+24V` | 24 V input |
| `+5VA` | Clean 5 V analog rail |
| `GND` | 0 V reference |
| `VOUT` | 0.5–4.5 V output |

7. Add a schematic text note at the top:

```text
Elara Aerospace — CPS 1184 Signal Conditioner
Output: 0.5–4.5 V ratiometric
Bridge excitation: 5 V
Target bandwidth: 1 kHz
```

### 9.2 Main schematic blocks

Draw the schematic in this order:

```text
J1: 24 V input
  → F1 / PTC
  → D1 TVS
  → Q1 reverse-polarity MOSFET
  → U1 5 V LDO
  → J2 CPS 1184 bridge
  → RC input filter
  → U2 instrumentation amplifier
  → U3A buffered 0.5 V reference
  → U3B 2nd-order low-pass filter
  → J1 VOUT to DAQ
```

### 9.3 Input connector and protection

#### Connector J1 — 24 V input and output

Use a 3-pin locking connector:

| Pin | Net | Function |
|---|---|---|
| 1 | `+24V` | 24 V supply |
| 2 | `GND` | Supply return |
| 3 | `VOUT` | 0.5–4.5 V output |

Use a polarized connector. The cable leaves through the M20x1.5 hollow screw.

#### Protection circuit

| Reference | Part | Suggested value / type | Connection |
|---|---|---|---|
| F1 | PTC or fuse | 100 mA to 250 mA hold current | In series with `+24V` |
| D1 | TVS diode | SMAJ24A or similar | From `+24V` to `GND` |
| Q1 | P-channel MOSFET | Rated at least 40 V, low Rds(on) | Reverse-polarity protection |
| C1 | Ceramic capacitor | 10 µF, 35 V or higher | `+24V` to `GND` |
| C2 | Ceramic capacitor | 100 nF, 50 V | `+24V` to `GND` |
| R1 | Resistor | 100 Ohm to 1 kOhm | Optional series damping before LDO |
| R2 | Resistor | 100 kOhm | MOSFET gate pull-down |

Reverse-polarity MOSFET connection:

```text
+24V → F1 → Q1 drain
Q1 source → protected +24V rail
Q1 gate → GND through R2
```

Check the MOSFET polarity carefully in KiCad. For a P-channel MOSFET used for reverse-polarity protection, the body diode must conduct during normal operation and the gate must be pulled to ground.

### 9.4 5 V regulator

Use the **TPS7A4001** adjustable LDO.

It is suitable because it accepts 7–100 V input, gives up to 50 mA, and is rated from -40 to +125 °C. [web:42][web:47]

| Reference | Part | Value / part number | Function |
|---|---|---|---|
| U1 | TPS7A4001DGNR | 5 V LDO | 24 V to 5 V regulator |
| C3 | Ceramic capacitor | 1 µF, 50 V | LDO input capacitor |
| C4 | Ceramic capacitor | 1 µF, 10 V or higher | LDO output capacitor |
| C5 | Ceramic capacitor | 100 nF | LDO output high-frequency decoupling |
| R3, R4 | Resistors | Set feedback for 5 V | LDO feedback divider |
| C6 | Ceramic capacitor | Check datasheet | Adjust/feedforward capacitor, if required |

LDO connections:

```text
Protected +24V → U1 IN
U1 OUT → +5VA
U1 GND → GND
U1 EN → protected +24V
U1 ADJ/FB → R3/R4 divider
```

Set the output to 5 V using the resistor values given in the TPS7A4001 datasheet. Verify the exact feedback equation and recommended capacitor values before finalizing the BOM. [web:47]

Power check:

```text
Assume total load = 10 mA.
P_LDO = (24 V - 5 V) x 0.010 A = 0.19 W
```

Use the power-pad package and connect its thermal pad to a copper area on the PCB.

### 9.5 Sensor interface

#### Connector J2 — CPS 1184

Use a 4-pin, 2.54 mm header or direct solder connection.

| J2 pin | CPS 1184 signal | Net name |
|---|---|---|
| 1 | `+Ub` | `+5VA_BRIDGE` |
| 2 | `S-` | `BRIDGE_N` |
| 3 | `S+` | `BRIDGE_P` |
| 4 | `-Ub` | `GND` |

The CPS 1184 is a 10 kOhm ±20% Wheatstone bridge with a full-scale output of 1.5 to 5.3 mV/V. [file:11]

#### Bridge decoupling

| Reference | Part | Value | Connection |
|---|---|---|---|
| C7 | Ceramic capacitor | 100 nF, 10 V | `+5VA_BRIDGE` to `GND`, close to J2 |
| C8 | Ceramic capacitor | 1 µF, 10 V | `+5VA_BRIDGE` to `GND`, close to J2 |

Keep `BRIDGE_P` and `BRIDGE_N` short, parallel, and away from the 24 V input.

### 9.6 Input RC filter

Place a small differential filter before the instrumentation amplifier.

| Reference | Part | Suggested value | Connection |
|---|---|---|---|
| R5 | Resistor | 1 kOhm | `BRIDGE_P` to `AMP_IN_P` |
| R6 | Resistor | 1 kOhm | `BRIDGE_N` to `AMP_IN_N` |
| C9 | Ceramic capacitor | 1 nF | `AMP_IN_P` to `AMP_IN_N` |
| C10 | Ceramic capacitor | 1 nF | `AMP_IN_P` to `GND` |
| C11 | Ceramic capacitor | 1 nF | `AMP_IN_N` to `GND` |

This gives a differential cutoff near:

\[
f_c = \frac{1}{2 \pi RC}
\]

With 1 kOhm and 1 nF:

\[
f_c \approx 159\ kHz
\]

This filter removes radio-frequency interference but does not limit the 1 kHz measurement bandwidth.

### 9.7 Instrumentation amplifier

Use the **AD8422** instrumentation amplifier.

It is a good fit because it works from a 4.6–36 V single supply, has rail-to-rail output, supports gain from 1 to 1000 with one resistor, and has low noise and low offset drift. [web:48][web:55]

| Reference | Part | Part number | Function |
|---|---|---|---|
| U2 | Instrumentation amplifier | AD8422BRZ or AD8422ARMZ | Amplify bridge signal |

AD8422 connections:

| AD8422 pin | Connect to |
|---|---|
| `+VS` | `+5VA` |
| `-VS` | `GND` |
| `IN+` | `AMP_IN_P` |
| `IN-` | `AMP_IN_N` |
| `REF` | `VREF` from low-impedance offset buffer |
| `RG` pins | Gain resistor network |
| `OUTPUT` | `AMP_OUT` |

The AD8422 transfer function is:

\[
V_{OUT} = G \times (V_{IN+} - V_{IN-}) + V_{REF}
\]

[web:55]

#### Gain calculation

The AD8422 gain equation is:

\[
G = 1 + \frac{19.8\ k\Omega}{R_G}
\]

Therefore:

\[
R_G = \frac{19.8\ k\Omega}{G - 1}
\]

| Sensor span | Bridge signal at 5 V | Required gain for 4 V span | Calculated \(R_G\) |
|---|---:|---:|---:|
| 1.5 mV/V | 7.5 mV | 533 | 37.2 Ohm |
| 2.8 mV/V | 14.0 mV | 286 | 69.6 Ohm |
| 5.3 mV/V | 26.5 mV | 151 | 132 Ohm |

Use 0.1% resistors.

For a flexible design, use solder-selectable gain resistors:

| Gain option | \(R_G\) value | Suitable sensor span |
|---|---:|---|
| Low gain | 132 Ohm | About 5.3 mV/V |
| Medium gain | 69.8 Ohm | About 2.8 mV/V |
| High gain | 37.4 Ohm | About 1.5 mV/V |

Use 0-Ohm resistors or solder bridges to select one gain path. Do not install more than one gain resistor at the same time.

### 9.8 Offset and reference

The output must be 0.5 V at zero pressure.

Use a low-impedance 0.5 V reference on the AD8422 `REF` pin.

Do not connect a simple resistor divider directly to `REF`. The `REF` pin needs a low-impedance drive.

#### Reference circuit

| Reference | Part | Suggested value | Function |
|---|---|---|---|
| R10, R11 | Resistors | Divider from `+5VA` to `GND` | Creates about 0.5 V |
| C12 | Ceramic capacitor | 100 nF | Reference decoupling |
| U3A | Rail-to-rail op-amp | OPA2376 or similar | Buffers `VREF` |
| R12 | Resistor | 0 Ohm | Connects op-amp output to AD8422 `REF` |

Example divider:

```text
+5VA → R10 → VREF_DIV → R11 → GND
VREF_DIV → U3A non-inverting input
U3A output → VREF → AD8422 REF pin
```

For a simple 0.5 V reference:

```text
R10 = 9 kOhm
R11 = 1 kOhm
VREF = 5 V x 1 kOhm / (9 kOhm + 1 kOhm) = 0.5 V
```

Use 0.1% resistors.

#### Sensor offset handling

The CPS 1184 offset can be 0 ±0.2 mV/V. At 5 V excitation, this is:

\[
V_{offset,bridge} = \pm 1\ mV
\]

At high gain, for example 533, this becomes:

\[
V_{offset,out} = 533 \times 1\ mV = 0.533\ V
\]

This is larger than the 0.5 V output baseline. Therefore, include a small selectable offset-adjustment range at the `VREF` buffer.

Use one of these options:

| Option | Method | Recommendation |
|---|---|---|
| A | Solder-selectable offset resistors around the reference divider | Best for no-calibration, repeatable production |
| B | Small trimmer potentiometer | Allows adjustment but can move with vibration |
| C | Fixed 0.5 V reference only | Simple, but may not cover worst-case sensor offset at high gain |

**Recommended:** use option A. Provide solder-selectable offset resistors that move `VREF` slightly above or below 0.5 V. Select the option during sensor configuration, not during every-board calibration.

Keep the offset-adjustment range small, for example about ±0.6 V at the AD8422 `REF` pin. Check that the output remains inside 0.5–4.5 V for the selected sensor and gain.

### 9.9 2nd-order low-pass filter

Use the second op-amp in a dual op-amp package, for example **OPA2376**.

Target:

```text
Filter cutoff = 1 kHz
Filter type = 2nd-order Sallen-Key low-pass
```

A practical starting design:

| Reference | Part | Suggested value | Function |
|---|---|---|---|
| U3B | Rail-to-rail op-amp | OPA2376 | Active low-pass filter and output driver |
| R20 | Resistor | 10 kOhm | Filter input resistor |
| R21 | Resistor | 10 kOhm | Filter resistor |
| C20 | Ceramic capacitor | 2.2 nF | Filter capacitor |
| C21 | Ceramic capacitor | 1 nF | Filter capacitor |
| R22 | Resistor | 100 Ohm | Output series resistor |
| C22 | Ceramic capacitor | Optional 1 nF to 10 nF | Output RF filter |

Use a standard Sallen-Key unity-gain low-pass topology:

```text
AMP_OUT → R20 → node A → R21 → U3B +
U3B output → C20 → node A
U3B output → C21 → U3B -
U3B - → U3B output, unity-gain feedback
U3B output → R22 → VOUT
```

For a Butterworth-like 2nd-order filter, calculate the exact resistor and capacitor values in a filter tool or spreadsheet. The values above are a starting point; verify the cutoff, Q factor, and component sensitivity before finalizing.

The 1 kHz filter gives an approximate 10–90% rise time of:

\[
t_r \approx \frac{0.35}{1\ kHz} = 0.35\ ms
\]

This supports pressure changes on a millisecond timescale. [web:27][web:28]

### 9.10 Output stage

| Reference | Part | Suggested value | Function |
|---|---|---|---|
| R22 | Resistor | 100 Ohm | Output protection |
| C22 | Ceramic capacitor | 1 nF to 10 nF | Output RF filter |
| D2 | Small-signal Schottky or TVS | Optional | Output clamp |
| TP1 | Test point | `+5VA` | Power test |
| TP2 | Test point | `GND` | Ground test |
| TP3 | Test point | `VOUT` | Output test |
| TP4 | Test point | `AMP_OUT` | Pre-filter signal test |

Output connection:

```text
U3B output → R22 → VOUT → J1 pin 3
```

If J1 already carries `VOUT`, use J1 as the single field connector. Do not create two separate output connectors unless the mechanical design needs them.

### 9.11 Suggested reference designators

| Block | Reference range |
|---|---|
| Input protection | J1, F1, D1, Q1, R1, R2, C1, C2 |
| Regulator | U1, C3–C6, R3, R4 |
| Sensor interface | J2, C7, C8 |
| Input filter | R5, R6, C9–C11 |
| Instrumentation amplifier | U2, gain resistors |
| Reference and offset | R10–R12, C12, U3A |
| Low-pass filter | U3B, R20–R22, C20–C22 |
| Output and test points | R22, C22, D2, TP1–TP4 |

### 9.12 KiCad 10 drawing steps

1. **Place connectors first.**  
   Add `J1`, `J2`, and test points. Add power symbols and net labels.

2. **Draw the input protection.**  
   Place `F1`, `D1`, `Q1`, `C1`, and `C2`. Connect them to `+24V` and `GND`.

3. **Draw the LDO.**  
   Place `U1` and its capacitors. Use the TPS7A4001 datasheet for the feedback network.

4. **Draw the sensor header.**  
   Place `J2` with `+5VA_BRIDGE`, `BRIDGE_P`, `BRIDGE_N`, and `GND`.

5. **Draw the input filter.**  
   Place `R5`, `R6`, `C9`, `C10`, and `C11`.

6. **Draw the AD8422.**  
   Connect `+VS` to `+5VA`, `-VS` to `GND`, inputs to the filter, and `REF` to the buffered reference.

7. **Draw gain selection.**  
   Place three gain-resistor paths. Use 0-Ohm resistors or solder bridges for selection.

8. **Draw the reference buffer.**  
   Place `U3A`, divider resistors, and offset-selection resistors.

9. **Draw the low-pass filter.**  
   Place `U3B`, `R20`, `R21`, `C20`, and `C21`.

10. **Draw the output stage.**  
   Add `R22`, optional `C22`, and the `VOUT` net.

11. **Add net labels.**  
   Use clear names: `+24V`, `+5VA`, `+5VA_BRIDGE`, `BRIDGE_P`, `BRIDGE_N`, `AMP_IN_P`, `AMP_IN_N`, `AMP_OUT`, `VREF`, `VOUT`, and `GND`.

12. **Annotate the schematic.**  
   Use **Tools → Annotate Schematic**.

13. **Assign footprints.**  
   Use **Tools → Assign Footprints**. Check every package before PCB layout.

14. **Run ERC.**  
   Use **Inspect → Electrical Rules Checker**.

15. **Check the schematic.**  
   Confirm every pin is connected and every power net is correct.

### 9.13 Schematic review checklist

- [ ] `J1` has `+24V`, `GND`, and `VOUT`.
- [ ] `F1`, `D1`, and `Q1` protect the 24 V input.
- [ ] `U1` creates a clean 5 V rail.
- [ ] `J2` matches the CPS 1184 pinout.
- [ ] `BRIDGE_P` and `BRIDGE_N` have an RC filter.
- [ ] `U2` is powered from `+5VA` and `GND`.
- [ ] `U2` gain can be selected for 1.5 to 5.3 mV/V sensors.
- [ ] `U2 REF` is driven by a low-impedance buffer.
- [ ] Offset selection can handle the sensor offset range.
- [ ] `U3B` creates a 1 kHz low-pass filter.
- [ ] Output is 0.5–4.5 V.
- [ ] Test points exist for `+5VA`, `GND`, `AMP_OUT`, and `VOUT`.
- [ ] Every part has a footprint.
- [ ] KiCad ERC passes.

---

## 10. PCB layout rules

### Board size

- Start with 17.5 mm x 30 mm.
- Keep the sensor header on the correct 2.54 mm pitch.
- Put the cable connector at the edge that faces the M20 screw.
- Keep enough space for the cable and connector body.

### Placement

Use this order:

1. Sensor header.
2. Cable connector.
3. Input protection.
4. Regulator.
5. Bridge excitation decoupling.
6. Instrumentation amplifier.
7. Reference and filter.
8. Output buffer.
9. Test points.

### Routing

- Make `S+` and `S-` short and parallel.
- Keep analog ground clean.
- Use one solid ground reference.
- Keep the 24 V input away from the bridge signal.
- Put decoupling capacitors close to the amplifier supply pins.
- Put bridge decoupling close to the sensor header.
- Avoid sharp bends in sensitive analog traces.
- Do not route digital or high-current lines under or beside `S+` and `S-`.

### Mechanical design

- Use insulated spacers or standoffs to hold the board.
- The board must not press on the ceramic sensor.
- The cable must be fixed at the M20 screw or housing.
- The cable must not pull on the PCB or solder joints.
- Leave clearance for the connector body and cable bend radius.
- Show the mounting concept in the report or as notes on the layout.

---

## 11. Accuracy and noise

Make an error budget before you say the board is accurate.

Include:

- Sensor offset.
- Sensor span tolerance.
- Sensor thermal offset shift.
- Sensor thermal span shift.
- Amplifier input offset voltage.
- Amplifier offset drift.
- Amplifier noise.
- Resistor tolerance.
- Resistor temperature drift.
- Regulator output accuracy.
- Regulator noise.
- Filter error.
- Output-buffer offset.
- Cable and connector effects.

Write the result like this:

```text
Estimated total error: +/- X % of full scale, before temperature effects.
Estimated temperature error: +/- Y % of full scale over the assumed board temperature range.
```

Do not claim calibration accuracy because there is no assembly calibration.

---

## 12. Verification checklist

### Electrical checks

- [ ] Sensor pinout is correct.
- [ ] Bridge excitation is 5 V.
- [ ] Amplifier input common-mode range is correct.
- [ ] Gain covers 1.5 to 5.3 mV/V sensor span.
- [ ] Offset network can handle 0 ±0.2 mV/V sensor offset.
- [ ] Output is 0.5 V at zero pressure.
- [ ] Output is 4.5 V at full-scale pressure.
- [ ] 24 V input protection is present.
- [ ] Regulator input and output ratings are correct.
- [ ] Regulator power loss is acceptable.
- [ ] Filter cutoff is 1 kHz.
- [ ] Output cannot damage the DAQ in normal operation.
- [ ] All capacitor voltage ratings are above their working voltage.
- [ ] All resistor power ratings are acceptable.
- [ ] All parts work at the assumed temperature range.

### PCB checks

- [ ] Board outline is 17.5 x 30 mm or smaller.
- [ ] Sensor header pitch is 2.54 mm.
- [ ] Cable connector fits the M20 screw opening.
- [ ] No part blocks the cable path.
- [ ] Board does not press on the sensor.
- [ ] Cable strain relief is shown.
- [ ] `S+` and `S-` are short and close together.
- [ ] Analog and power sections are separated.
- [ ] Ground return is clear.
- [ ] Decoupling capacitors are near the parts they support.
- [ ] KiCad ERC passes.
- [ ] KiCad DRC passes.
- [ ] All footprints match the selected parts.

### Document checks

- [ ] BOM has part values, packages, and important ratings.
- [ ] Report explains why 0.5–4.5 V was selected.
- [ ] Report explains gain and offset design.
- [ ] Report gives expected accuracy and noise.
- [ ] Report states temperature assumptions and limits.
- [ ] Report explains test and calibration method.
- [ ] Report explains mechanical mounting.
- [ ] Report states what would be improved with more time.

---

## 13. Test and calibration plan

Even without assembly calibration, explain how Elara can test and calibrate the board.

### Bench test

1. Apply 24 V DC through a current-limited supply.
2. Check input current.
3. Check 5 V rail.
4. Check bridge excitation.
5. Apply zero pressure or a known low pressure.
6. Record output voltage.
7. Apply a known high pressure.
8. Record output voltage.
9. Check output noise with the DAQ or an oscilloscope.
10. Check step response with a fast pressure change, if equipment is available.
11. Test output load with the expected DAQ input impedance.
12. Test reverse supply, if safe to do so.
13. Test output short circuit, if the design claims short-circuit protection.

### Calibration method

Use two known pressures:

- Zero or low pressure.
- Known high pressure near full scale.

Adjust or document:

- Zero output.
- Span output.
- Linearity.
- Temperature behavior.

Because this design has no assembly calibration, present this as the **recommended production calibration method**, not as something already built into the board.

---

## 14. Report outline

### Title

```text
Signal-Conditioning Board for Metallux CPS 1184 Pressure Sensor
```

### 1. Objective

Design a compact PCB that changes the CPS 1184 bridge signal into a 0.5–4.5 V analog output for Elara’s DAQ.

### 2. Output choice

Use 0.5–4.5 V ratiometric output.

Reason:

- Similar to the CPS 1184-Z.
- Easy for DAQ systems.
- Fault-indicating signal limits.
- Simpler than 4–20 mA.

### 3. Circuit concept

Describe:

- 24 V protection.
- 5 V regulator.
- 5 V bridge excitation.
- Instrumentation amplifier.
- Selectable gain and offset.
- 1 kHz low-pass filter.
- Output buffer.

### 4. Main calculations

Show:

- Gain calculation.
- Offset calculation.
- Regulator power calculation.
- Filter cutoff.
- Step response.
- Error budget.

### 5. Expected accuracy and noise

Give realistic numbers.

Separate:

- Sensor error.
- Electronics error.
- Temperature error.
- Noise.
- Total estimated error.

### 6. Temperature

State:

- Sensor range: -40 to +150 °C.
- Board design range: -40 to +85 °C, with margin for possible cold exposure.
- No full thermal isolation is assumed.
- Prototype temperature testing is needed before production.

### 7. Mechanical design

Explain:

- Board position behind the sensor.
- Spacer or standoff method.
- Cable strain relief at the M20 screw.
- No mechanical load on the sensor.

### 8. Test and calibration

Explain:

- Bench test steps.
- Two-point calibration method.
- Noise test.
- Step-response test.
- Protection test.

### 9. Limits and next steps

State:

- No assembly calibration in this version.
- Temperature range is an assumption.
- Complete sensor-plus-electronics response needs prototype testing.
- Possible future work: temperature compensation, 4–20 mA version, EMC testing.

---

## 15. BOM template

| Ref | Part | Value / part number | Package | Function | Key rating |
|---|---|---|---|---|---|
| F1 | PTC or fuse | TBD | TBD | Input overcurrent protection | Voltage, current |
| D1 | TVS diode | SMAJ24A or similar | SMA | Transient protection | Clamp voltage, power |
| Q1 | P-channel MOSFET | TBD | SOT-23 or larger | Reverse-polarity protection | Vds, current |
| U1 | LDO regulator | TPS7A4001DGNR | MSOP-8 PowerPAD | 24 V to 5 V | Vin, Iout, temperature |
| U2 | Instrumentation amplifier | AD8422BRZ / ARMZ | SOIC-8 / MSOP-8 | Bridge-signal amplification | Offset, noise, gain |
| U3 | Dual op-amp | OPA2376 or similar | SOIC-8 / MSOP-8 | Reference buffer and filter | Offset, bandwidth |
| R1–Rn | Precision resistors | TBD | 0603 / 0805 | Gain, offset, filter | Tolerance, drift |
| C1–Cn | Capacitors | TBD | TBD | Decoupling and filtering | Voltage, tolerance |
| J1 | Field connector | 3-pin locking | TBD | 24 V, GND, VOUT | Current, temperature |
| J2 | Sensor header | 4-pin, 2.54 mm | TBD | Connection to CPS 1184 | Current, temperature |
| TP1–TP4 | Test points | TBD | TBD | Test and debug | Voltage rating |

---

## 16. File names

Use these names:

```text
Elara_CPS1184_SignalConditioner.kicad_pro
Elara_CPS1184_SignalConditioner.kicad_sch
Elara_CPS1184_SignalConditioner.kicad_pcb
Elara_CPS1184_Schematic.pdf
Elara_CPS1184_PCB_Top.pdf
Elara_CPS1184_PCB_Bottom.pdf
Elara_CPS1184_BOM.csv
Elara_CPS1184_Design_Decisions.pdf
```

---

## 17. Submission email

Use this after your files are ready.

```text
Subject: Applicant task — CPS 1184 signal-conditioning board

Dear Mr. Leutner,

Please find attached my design for the CPS 1184 signal-conditioning board.

I selected a 0.5–4.5 V ratiometric analog output because it matches the
CPS 1184-Z concept, is easy to read with a DAQ, and provides clear
fault-indicating signal limits.

The design uses a protected 24 V input, a 5 V bridge-excitation rail,
a precision instrumentation amplifier, selectable gain and offset,
a 1 kHz low-pass filter, and a buffered 0.5–4.5 V output.

The attached files contain:

- KiCad schematic
- PCB layout within the requested size limit
- Short BOM
- One-page explanation of design decisions, expected accuracy and noise,
  and the proposed test and calibration method

Best regards,
Ninad Deshpande
```

---

## 18. Final rule

Do not send the design until:

1. The schematic is complete.
2. The PCB fits the size limit.
3. The gain and offset calculations are checked.
4. The protection concept is clear.
5. The mechanical mounting is safe.
6. The accuracy and temperature limits are honestly written.
7. KiCad ERC and DRC pass.
8. The BOM and report match the schematic.
