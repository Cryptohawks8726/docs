# Safety Rules

Electrical safety is one of the most important things to get right on an FRC robot. Mistakes with wiring can damage expensive components, cause fires, or injure team members. These rules must be followed by everyone working on the robot at any time — during build season, at practice, and at competition.

---

## Core Rules

### 1. Always Disconnect the Battery Before Wiring
Before touching any wire, connector, or electrical component, physically unplug the battery from the robot. It is not enough to flip the main breaker — you need to fully remove the battery connector. This ensures no current can flow through the robot while you are working on it.

> Even a "dead" battery still holds enough charge to cause arcing, burns, or component damage. Always disconnect it.

### 2. Never Work on an Enabled Robot
Never touch any part of the robot while it is enabled in the Driver Station — not even to adjust a wire or check a connector. An enabled robot can move unexpectedly and cause serious injury. Always disable the robot and confirm it is in a safe state before approaching it.

### 3. Never Short Positive and Negative Wires
A short circuit occurs when the positive and negative sides of a power circuit are connected directly, bypassing any load. This causes a massive surge of current that can instantly destroy motor controllers, the PDH, the RoboRIO, or other components — and can start a fire. Always be aware of where your positive (red) and negative (black) wires are when working near exposed terminals.

### 4. Use the Correct Wire Gauge
Every wire on the robot has a required gauge (thickness) based on how much current flows through it. Using wire that is too thin creates resistance, which generates heat. A wire carrying more current than it is rated for will overheat, melt its insulation, and potentially start a fire. Refer to the [Wire Gauges](wire_gauges.md) page for a full breakdown.

### 5. Keep Wiring Away from Moving Parts
Wires that run near gears, belts, chains, or rotating shafts can get caught and ripped out during operation. This can cause sudden loss of power to a subsystem, damage connectors, or create a short circuit. Always route wires away from moving mechanisms and secure them with zip ties.

---

## Additional Safety Habits

- **Label your wires at both ends.** If something goes wrong, knowing which wire goes where will save a lot of debugging time and prevent accidentally disconnecting the wrong thing.
- **Inspect wiring before every match.** Vibration from matches can loosen connectors and zip ties over time. Do a quick visual check before every test run or competition match.
- **Do not leave tools on the robot.** Screwdrivers, wire cutters, or other metal tools left on or near the robot can fall and cause a short.
- **Use insulated tools when possible.** When working near powered systems (such as when checking connections on a powered-off robot that may still have capacitor charge in motor controllers), insulated tools add an extra layer of protection.
- **Only one person wires at a time.** Having multiple people reaching into the electrical bay at once increases the chance of mistakes. One person works, others observe and assist.

---

## At Competition

At FIRST events, robots are inspected by referees before being allowed on the field. Electrical violations — such as exposed conductors, missing wire labels, or incorrect breaker sizes — can result in a failed inspection. Beyond inspection, unsafe wiring can cause a robot to break down mid-match or be disabled by the field system.

> These rules apply at all times: during build, testing, and competition. Safety is always the top priority.
