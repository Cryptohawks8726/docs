# Wiring Best Practices

Good wiring is the difference between a robot that is reliable all season and one that fails mid-match due to a loose connector or a wire caught in a mechanism. This page covers the habits and techniques that keep wiring clean, secure, and easy to debug.

---

## Cable Management

Poor cable management is one of the most common causes of robot problems. Loose wires can get snagged in mechanisms, connectors can vibrate out, and a messy wiring bay makes it very hard to trace a problem under pressure at a competition.

### Routing Wires
- Plan wire routes before cutting wire to length. Route wires along the frame rails and avoid cutting across open space where possible.
- Leave a small amount of slack in every run — about 2-3 inches at each end. Wires under tension will eventually pull free from their connectors, especially after repeated matches.
- Do not route wires through areas that move, flex, or are near rotating mechanisms. Use fixed frame members as guides.
- Keep power wires (high current) and signal wires (CAN, PWM, sensors) separated where possible. Running them together for long distances can introduce electrical noise into sensor readings.

### Securing Wires
- Use zip ties to bundle wires together and anchor them to the frame at regular intervals — roughly every 6-8 inches on longer runs.
- Do not over-tighten zip ties. They should hold the wire firmly without compressing the insulation. A zip tie that cuts into the insulation can damage the wire conductor over time.
- Trim the tails of zip ties flush with a diagonal cutter after tightening. Long tails are sharp and can scratch wires or people.
- Use zip tie anchor mounts (adhesive or screw-in) to create fixed attachment points on the frame when there is no convenient hole or bracket.

### Wire Labels
Label every wire at both ends. When you are debugging a problem at 11 PM at a competition, you do not want to spend 20 minutes tracing a wire to figure out what it is.
- Use label tape or pre-printed labels. Color-coded electrical tape is also helpful for identifying wire pairs quickly.
- A good labeling convention is to include the component name and the pin or channel number. For example: `LEFT_DRIVE_MC_PWR+` or `CAN_CHAIN_3`.

---

## Connector Types

Different connectors are used for different purposes on an FRC robot. Using the right connector for each application ensures a secure, reliable connection.

| Connector | Used For | Notes |
|---|---|---|
| Anderson SB50 | Battery leads | Requires a special crimping tool. Inspect the contact for full seating. |
| Wago lever nuts | PDH main power input terminals | Flip the orange lever open, insert wire, flip closed. |
| Weidmuller push-in | PDH channel outputs, radio power | Push the wire in firmly until it clicks. Tug to confirm it is secure. |
| JST-SH / JST-GH | Sensors, encoders, small signal connectors | Commonly used on REV and CTRE sensor ports. Be gentle — these are fragile. |
| RJ45 (ethernet) | Radio to RoboRIO connection | Use a quality cable. Avoid sharp bends near the plug. |
| Phoenix Contact (screw terminal) | Some older motor controllers | Tighten screw firmly but do not overtighten — can strip the terminal. |

### Keeping Connectors Secure
Vibration during a match is surprisingly aggressive. Connectors that seem solid on the bench can work loose after a few matches.
- Use small zip ties around connector pairs to prevent them from separating under vibration.
- For critical connections like motor controller power leads, consider using electrical tape or cable clamps in addition to zip ties.
- Always do a tug test on every connector after you make it. If it comes out with light force, re-seat it or investigate whether the terminal is properly crimped.

---

## Protecting Wires

### Split Loom and Cable Sleeves
When wires must pass through an area with rough edges, moving parts, or tight quarters, protect them with split loom tubing or braided cable sleeve. This prevents abrasion that can wear through insulation over time.
- Size split loom to the bundle — too small and you cannot fit all the wires in, too large and it flops around and may not stay in place.
- Secure the ends of split loom with a zip tie or electrical tape to prevent it from sliding.

### Grommets
Any time a wire passes through a hole in sheet metal or a frame extrusion, use a rubber grommet in the hole. Sheet metal edges are sharp enough to cut through wire insulation over time, especially with vibration. Grommets create a smooth, rounded surface for the wire to contact.

### Heat Exposure
- Avoid routing wires near motors or motor controllers, as they generate significant heat during operation. Sustained heat exposure degrades insulation.
- If you must route wires near a heat source, use high-temperature wire or add additional insulation like fiberglass sleeve.

---

## Electrical Tape and Heat Shrink

- **Electrical tape** is useful for bundling wires, covering exposed connectors, and adding a secondary layer of insulation. Do not rely on it as a substitute for proper insulation — it can unravel over time, especially in warm environments.
- **Heat shrink tubing** is the preferred way to insulate exposed solder joints or crimped connections. It creates a permanent, durable seal. Always use the correct diameter and apply heat evenly with a heat gun. Avoid using an open flame.

---

## General Habits

- **Cut wires to length.** Do not coil up excess wire and zip tie the bundle. Extra wire adds weight and creates a tangled mess that is hard to service. Measure, cut, and route precisely.
- **Use the right tool for each job.** Strip wire with a proper wire stripper. Crimp with a proper crimper. A sharp knife and pliers are not substitutes — they lead to damaged wires and poor connections.
- **Document your wiring.** Keep a wiring diagram or spreadsheet that lists every PDH channel, what is connected to it, the breaker size, and the CAN ID if applicable. This is invaluable during troubleshooting.
