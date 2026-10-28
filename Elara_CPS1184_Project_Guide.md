# Elara Aerospace — CPS 1184 Signal-Conditioning Board  
## Project Guide for Ninad Deshpande

**Task:** Design a small PCB behind a Metallux CPS 1184 RM 2.54 ceramic pressure sensor.  
**Output:** 0.5–4.5 V analog signal for the customer data-acquisition system.  
**Deadline:** One week from the task email.  
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
- The electronics temperature range is not yet known.
- The measurement bandwidth is not yet known.

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
   +-- Fixed gain and offset network
   |
   +-- 2nd-order low-pass filter
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
V_{diff} = sensor\ span\ (mV/V) 	imes 5\ V
\]

For the required 4 V output span:

\[
G = rac{4.0\ V}{V_{diff}}
\]

| Sensor span | Bridge signal at 5 V excitation | Needed gain |
|---|---:|---:|
| 1.5 mV/V | 7.5 mV | 533 |
| 2.8 mV/V | 14.0 mV | 286 |
| 5.3 mV/V | 26.5 mV | 151 |

Use a gain range that covers the sensor types used by Elara.  
Do not use one fixed gain for all pressure ranges unless Elara confirms one sensor type.

Target transfer function:

\[
V_{out} = 0.5\ V + 4.0\ V 	imes rac{P}{P_{FS}}
\]

Where:

- \(P\) = measured pressure.
- \(P_{FS}\) = full-scale pressure of the selected sensor.

---

## 5. Requirements checklist

| ID | Requirement | Design answer | Evidence to show | Priority |
|---|---|---|---|---|
| R1 | Read CPS 1184 bridge signal | Instrumentation amplifier with enough gain | Gain calculation and schematic | MUST |
| R2 | Work from 24 V DC | Input protection and 5 V regulator | Schematic and power calculation | MUST |
| R3 | Support 10–400 bar sensors | Gain and offset designed for sensor span | Gain/offset table | MUST |
| R4 | Give 0.5–4.5 V output | Output buffer and offset network | Output-stage schematic | MUST |
| R5 | Protect the board | Reverse polarity, TVS, input filter, output protection | Protection schematic | MUST |
| R6 | Reduce noise | RC input filter and 2nd-order low-pass filter | Filter calculation | MUST |
| R7 | Fit in 17.5 x 30 mm | Small packages and careful placement | PCB layout | MUST |
| R8 | Mount safely | Spacers, no sensor load, cable strain relief | Mechanical notes and layout | MUST |
| R9 | State temperature range | Use assumed range until Elara answers | Report limitation section | MUST |
| R10 | Short BOM | List main parts and values | BOM table | MUST |
| R11 | Explain accuracy and noise | Error budget and noise estimate | One-page report | MUST |
| R12 | Use KiCad | Make KiCad project and PDF exports | Project files | MUST |
| R13 | Make the design easy to check | Test points and clear net names | Schematic annotations | SHOULD |
| R14 | Keep option for 4–20 mA | Explain in report why it is not used | Design-decision note | STRETCH |

---

## 6. Assumptions

Use these assumptions until Elara answers.

| Item | Assumption | If wrong |
|---|---|---|
| Output | 0.5–4.5 V analog | Redesign output stage |
| Calibration | No calibration during assembly | Accuracy depends only on parts and sensor specification |
| PCB temperature | -40 to +85 °C | May need different parts, PCB material, connector, and solder process |
| Bandwidth | 500 Hz or less | Change filter cutoff and noise plan |
| Cable | Short shielded cable to DAQ | Long cable may need more filtering and protection |
| DAQ input | High-impedance voltage input | Check input impedance and protection |

---

## 7. Questions for Elara

Send this email now.

```text
Subject: Applicant task — two clarification questions

Dear Mr. Leutner,

Thank you for the task. To finalize the signal-conditioning design, could you please clarify two points?

1. What temperature range will the signal-conditioning PCB itself be exposed to?
   In particular, will it be directly exposed to cryogenic temperatures, or is it
   thermally isolated behind the sensor?

2. What measurement bandwidth or fastest pressure event/rise time should the board preserve?

Best regards,
Ninad Deshpande
```

Do not wait for the answer. Continue with the assumptions above.

---

## 8. Work plan

Assume about 20 focused working hours. Change the hours if your available time is different.

| Phase | Hours | Work |
|---|---:|---|
| 1. Understand and calculate | 2–3 h | Read task and sensor data. Calculate gain, offset, power, and error budget. |
| 2. Choose parts | 2–3 h | Choose regulator, amplifier, op-amps, protection parts, and connector. |
| 3. Draw schematic | 3–4 h | Draw complete circuit in KiCad. |
| 4. Make PCB layout | 4–5 h | Place parts, route board, check size and mechanical limits. |
| 5. Check design | 3–4 h | Run ERC/DRC. Check calculations, footprints, temperature, and mechanical fit. |
| 6. Write BOM and report | 2–3 h | Write BOM, design decisions, accuracy, noise, test, and limits. |
| 7. Final check and send | 1 h | Export PDFs, check files, send reply email. |

### Checkpoints

- **After phase 1:** You know the gain range, output range, power use, and temperature assumption.
- **After phase 3:** The schematic meets R1 to R6.
- **After phase 4:** The board fits, routes cleanly, and has cable strain relief.
- **Stop adding features:** At least 30 minutes before the deadline.
- **Never cut:** protection, verification, documentation, and mechanical safety.

---

## 9. Schematic blocks

### 9.1 Input protection

Use:

- Input fuse or resettable PTC.
- Reverse-polarity MOSFET.
- TVS diode for transient protection.
- Input bulk capacitor.
- Small series resistor or ferrite bead if needed.

