# CAN Bus

CAN (Controller Area Network) bus is the primary communication network on an FRC robot. It connects the RoboRIO to smart devices like motor controllers, the PDH, and the Pneumatic Hub using just two wires. Understanding how CAN works — and how to set it up correctly — is essential to getting a robot that runs reliably.

---

## What is CAN Bus?

CAN bus is a two-wire serial communication protocol originally developed for automotive use. On an FRC robot, it allows the RoboRIO to send commands to and receive data from smart devices at high speed.

The two wires are:
- **CAN High (CANH)** — Yellow wire
- **CAN Low (CANL)** — Green wire

Data is transmitted as a differential signal between these two wires. Because the signal is differential (not referenced to ground), CAN bus is naturally resistant to electrical noise — which is why it works well near the high-current wiring found on FRC robots.

---

## How CAN Bus is Wired

CAN devices must be wired in a **daisy chain** topology — each device connects to the next in a single continuous line. The two endpoints of the chain must be terminated. On an FRC robot:

- The **RoboRIO** is always one endpoint (it is the CAN bus master)
- The **PDH** is always the other endpoint (it has a built-in 120Ω termination resistor)

> [!NOTE]
> With the release of System Core for the 2027 FRC Season we will most likely be starting multiple parrallel CAN bus chains at the SystemCore. For this reason, we will also need seperate 120Ω resisters to terminate the CAN. Also, the CAN bus does NOT HAVE to terminate at the PDH. We usually keep it this way to avoid having another resister but it can be terminated anywhere with a resister. 

```
RoboRIO --> Device 1 --> Device 2 --> Device 3 --> ... --> PDH (termination)
```

The order of devices in the middle of the chain does not affect function. What matters is that the RoboRIO and PDH are at the two ends.

**Wiring rules:**
- Connect CANH to CANH and CANL to CANL throughout the chain. Do not cross the wires.
- **Twist the yellow and green wires together** for any run longer than about 6 inches. Twisting reduces electromagnetic interference (EMI) from nearby high-current motor wiring.
- Keep CAN wires as far from high-current power cables as is practical.
- Do not use a star (branching) topology. CAN bus must be a single continuous chain with no branches.
- Use 22 AWG twisted pair wire for all CAN runs.

---

## CAN IDs

Every device on the CAN bus must have a **unique ID number**. IDs are assigned by the user using configuration software before the device is added to the bus. Most motor controllers ship with a default ID of 0 or 1, which means if you add multiple controllers to the bus without configuring them first, they will all have the same ID and conflict with each other.

### Why Unique IDs Matter
When two devices share the same CAN ID, they both respond to commands meant for that ID and both transmit data on it at the same time. This causes bus collisions and errors. One or both devices may stop responding, and the errors can affect all other devices on the bus as well. This is one of the most common causes of motor failures at competition.

### How to Set CAN IDs

**For REV devices (SPARK MAX, SPARK Flex):**
1. Connect the motor controller to your computer via USB.
2. Open **REV Hardware Client**.
3. Select the device from the list.
4. Change the CAN ID to the desired value and click "Update".
5. Disconnect the USB before adding the device to the CAN bus.

**For CTRE devices (Talon FX, Talon FXS, Talon SRX, Victor SPX):**
1. Connect the motor controller to your computer via USB (or connect it to the robot with CAN and USB).
2. Open **Phoenix Tuner X**.
3. Select the device from the device list.
4. Set the ID field to the desired value and click "Set".
5. Confirm the ID change before proceeding.

### Recommended CAN ID Conventions

Keeping CAN IDs organized by subsystem makes debugging much easier. Here is one common convention:

| ID Range | Use |
|---|---|
| 0-5 | Gyro, PDH, Rio, etc |
| 10-12, 20-22, 30-32, 40-42 | Drive encoders, steering motors, and drive motors |
| 50+ | Mechanisms - Usually grouped together |
| 60+ | Sensors and other Misc (Also can go in 1-10)|

The actual convention your team uses does not matter as long as it is consistent and documented.

### Keeping a CAN ID Record

Always maintain a written or digital record of every CAN ID assignment. A simple spreadsheet works well:

| CAN ID | Device Type | Mechanism | Notes |
|---|---|---|---|
| 1 | Talon FX | Left Front Drive | Inverted in code |
| 2 | Talon FX | Left Rear Drive | Inverted in code |
| 3 | Talon FX | Right Front Drive | |
| 4 | Talon FX | Right Rear Drive | |
| 10 | SPARK MAX | Arm | NEO motor |
| 11 | SPARK MAX | Wrist | NEO 550 |
| 20 | SPARK Flex | Intake | NEO Vortex |

This record should be version-controlled alongside your robot code.

> [!NOTE]
> 8726 uses a PDH Diagram which can be found [here](https://docs.google.com/spreadsheets/d/1W4r9z-1OGcxcLtQgmEdVuYnr0KC4oxIJB-q80xxuI-M/edit?usp=drive_web&ouid=109313504472713111007).(Might require access to team drive but it can be found online somewhere) This serves as a good holder for the wiring charts/diagrams and CAN ids. 

---

## Checking CAN Bus Health

The CAN bus status can be monitored in real time from the **Driver Station**.

- Open the Driver Station and click the **lightning bolt icon** or go to the **Diagnostics** tab.
- The **CAN Bus Utilization** percentage should stay below **90%** during normal operation. High utilization can cause delays and missed messages.
- **CAN Bus errors** should be **zero** at all times. Any non-zero error count indicates a problem that needs to be investigated.

### Common CAN Bus Problems and Fixes

| Symptom | Likely Cause | Fix |
|---|---|---|
| CAN errors in the DS | Loose or broken CAN wire | Inspect the entire CAN chain for loose connections |
| CAN errors in the DS | Duplicate CAN IDs | Check IDs in Phoenix Tuner X and REV Hardware Client |
| One device not responding | Break in the daisy chain at that device | Check CAN wires at the problem device |
| All devices stopped responding | Missing termination (PDH disconnected from chain) | Verify PDH is at the end of the chain and CAN wires are connected |
| Intermittent CAN errors | CAN wires not twisted, near power cables | Re-route and twist CAN wires away from motor wiring |

---

## CAN vs PWM

Some motor controllers support both CAN and PWM communication. Here is a comparison:

| Feature | CAN | PWM |
|---|---|---|
| Wires required | 2 (shared bus) | 3 per controller |
| Feedback (current, temp, velocity) | Yes | No |
| Configuration options | Full | Limited |
| Fault detection | Yes | No |
| Recommended for | All new robots | Legacy setups only |

CAN is strongly preferred for all motor controllers. Use PWM only when a CAN controller is not available or as a fallback.
