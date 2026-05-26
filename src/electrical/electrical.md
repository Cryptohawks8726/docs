# Electrical Guide

This section covers wiring and managing the electrical components of an FRC robot. It explains how each component works, how they connect together, and the rules and best practices you need to follow.

For more information, refer to the [Rev Hardware Documentation](https://docs.revrobotics.com/), the [CTRE Documentation](https://v6.docs.ctr-electronics.com/en/stable/), and the [WPILib Wiring Guide](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-1/intro-to-frc-robot-wiring.html).

---

## Safety Rules

Before touching any wiring, follow these rules:

- Always disconnect the battery before making any wiring changes.
- Never work on the robot while it is enabled in the Driver Station.
- Do not short positive and negative wires together. This can damage components or start a fire.
- Use the correct wire gauge for each connection. Using wire that is too thin can cause it to overheat.
- Keep wiring away from moving parts such as gears, belts, and arms.

---

## Core Components Overview

Every FRC robot uses the same basic set of electrical components. Understanding what each one does makes wiring much easier.

### Battery

The battery is a 12V sealed lead-acid battery. It is the only power source for the robot. FRC requires a specific battery type: 18 Ah, 12V. The battery connects to the main breaker with 6 AWG (or larger) wire.

- Always use Anderson SB50 connectors on the battery leads.
- Charge batteries using an approved FRC charger.
- Label batteries with tape and a marker so you know which are charged.
- Never leave a battery fully discharged for a long time, as this damages it.

### Main Breaker

The main breaker is a 120A circuit breaker that acts as the robot's master power switch. It connects between the battery and the Power Distribution Hub (PDH). Pressing the red button cuts all power to the robot.

- Mount the main breaker somewhere it can be easily reached in an emergency.
- The main breaker must be accessible from outside the robot frame per FRC rules.
- Wire from battery (+) to main breaker IN, then main breaker OUT to PDH (+). Battery (-) goes directly to PDH (-).

### Power Distribution Hub (PDH)

The PDH (REV Power Distribution Hub) takes power from the battery and distributes it to every component on the robot. It has 20 high-current channels (40A max each) and 3 low-current channels. Each channel has a snap-action breaker.

- Each channel can be individually switched off or on in software.
- The PDH communicates with the robot over CAN bus.
- Connect the PDH to the RoboRIO using a CAN connection and a 10A breaker channel for the RoboRIO power input.
- Power the radio directly from a PDH channel with a 10A breaker. Do not use the VRM for radio power.

**Breaker sizes by component:**

| Component | Breaker Size |
|---|---|
| Drive motors (Falcon 500, Kraken X60, NEO) | 40A |
| Smaller motors (NEO 550, Minion, etc.) | 20A or 30A |
| RoboRIO | 10A |
| Radio (VH-109) | 10A |
| Pneumatic Hub (PH) | 20A |

> **Note:** Always check the FRC game manual and component documentation for the required breaker size. Using the wrong size can trip breakers during matches or damage components.

### RoboRIO

The RoboRIO is the main controller of the robot. It runs your team's code and communicates with all other components. Both the roboRIO 1 and roboRIO 2 are legal. The roboRIO 2 is preferred due to better performance and more storage.

- Power the RoboRIO through its power input terminals using a dedicated PDH channel with a 10A breaker.
- The RoboRIO connects to the radio via ethernet using the port labeled "RIO" on the VH-109.
- The RoboRIO is on the CAN bus as the bus master.
- PWM headers on the RoboRIO can control motor controllers that do not use CAN.

### Radio

The radio for FRC is the Vivid-Hosting VH-109. It handles communication between the robot and the Driver Station over Wi-Fi 6E (6 GHz). The radio is powered directly from the PDH, not through a VRM.

- Power the radio using a 10A breaker channel on the PDH, wired to the Weidmuller DC input on the VH-109.
- Connect the RoboRIO to the port labeled "RIO" on the VH-109 using an ethernet cable.
- The radio must be configured at a FIRST event using the Radio Kiosk, or at home using the Vivid-Hosting web interface.
- The radio must be updated to the latest firmware before use at official events.
- Mount the radio so its indicator lights are visible.

> **Note:** At home for practice, you need a second VH-109 acting as an access point to connect your Driver Station laptop to the robot radio. The access point radio must be powered from a wall adapter, not a battery.

### Motor Controllers

Motor controllers sit between the PDH and the motors. They take a power input from the PDH and control how much power goes to the motor based on commands from the RoboRIO.

Common motor controllers used in FRC:

| Controller | Communication | Common Motors |
|---|---|---|
| Talon FX (inside Falcon 500 / Kraken X60) | CAN | Falcon 500, Kraken X60 (integrated) |
| Talon FXS | CAN | Minion, other brushless/brushed motors |
| SPARK MAX | CAN or PWM | NEO, NEO 550 |
| SPARK Flex | CAN or PWM | NEO Vortex |
| Thrifty Nova | CAN or USB | NEO, other brushless motors |
| Talon SRX | CAN or PWM | CIM, Mini-CIM, brushed motors |
| Victor SPX | CAN or PWM | Brushed motors (legacy use) |

**Wiring a motor controller:**
1. Connect the controller power input (red/black) to a PDH channel with the correct breaker.
2. Connect the motor output wires to the motor.
3. If using CAN, daisy-chain the CAN wires through the controller.
4. If using PWM, connect a PWM cable from the controller to a PWM header on the RoboRIO.

> **Important:** Each motor controller must have a unique CAN ID set using a configuration tool (REV Hardware Client for SPARK MAX and SPARK Flex, Phoenix Tuner X for CTRE devices). Duplicate IDs will cause unpredictable behavior.

---

## Wire Gauges

Using the correct wire gauge is critical for safety. Wire that is too thin will overheat and can melt or start a fire.

| Application | Wire Gauge |
|---|---|
| Battery to main breaker | 6 AWG |
| Main breaker to PDH | 6 AWG |
| Drive motor controllers | 12 AWG |
| Smaller motor controllers | 18 AWG |
| CAN bus wiring | 22 AWG (twisted pair) |
| PWM signals | Provided cable |
| Sensor wiring | 22-24 AWG |

In wire sizing, a smaller AWG number means a thicker wire that can carry more current.

---

## CAN Bus

CAN (Controller Area Network) bus is a two-wire communication network that connects the RoboRIO to smart devices like motor controllers and the PDH. It uses a yellow wire (CAN High) and a green wire (CAN Low).

### How CAN Bus is Wired

CAN devices are wired in a daisy-chain from the RoboRIO out to the last device, which must be terminated. The PDH has a built-in CAN terminator.

```
RoboRIO --> Device 1 --> Device 2 --> ... --> PDH (termination)
```

- The RoboRIO is always one end of the chain.
- The PDH must be the other end. Its built-in terminator closes the bus.
- Twist the yellow and green wires together when running long distances to reduce interference.
- Keep CAN wires away from high-current wires where possible.

### CAN IDs

Every device on the CAN bus needs a unique ID number. Motor controllers default to ID 0 or 1, so you must change them before putting multiple controllers on the bus.

- Use REV Hardware Client to set CAN IDs on SPARK MAX and SPARK Flex controllers.
- Use Phoenix Tuner X to set CAN IDs on CTRE devices (Talon FX, Talon FXS, Talon SRX, Victor SPX).
- Keep a written or digital record of which ID is assigned to which mechanism.

### Checking CAN Bus Health

In the Driver Station, the CAN utilization percentage should stay below 90% during normal operation. If you see CAN errors or missing devices, check:

- Loose or disconnected CAN wires
- Duplicate CAN IDs
- A broken daisy-chain (missing connection between two devices)
- Missing termination

---

## Wiring Best Practices

### Cable Management

Poor cable management causes problems during matches. Loose wires can get caught in mechanisms, connectors can pull out, and it becomes very hard to debug issues.

- Use zip ties to bundle and secure wires along the frame.
- Leave enough slack so wires are not pulled tight when the robot moves.
- Do not zip-tie directly over wire insulation so tightly that it cuts in.
- Use wire labels or colored tape to identify wires quickly.
- Secure connectors so they cannot vibrate loose.

### Connector Types

| Connector | Used For |
|---|---|
| Anderson SB50 | Battery leads |
| Wago lever nuts | PDH power input terminals |
| Weidmuller push-in | PDH channel outputs, radio power input |
| JST | Sensors, encoders, small signal wires |
| RJ45 (ethernet) | Radio to RoboRIO |

Always make sure connections are fully seated and secure. A loose connection has high resistance, which causes voltage drop and heat.

### Protecting Wires

- Use split loom tubing or cable sleeves to protect wire bundles from sharp edges.
- Wherever a wire passes through a hole in sheet metal or a frame extrusion, use a rubber grommet to prevent the edge from cutting the insulation.
- Avoid running wires near motors, as motors get hot.

---

## Robot Wiring Order

When wiring a new robot, work in this order to stay organized:

1. Mount the PDH, RoboRIO, and main breaker to the robot.
2. Run and connect the main power cables (battery, main breaker, PDH).
3. Wire the RoboRIO power from the PDH.
4. Wire the radio power directly from a PDH channel.
5. Connect the RoboRIO to the radio with an ethernet cable.
6. Wire each motor controller to the PDH and to its motor.
7. Run the CAN bus chain through all devices.
8. Connect signal cables (PWM, sensors, encoders).
9. Label every wire and secure all cable runs.
10. Do a visual inspection before connecting the battery.

---

## Pre-Match Checklist

Before every match or test session, verify the following:

- [ ] Battery is fully charged and connector is secure
- [ ] Main breaker is accessible and seated properly
- [ ] No loose wires or connectors
- [ ] No wires near moving mechanisms
- [ ] All PDH breakers are inserted fully
- [ ] Radio lights indicate correct status
- [ ] Driver Station shows green communication and no CAN errors
- [ ] Robot mode set to Teleop (or correct mode) before enabling

---

## Common Wiring Mistakes

**Reversed polarity:** Connecting positive and negative backwards will damage or destroy components. Always double-check red (+) and black (-) before connecting power.

**Wrong breaker size:** Using a breaker that is too large will not protect the wiring. Always use the breaker size specified in the FRC rules or component documentation.

**Duplicate CAN IDs:** Two devices with the same CAN ID will conflict and cause one or both to stop responding. Set CAN IDs before putting devices on the bus.

**Not securing connectors:** Vibration during a match can pull connectors loose. Always use zip ties or adhesive mounts to keep connectors in place.

**Wires too short:** Wires that are pulled tight will break at the connector over time. Leave a small amount of slack in every run.

**No wire labels:** Unlabeled wiring makes debugging very slow. Label every wire at both ends.

**Wrong radio ethernet port:** The RoboRIO must connect to the port labeled "RIO" on the VH-109. Using the wrong port can damage connected devices.
