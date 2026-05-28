# Wire Gauges

Using the correct wire gauge is one of the most important parts of safe robot wiring. Every wire on the robot carries a different amount of current depending on what it powers, and the wire must be thick enough to handle that current without overheating.

AWG (American Wire Gauge) is the standard used for wire sizing in FRC. A **smaller AWG number means a thicker wire** that can carry more current. For example, 6 AWG is much thicker than 18 AWG.

---

## Quick Reference Table

| Application | Wire Gauge | Notes |
|---|---|---|
| Battery to main breaker | 6 AWG | Must use lugs, not bare wire ends |
| Main breaker to PDH | 6 AWG | Must use lugs, not bare wire ends |
| Drive motor controllers (Falcon 500, Kraken, NEO) | 12 AWG | 40A breaker circuit |
| Smaller motor controllers (NEO 550, Minion) | 18 AWG | 20A or 30A breaker circuit |
| CAN bus wiring | 22 AWG (twisted pair) | Yellow = CAN High, Green = CAN Low |
| PWM signals | Pre-made cable (provided) | Do not substitute with random wire |
| Sensor wiring (encoders, limit switches) | 22-24 AWG | Low current, signal only |
| Radio power input | 18 AWG | 10A breaker circuit |
| RoboRIO power input | 18 AWG | 10A breaker circuit |

---

## Why Wire Gauge Matters

Every wire has a maximum current rating. If you push more current through a wire than it is rated for, the resistance of the wire causes it to heat up. At high enough temperatures the insulation melts, which can expose live conductors and cause a short circuit or fire.

This is why you should **never substitute a thinner wire** just because it is easier to route or already cut to the right length. If the required gauge is 12 AWG, use 12 AWG.

---

## High-Current Runs (6 AWG)

The battery-to-breaker and breaker-to-PDH connections are the highest current runs on the robot. Under peak load, the robot can draw well over 100A through these cables. 6 AWG wire is required for these runs, and the ends must be terminated with **crimp lugs** — not stripped and inserted bare into a terminal.

- Use a proper hydraulic or ratchet crimper for 6 AWG lugs. Hand crimpers will not create a reliable connection at this gauge.
- Inspect these connections regularly. A loose lug on a high-current run creates significant resistance, which causes heat buildup at the connection point.

---

## Motor Controller Wiring (12-18 AWG)

Motor controllers connect to the PDH on the input side and to the motor on the output side. The wire gauge depends on the breaker size protecting the circuit:

- **40A channels** (drive motors): Use **12 AWG**. This handles Falcons, Krakens, and NEOs at full load.
- **20A or 30A channels** (auxiliary motors): Use **18 AWG**. This is appropriate for smaller motors like the NEO 550 or Minion.

When in doubt, check the motor controller’s documentation for its recommended wire gauge on both the power input and motor output sides.

---

## CAN and Signal Wiring (22-24 AWG)

CAN bus wires carry only small communication signals, not power, so they can be thin. However, they must be **twisted together** (yellow and green as a pair) to reduce electromagnetic interference from nearby high-current wires.

Sensor wiring (encoders, limit switches, beam breaks) is also low-current and uses 22-24 AWG. These wires should be kept away from motor and power wiring where possible to prevent signal noise.

---

## Tips for Cutting and Stripping Wire

- Always use a proper wire stripper sized for the gauge you are working with. Using the wrong slot can nick the wire strands, weakening the connection.
- Strip only as much insulation as needed for the connector — leaving exposed conductor outside a terminal creates a short circuit risk.
- After crimping or inserting into a terminal, give the wire a firm tug to confirm it is secure.

> **Always check the FRC game manual and component documentation for required wire gauges.** Requirements can change between seasons, and using the wrong gauge can result in a failed inspection or a damaged robot.
