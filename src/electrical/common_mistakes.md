# Common Wiring Mistakes

Every electrical team makes mistakes, especially early in build season. This page documents the most common wiring errors seen in FRC robots, explains why they happen, and tells you how to avoid or fix them.

---

## Reversed Polarity

**What it is:** Connecting the positive (+) wire to a negative terminal, or the negative (-) wire to a positive terminal.

**Why it happens:** Wires are cut and labeled incorrectly, or color conventions are not followed consistently. It is most common on motor output wires where both leads look similar.

**What happens:** Reversed polarity on motor controllers can destroy them instantly. Some controllers have protection against this, but many do not. On motors, reversed polarity causes the motor to spin backwards — which may not be immediately obvious and can cause mechanical damage if a mechanism is driven in the wrong direction.

**How to avoid it:**
- Always use red for positive and black for negative on power wires. Do not improvise.
- Double-check polarity before connecting any power cable.
- Use a multimeter to verify polarity at both ends of a run before connecting.
- Label both ends of every wire as part of the wiring process, not as an afterthought.

---

## Wrong Breaker Size

**What it is:** Installing a breaker that is too large or too small for the circuit it is protecting.

**Why it happens:** Teams grab whatever breaker is available without checking the spec, or assume that a larger breaker is "safer" because it is less likely to trip.

**What happens:** A breaker that is too large will not trip when it should, meaning the wire it protects can overheat and potentially start a fire before the breaker responds. A breaker that is too small will trip repeatedly during normal operation, causing unexpected robot shutdowns during matches.

**How to avoid it:**
- Refer to the [Wire Gauges](wire_gauges.md) page and the FRC game manual for correct breaker sizes.
- Do not substitute breaker sizes based on what you have in stock. Order the correct size.
- Label each PDH channel with the breaker size and what it powers.

---

## Duplicate CAN IDs

**What it is:** Two or more devices on the CAN bus assigned the same ID number.

**Why it happens:** Motor controllers from the factory often default to ID 0 or ID 1. If you add multiple controllers to the bus without configuring them individually, they will all have the same ID.

**What happens:** Devices with duplicate IDs will conflict on the bus. One or both devices may stop responding, behave erratically, or cause CAN bus errors that affect all other devices on the bus. This is one of the most common causes of mysterious motor failures at competition.

**How to avoid it:**
- Set CAN IDs **before** connecting a new device to the CAN bus. Configure it alone first.
- Use REV Hardware Client for SPARK MAX and SPARK Flex.
- Use Phoenix Tuner X for CTRE devices (Talon FX, Talon FXS, Talon SRX, Victor SPX).
- Keep a written record (spreadsheet or wiring diagram) of every CAN ID and what device it belongs to.
- Check the CAN bus status in the Driver Station before every match. Zero errors is the goal.

---

## Unsecured Connectors

**What it is:** Connectors that are not physically anchored and can vibrate or pull loose.

**Why it happens:** During wiring, connectors are seated and tested on a stationary bench. Vibration from a match is very different from static conditions. A connector that feels solid on the bench can work loose after one or two matches.

**What happens:** An unsecured connector can pull free during a match, cutting power or communication to a motor or the RoboRIO. This often looks like a random failure with no obvious cause.

**How to avoid it:**
- Zip-tie connectors together or anchor them to the frame so they cannot separate.
- For Weidmuller push-in connectors, give every wire a firm tug after insertion to confirm it is locked.
- For Anderson SB50 battery connectors, inspect the spring contact inside for proper tension.
- After every match, do a quick tug test on critical connectors.

---

## Wires Cut Too Short

**What it is:** Wires that are too short and are pulled tight between their endpoints.

**Why it happens:** Wires are measured and cut when the robot is in a specific configuration. When the robot moves or a mechanism shifts, the slack disappears and the wire is stretched tight.

**What happens:** A wire under constant tension will eventually fatigue and break at the connector crimp or terminal. This usually happens during or just before a match and can be very difficult to diagnose because the break may not be visible.

**How to avoid it:**
- Always leave 2-3 inches of slack at each end of every wire run.
- Before finalizing wire routes, move the robot through its full range of motion and verify that no wire becomes tight.
- Pay special attention to wires that cross between the robot frame and a moving mechanism (like a rotating arm or elevator).

---

## No Wire Labels

**What it is:** Wires that are not labeled, making it impossible to identify them without tracing the full run.

**Why it happens:** Labeling feels slow and tedious, especially when the team is rushing to get the robot ready. Labels get skipped with the intention of doing them later, which often never happens.

**What happens:** When a problem occurs (and problems always occur), unlabeled wiring makes debugging dramatically slower. At a competition with limited time between matches, spending 20 minutes tracing a wire can cost you a match.

**How to avoid it:**
- Make labeling a required part of the wiring process. Do not consider a wire finished until it is labeled at both ends.
- Use a consistent naming convention so labels are immediately meaningful (e.g., `LEFT_REAR_DRIVE_+`, `CAN_TO_PDH_H`).
- Even simple color-coded tape on bundles helps when the full label is not readable at a glance.

---

## Wrong Radio Ethernet Port

**What it is:** Plugging the RoboRIO ethernet cable into the wrong port on the VH-109 radio.

**Why it happens:** The VH-109 has multiple ethernet ports and they are not all the same. The port for the RoboRIO is specifically labeled "RIO" and it is the only port that should be used for this connection.

**What happens:** Using the wrong port will prevent robot communication. In some cases it can also damage the connected device. The robot will appear to have no communication even though the radio is powered and the ethernet cable is connected.

**How to avoid it:**
- Always connect to the port labeled **"RIO"** on the VH-109.
- Mark the correct port with a small piece of colored tape after initial setup so it is always obvious which port to use.

---

## Running Wires Near Moving Parts

**What it is:** Routing a wire through or near a mechanism that moves, rotates, or flexes during operation.

**Why it happens:** Space is tight on FRC robots, and the shortest path for a wire often crosses through an area that a mechanism also uses. During initial wiring it may not be obvious that a mechanism will reach a wire once the robot is running.

**What happens:** The mechanism catches the wire, either ripping it out at the connector, cutting through the insulation, or breaking the wire. This can cause a sudden loss of power or a short circuit.

**How to avoid it:**
- Run the robot through its full range of motion before finalizing wire routes.
- Use flexible wire (stranded, not solid core) for any run that must cross near a moving mechanism.
- When a wire must pass near a moving part, use split loom to protect it and secure it so it cannot be pulled into the mechanism.
