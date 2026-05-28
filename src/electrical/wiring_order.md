# Robot Wiring Order

When wiring a new robot (or rewiring after major mechanical changes), following a consistent order keeps the process organized and reduces the chance of mistakes. This order ensures that each step builds on the last — you are never connecting something before what it depends on is already in place.

> **Before you start:** Make sure the battery is disconnected and no one is powering the robot during the wiring process.

---

## Step 1 — Mount the Core Electrical Components

Before running any wire, mount all of the main electrical components to the robot frame in their final positions. This lets you plan wire routes accurately and cut wires to the correct length.

Components to mount:
- **PDH** (Power Distribution Hub) — Central location on the frame, accessible from the outside.
- **RoboRIO** — Secure location, protected from direct hits, accessible for USB connection during programming.
- **Main breaker** — Must be accessible from the outside of the robot frame per FRC rules.
- **Radio (VH-109)** — Elevated location where its indicator lights are visible. Avoid metal enclosures that block Wi-Fi signal.
- **Motor controllers** — Mounted near the motors they control to keep motor output wires short.

---

## Step 2 — Run the Main Power Cables

This is the backbone of the robot’s electrical system. These are the highest-current wires on the robot and must be done first.

1. Connect the battery to the **main breaker IN** terminal using 6 AWG wire with crimped lugs.
2. Connect the **main breaker OUT** terminal to the **PDH positive input** using 6 AWG wire with crimped lugs.
3. Connect the **battery negative** directly to the **PDH negative input** using 6 AWG wire with crimped lugs.

> Do not connect the battery yet. Route and secure these cables, but leave the battery disconnected until all wiring is complete and inspected.

---

## Step 3 — Wire RoboRIO Power

The RoboRIO must have a dedicated 10A breaker channel on the PDH.

1. Select a 10A breaker channel on the PDH and insert a 10A snap-action breaker.
2. Run 18 AWG wire from that PDH channel to the RoboRIO power input terminals (marked + and -).
3. Connect the wires to the RoboRIO’s Weidmuller power input. Red (+) to the positive terminal, black (-) to the negative terminal.
4. Tug both wires to confirm they are fully seated.

---

## Step 4 — Wire Radio Power

The VH-109 radio is powered directly from a PDH channel, not through a VRM. This is an important distinction from older FRC radio setups.

1. Select a 10A breaker channel on the PDH and insert a 10A breaker.
2. Run 18 AWG wire from that PDH channel to the **Weidmuller DC input** on the VH-109.
3. Do **not** use the VRM (Voltage Regulator Module) to power the radio. The VH-109 requires a direct PDH connection.

---

## Step 5 — Connect RoboRIO to Radio

1. Run a quality ethernet cable from the **RoboRIO ethernet port** to the port labeled **"RIO"** on the VH-109.
2. Use the correct port — plugging into the wrong port on the radio can prevent communication or damage the radio.
3. Secure the ethernet cable with zip ties so it cannot be pulled loose during a match.

---

## Step 6 — Wire Motor Controllers

For each motor controller on the robot:

1. Select a PDH breaker channel appropriate for the motor (40A for drive motors, 20-30A for smaller motors).
2. Insert the correct snap-action breaker for that channel.
3. Run the appropriate gauge power wire (12 AWG for 40A channels, 18 AWG for smaller channels) from the PDH channel to the motor controller power input.
4. Connect the motor output wires from the controller to the motor terminals.
5. If the motor controller uses **CAN**, leave the CAN connections for Step 7.
6. If the motor controller uses **PWM**, connect a PWM cable from the controller signal input to the appropriate PWM header on the RoboRIO.

Repeat this for every motor controller on the robot before moving on.

---

## Step 7 — Run the CAN Bus Chain

CAN devices must be wired in a daisy chain from the RoboRIO to the PDH. The PDH must be at the end of the chain because it has the built-in terminator.

1. Start at the **RoboRIO CAN port** (yellow = CANH, green = CANL).
2. Run CAN wire to the first device in the chain, connecting CANH to CANH and CANL to CANL.
3. Continue daisy-chaining through each CAN device (motor controllers, Pneumatic Hub, etc.).
4. End the chain at the **PDH CAN port**. The PDH’s built-in terminator closes the bus.
5. Twist yellow and green wires together for any run longer than about 6 inches to reduce interference.

After completing the chain, use REV Hardware Client and Phoenix Tuner X to **assign unique CAN IDs** to every device before powering the full system. See [CAN Bus](can_bus.md) for details.

---

## Step 8 — Connect Signal Cables (PWM, Sensors, Encoders)

With the power and CAN wiring done, connect all remaining signal cables.

- **PWM cables:** Connect from motor controllers to the RoboRIO PWM headers. Match the signal pin to the correct header number as defined in your code.
- **Encoders and sensors:** Connect sensor signal cables to the appropriate DIO, AIO, or sensor ports on the RoboRIO.
- **Limit switches and beam breaks:** Wire to the DIO ports on the RoboRIO.
- Route all signal cables away from high-current power wires to reduce noise.

---

## Step 9 — Label Every Wire and Secure Cable Runs

Before doing a final inspection, label every wire at both ends and secure all cable runs with zip ties.

- Labels should identify the component and connection point (e.g., `LEFT_FRONT_DRIVE_+` or `CAN_CHAIN_2`).
- Use zip tie anchor mounts to secure wire bundles to the frame at regular intervals.
- Trim all zip tie tails flush.
- Check that no wires cross near gears, belts, or other moving mechanisms.

---

## Step 10 — Visual Inspection Before Connecting the Battery

Before connecting the battery, do a thorough visual inspection:

- Check every power connection for correct polarity (red = +, black = -).
- Check that all PDH breakers are installed and seated properly.
- Confirm all CAN connections are secure and the chain runs from the RoboRIO to the PDH.
- Confirm no bare wire conductors are exposed outside of connectors.
- Confirm no wires are near moving mechanisms.
- Verify that the main breaker is in the OFF (popped/reset) position before connecting the battery.

Once the inspection passes, connect the battery and power on the robot to test communication.
