# 120A Main Circuit Breaker

![Eaton Bussmann CB285-120](../images/breaker.jpg)

The main breaker can be found mounted on the robot, typically on the outside of the frame perimeter in an accessible location. It **must** be easily reachable and operable by a referee or field staff at all times during competition.

## What is this?

The **Eaton Bussmann CB285-120** is the FRC-legal 120-amp main circuit breaker required on every FRC robot. It serves as the master power switch and overcurrent protection device for the entire robot. It connects in-line between the battery and the Power Distribution Hub (PDH), and can be used to manually cut all robot power in an emergency. It is a Type III (switchable/manual reset) thermally responsive breaker — it can trip automatically under sustained overcurrent, or be manually switched off.

Per the FRC Game Manual, this specific breaker (CB285-120) is the only legal main breaker for FRC competition robots.

## Ports / Connections

| Port | Wire Type | Current Draw | Required | Where is it? | Additional Info |
|------|-----------|--------------|----------|---------------|-----------------|
| Battery-side stud (input) | 6 AWG copper, red (positive) | Up to 120A continuous | ✓ | One of two M6 terminal studs | Connects from the Anderson SB50 robot-side connector; ring terminal required |
| PDH-side stud (output) | 6 AWG copper, red (positive) | Up to 120A continuous | ✓ | Second M6 terminal stud | Connects to the PDH positive input terminal; ring terminal required |

> **Note:** Only the positive (red) wire passes through the main breaker. The negative (black/blue) wire runs directly from the battery cable to the PDH negative terminal, unbroken.

## Status Lights

The CB285-120 does **not** have a status LED. Instead, it has a **visible yellow reset lever** that shows the open (tripped) condition. When the breaker is tripped:
- The yellow lever will be in the raised/protruding position.
- Push the lever back in to reset the breaker (only after resolving the cause of the trip).

The breaker also has a red **push-to-trip** button that can be used to manually cut power.

## Extra Information

- **Manufacturer:** Eaton Bussmann
- **Part Number:** CB285-120
- **Rating:** 120A, 6–48V DC
- **Interrupt rating:** 3 kAIC
- **Type:** Type III — switchable, manual reset, trip-free
- **Terminal:** M6 stud (accepts 1/4" ring terminals)
- **Operating temperature:** -40°C to 85°C
- **Size:** 2.9" × 1.9" × 1.5" (73.7 × 48.3 × 38.1 mm)
- **Weight:** 0.24 lbs
- **IP Rating:** IP67 (weather resistant / waterproof housing)
- The breaker is "trip-free" — it cannot be held closed after it trips. Do not attempt to force it closed during a trip.
- The breaker must be **easily accessible** on the robot; FRC rules require that a referee can cut robot power quickly.
- Do not substitute with any other breaker — only the CB285-120 is FRC-legal as the main breaker.

## Official Documentation and Manuals

- [WPILib Wiring Guide (Main Breaker)](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-1/intro-to-frc-robot-wiring.html)
- [AndyMark CB285-120 Listing](https://andymark.com/products/surface-mount-120-amp-breaker-by-eaton-bussmann)
- [CTR Electronics CB285-120 Listing](https://store.ctr-electronics.com/products/120-amp-breaker)
- [Eaton CB285-120 Product Page](https://www.eaton.com/us/en-us/skuPage.CB285-120.html)
- [FRC Game Manual — R609 (Main Breaker Rules)](https://www.firstinspires.org/resource-library/frc/competition-manual-qa-system)
