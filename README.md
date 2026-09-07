# Auto Volt Portable LED Tester & Diagnostic Unit

A handheld, rechargeable diagnostic instrument designed to test and characterize discrete LEDs, high-power emitters, and LED backlight strips under dynamic operating conditions. 

Unlike basic pass/fail continuity testers or digital multimeters that lack forward load simulation, this device measures and displays real-time forward voltage drop ($V_f$) and driving current ($I$) simultaneously. Built entirely modularly using off-the-shelf modules, point-to-point wiring, and a custom 3D-printed enclosure.

---

## Features

- **Simultaneous $V/I$ Telemetry:** Real-time dual display for forward voltage drop ($V_f$) and working current ($mA/A$) using the DSN-VC288 panel meter.
- **Dynamic Voltage Range:** Boosts single-cell lithium-ion levels (3.7V–4.2V) up to ~30V via an onboard MT3608 boost converter to test higher-voltage LED series strings.
- **Current Limiting / Load Protection:** 1 kΩ inline ballast resistor ($R_1$) limits driving current to protect sensitive LEDs against thermal runaway during probing.
- **Integrated Battery Management:** USB-rechargeable power architecture via a TP4056 lithium battery charger module with integrated protection circuitry.
- **Zero Custom PCB Required:** Engineered using standardized breakout modules assembled cleanly in a custom compact chassis.

---

## Component List (BOM)

| Component | Part / Module | Reference Designator | Description / Purpose |
| :--- | :--- | :--- | :--- |
| **Battery Charger** | TP4056 Module | `U5` | 1S Li-ion battery charging and protection unit (Micro-USB/Type-C) |
| **DC-DC Step-Up** | MT3608 Boost Module | `U4` | High-efficiency boost converter (3.7V in $\rightarrow$ stepped-up test rail out) |
| **Dual Meter** | DSN-VC288 | `U1` | 0–100V, 0–10A dual 3-digit 7-segment digital display |
| **Current Limiter**| Resistor (1 kΩ) | `R1` | Ballast resistor for load simulation and safe LED driving limits |
| **Power Switch** | 2-Pin Rocker Switch | `SWITCH` | SPST power switch to isolate the boost module from battery rail |
| **Output Port** | 2-Pin Screw Terminal | `P2` | 5.00mm pitch terminal connector (`CONN-TH_2P-P5.00`) for probes |
| **Power Source** | 18650 / Li-Po Cell | `BATTERY` | 3.7V nominal rechargeable lithium-ion battery |
| **Wiring** | 24–28 AWG Wires | — | Stranded hookup wire for module-to-module interconnections |

---

## Schematic Reference & Circuit Architecture

The device architecture is organized into four main stages:

## Step-by-Step Wiring & Connection Guide

### 1. Battery & Charging Stage (`U5` - TP4056)
1. Solder the **Battery Positive (+)** terminal to the **`B+`** pad of the TP4056 module.
2. Solder the **Battery Negative (-)** terminal to the **`B-`** pad of the TP4056 module.
3. Wire the **`OUT-`** pad of the TP4056 directly to the **Common System Ground (`GND`)**.
4. Wire the **`OUT+`** pad of the TP4056 to **Pin 1** of your external SPST power switch.

---

### 2. Boost Converter Stage (`U4` - MT3608)
1. Connect **Pin 2 of the SPST power switch** to **`VIN+`** (Pin 2) of the MT3608 module.
2. Connect **`VIN-`** (Pin 1) of the MT3608 module to the common ground rail coming from **`OUT-`** of the TP4056.
3. *Pre-tuning Step:* Before connecting downstream components, power on the MT3608, place a multimeter across **`VOUT+`** and **`VOUT-`**, and trim the onboard multi-turn potentiometer until the desired maximum open-circuit testing voltage is reached (typically ~24V–30V).

---

### 3. Display, Sense & Metering Stage (`U1` - DSN-VC288)

The DSN-VC288 module has two separate wiring harnesses:
- **Small 3-Pin Connector (Power & Voltage Sense):** Thin Red, Thin Black, Thin Yellow.
- **Large 2-Pin Connector (Current Shunt):** Thick Red, Thick Black.