Check:

- MOSFET voltage rating is above 24 V.
- TVS clamp voltage does not damage the regulator.
- Fuse or PTC current rating is correct.
- Parts work at the assumed board temperature.

### 9.2 5 V regulator

Use a low-noise LDO.

The regulator powers:

- Sensor bridge.
- Instrumentation amplifier.
- Filter and output op-amps.
- Other low-power analog parts.

Calculate regulator heat:

\[
P_{LDO} = (24\ V - 5\ V) 	imes I_{load}
\]

Example:

```text
If I_load = 10 mA:
P_LDO = 19 V x 0.010 A = 0.19 W
```

Check:

- Input voltage range.
- Output current.
- Dropout voltage.
- Thermal resistance.
- PCB copper area for heat.
- Output capacitor stability requirements.

### 9.3 Sensor interface

The CPS 1184 has four pins:

```text
+Ub
S-
S+
-Ub
```

Use a 4-pin, 2.54 mm header or direct solder connection.

Keep the differential lines `S+` and `S-`:

- Short.
- Close together.
- Away from the 24 V input.
- Away from high-current paths.
- Protected by a small differential RC filter.

### 9.4 Amplifier

Use a precision instrumentation amplifier.

Choose it for:

- Low input offset voltage.
- Low offset drift.
- Low noise.
- Rail-to-rail output if needed.
- Gain range near 150 to 600.
- Supply voltage compatible with the 5 V rail.
- Temperature range at least as wide as the board temperature range.

Do not load the sensor bridge too much. The bridge resistance is only 10 kOhm ±20%.

### 9.5 Gain and offset

Use fixed precision resistors.

Use 0.1% resistors or better for gain-setting and offset-setting networks.

Design for:

- Zero pressure gives about 0.5 V.
- Full-scale pressure gives about 4.5 V.
- The circuit can handle sensor offset of 0 ±0.2 mV/V.
- The circuit can handle sensor span from 1.5 to 5.3 mV/V.

If one gain cannot cover all cases, use solder-selectable gain options.

Avoid potentiometers if possible. They can move with vibration and make the design less repeatable.

### 9.6 Filtering

Use two filter stages:

1. Small RC filter before the instrumentation amplifier.
2. 2nd-order active low-pass filter after amplification.

Start with a cutoff near 500 Hz.

Change it after Elara gives the required bandwidth.

Check:

- Filter does not remove useful pressure information.
- Filter reduces noise and radio-frequency interference.
- Op-amp bandwidth is enough for the filter and gain.
- Filter does not make the output unstable.

### 9.7 Output stage

Use a rail-to-rail op-amp buffer.

Add:

- Series output resistor.
- Output clamp or TVS if needed.
- Test point for `VOUT`.
- Clear label: `0.5–4.5 V OUTPUT`.

Check:

- Output can drive the DAQ input.
- Output does not go below 0 V or above 5 V during normal operation.
- Output survives a short circuit to ground or supply, if this is a requirement.

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
7. Filter.
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

Check these before you send the files.

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
- [ ] Filter cutoff is suitable.
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
10. Test output load with the expected DAQ input impedance.
11. Test reverse supply, if safe to do so.
12. Test output short circuit, if the design claims short-circuit protection.

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

Use this structure for the one-page report.

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
- Gain and offset network.
- Low-pass filter.
- Output buffer.

### 4. Main calculations

Show:

- Gain calculation.
- Offset calculation.
- Regulator power calculation.
- Filter cutoff.
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
- Assumed board range until Elara answers.
- Required information from Elara.
- Effect of cryogenic exposure on parts and assembly.

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
- Protection test.

### 9. Limits and next steps

State:

- No assembly calibration in this version.
- Temperature range needs confirmation.
- Bandwidth needs confirmation.
- Prototype testing is needed before production.
- Possible future work: temperature compensation, 4–20 mA version, EMC testing.

---

## 15. BOM template

| Ref | Part | Value / part number | Package | Function | Key rating |
|---|---|---|---|---|---|
| F1 | Fuse or PTC | TBD | TBD | Input overcurrent protection | Voltage, current |
| D1 | TVS diode | TBD | TBD | Transient protection | Clamp voltage, power |
| Q1 | P-MOSFET | TBD | TBD | Reverse-polarity protection | Vds, current |
| U1 | LDO regulator | TBD | TBD | 24 V to 5 V | Vin, Iout, temperature |
| U2 | Instrumentation amplifier | TBD | TBD | Bridge-signal amplification | Offset, noise, gain |
| U3 | Op-amp | TBD | TBD | Filter / output buffer | Offset, bandwidth |
| R1–Rn | Precision resistors | TBD | 0603 / 0805 | Gain and offset | Tolerance, drift |
| C1–Cn | Capacitors | TBD | TBD | Decoupling and filtering | Voltage, tolerance |
| J1 | Sensor header | 4-pin, 2.54 mm | TBD | Connection to CPS 1184 | Current, temperature |
| J2 | Cable connector | TBD | TBD | 24 V, GND, VOUT | Locking, cable size |
| TP1–TPn | Test points | TBD | TBD | Test and debug | Voltage rating |

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
a precision instrumentation amplifier, adaptable gain and offset,
filtering, and a buffered 0.5–4.5 V output.

The attached files contain:

- KiCad schematic
- PCB layout within the requested size limit
- Short BOM
- One-page explanation of design decisions, expected accuracy and noise,
  and the proposed test and calibration method

I assumed a board temperature range of -40 to +85 °C and a measurement
bandwidth of up to 500 Hz until you confirm the actual values.

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
