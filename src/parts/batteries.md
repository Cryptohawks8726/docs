# Batteries

![MK ES17-12 Battery](../images/battery.jpg)

Batteries can be found in the electrical room, stored in the battery charging station. Charged batteries are labeled and kept separate from depleted ones.

## What is this?

The FRC robot battery is a **MK ES17-12** 12V 18Ah Sealed Lead Acid (SLA) battery. It is the sole power source for the entire robot, supplying 12V DC to the Power Distribution Hub (PDH), which then distributes power to all motors, sensors, and control system components. Each match, the robot runs on a single battery. Teams typically rotate through multiple batteries between matches to ensure full charge.

## Ports / Connections

| Port | Wire Type | Current Draw | Required | Where is it? | Additional Info |
|------|-----------|--------------|----------|---------------|-----------------|
| Positive terminal (+) | 6 AWG copper, red/yellow/white/brown or black w/ stripe | Up to 270A peak (5s) | ✓ | Top of battery, bolt terminal (M5) | Must use ring terminal; connects to red battery cable leading to main breaker |
| Negative terminal (−) | 6 AWG copper, black or blue | Up to 270A peak (5s) | ✓ | Top of battery, bolt terminal (M5) | Must use ring terminal; connects to black battery cable leading to main breaker |
| Anderson SB50 Connector | 6 AWG copper, red and black | — | ✓ | End of battery cable assembly | Mates with robot-side SB50 connector; gray and black housings are intermateable, use consistent color |

> **Note:** Per the FRC Game Manual, battery leads must be copper, minimum 6 AWG (16 mm²), maximum 12" long, color-coded for polarity.

## Extra Information

- **Voltage:** 12V nominal; 12.7–13.5V open circuit when fully charged
- **Capacity:** 18 Ah (20-hour rate)
- **Weight:** ~12.89 lbs (5.86 kg)
- **Dimensions:** 7.13" × 2.99" × 6.57" (181 × 76 × 167 mm)
- **Max discharge current (5s):** 270A
- **Internal resistance:** ~12 mΩ (ideal: <15 mΩ; replace if >20 mΩ)
- **Charger settings:** Cycle use 14.4–15.0V; max charge current 5.4A
- **Case material:** ABS (UL94 HB flame retardant)
- Batteries should be checked with a battery beak or similar tool before each match. A fully charged battery should read above 12.5V under load.
- Never leave a battery fully discharged for extended periods — this degrades SLA batteries significantly.
- Do **not** use non-SLA or lithium batteries; they are not FRC-legal.

## Official Documentation and Manuals

- [WPILib Robot Battery Basics](https://docs.wpilib.org/en/stable/docs/hardware/hardware-basics/robot-battery.html)
- [MK ES17-12 Datasheet (PDF)](https://www.mkbattery.com/download_file/1207/0)
- [AndyMark Battery Listing](https://andymark.com/products/mk-es17-12-12v-sla-battery-set-of-2)
- [FRC Game Manual — R601 (Battery Rules)](https://www.firstinspires.org/resource-library/frc/competition-manual-qa-system)