#### A. Powering the Meter & Voltage Sensing
* **Thin RED Wire (VCC):** Solder directly to **`VOUT+`** (Pin 4) of the MT3608 boost converter.
* **Thin BLACK Wire (GND):** Solder directly to **`VOUT-`** (Pin 3) of the MT3608 boost converter.
* **Thin YELLOW Wire (Voltage Sense):** Connect to **`VOUT+`** (Pin 4) alongside the thin red wire to monitor the stepped-up output rail voltage directly.

#### B. Current Shunt Return Path
* **Thick BLACK Wire (Current In):** Solder to **`VOUT-`** (Pin 3) of the MT3608 converter.
* **Thick RED Wire (Current Out):** Solder to **Pin 1** of output terminal connector `P2` (this forms your **Negative / Anode Test Probe**).

---

### 4. Output Terminal & Current Limiting (`R1` & `P2`)
1. Solder one leg of the **1 kΩ Resistor (`R1`)** to the **`VOUT+`** rail (Pin 4 of MT3608).
2. Solder the opposite leg of the **1 kΩ Resistor (`R1`)** to **Pin 2** of terminal connector `P2` (this forms your **Positive / Cathode Test Probe**).
3. Connect your red testing lead/probe to **`P2` Pin 2 (+)** and the black testing lead/probe to **`P2` Pin 1 (-)**.

---

## Module Connection Summary Table

| From Module & Pin | To Component & Pin | Wire Color / Notes |
| :--- | :--- | :--- |
| **Battery (+)** | `TP4056: B+` | Red |
| **Battery (-)** | `TP4056: B-` | Black |
| **`TP4056: OUT+`** | `SWITCH: Pin 1` | Power bus connection |
| **`TP4056: OUT-`** | `MT3608: VIN-` (Pin 1) | Shared system ground |
| **`SWITCH: Pin 2`** | `MT3608: VIN+` (Pin 2) | Switched positive input |
| **`MT3608: VOUT+`** (Pin 4) | `DSN-VC288: Thin RED` | Meter logic supply |
| **`MT3608: VOUT+`** (Pin 4) | `DSN-VC288: Thin YELLOW` | Meter voltage sense line |
| **`MT3608: VOUT+`** (Pin 4) | `R1 (1 kΩ): Lead 1` | Power delivery to ballast resistor |
| **`MT3608: VOUT-`** (Pin 3) | `DSN-VC288: Thin BLACK` | Meter logic return ground |
| **`MT3608: VOUT-`** (Pin 3) | `DSN-VC288: Thick BLACK` | Low-side shunt input |
| **`R1 (1 kΩ): Lead 2`** | `P2: Pin 2` | **PROBE (+)** Positive test terminal |
| **`DSN-VC288: Thick RED`** | `P2: Pin 1` | **PROBE (-)** Negative current-return terminal |

---

## Operating Instructions

1. **Powering On:** Flip the rocker switch. The DSN-VC288 digital display will illuminate, showing the open-circuit output voltage set on the MT3608 module (~24V–30V) and `0.00A`.
2. **Component Testing:**
   * Touch the **Red Probe (`P2` Pin 2)** to the **LED Anode (+)**.
   * Touch the **Black Probe (`P2` Pin 1)** to the **LED Cathode (-)**.
3. **Reading Values:**
   * The top digital display will automatically drop to and display the exact forward voltage drop ($V_f$) of the diode under test (e.g., ~1.8V–2.2V for red LEDs, ~3.0V–3.3V for white/blue LEDs, or higher for LED series arrays).
   * The bottom display will show the instantaneous operating current drawn through the 1 kΩ load resistor.
4. **Recharging:** Connect any standard 5V USB charger to the TP4056 port. The onboard red LED indicates active charging, and the blue/green LED indicates full charge status.

---

## Authors & Contributors
- **Prof. Parthasarthi Sadhasivam** (Mentor)— Department of Mechatronics, Thiagarajar College of Engineering (TCE)
- **S. Manibalagan** — Department of Mechatronics, Thiagarajar College of Engineering (TCE)
- **A Guruprakash** — Department of Mechatronics, Thiagarajar College of Engineering (TCE)
- **P. Kavitharun** — Department of Mechatronics, Thiagarajar College of Engineering (TCE)
