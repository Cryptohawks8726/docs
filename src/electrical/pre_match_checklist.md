# Pre-Match Checklist

This checklist should be run before every match and every significant test session. Electrical problems that are caught in the pits are much easier to fix than ones that show up on the field. Make this a routine — it only takes a few minutes and can prevent a lost match.

> **Tip:** Assign one person on the drive team or pit crew to own this checklist. It should be completed by the same person every time so nothing gets missed.

---

## Battery

- [ ] Battery is fully charged (check voltage with a voltmeter or the DS — should be 12.5V or above at rest)
- [ ] Battery connector (Anderson SB50) is fully seated and locked into the robot
- [ ] Battery is physically secured in its mount and cannot shift during a match
- [ ] No other batteries are sitting loose in the robot

---

## Main Breaker

- [ ] Main breaker is accessible from the outside of the robot frame
- [ ] Main breaker is in the ON (reset) position — button is not popped out
- [ ] Main breaker wiring (6 AWG lugs) is tight and not pulling away from the terminals

---

## Power Distribution Hub (PDH)

- [ ] All snap-action breakers are fully seated in their channels (press each one down firmly)
- [ ] No breakers are tripped (check the LED indicators on the PDH if visible)
- [ ] PDH mounting bolts are tight — a loose PDH can cause intermittent power issues
- [ ] PDH to RoboRIO and PDH to radio power wires are secure (tug test)

---

## Wiring and Connectors

- [ ] No loose wires or connectors visible anywhere on the robot
- [ ] No wires are crossing near gears, belts, chains, or rotating shafts
- [ ] No wire bundles are pulled tight or under tension
- [ ] All motor controller power connectors are secure (tug each one)
- [ ] All motor output connections are secure
- [ ] Ethernet cable between RoboRIO and radio is firmly seated at both ends

---

## CAN Bus

- [ ] Driver Station shows **0 CAN Bus errors** (check the DS diagnostics tab)
- [ ] CAN utilization shown in the DS is below 90%
- [ ] All expected CAN devices are visible and responding (verify in REV Hardware Client or Phoenix Tuner X if there is time)

---

## Radio

- [ ] Radio indicator lights show the correct status for field connection (solid green or blue depending on the event)
- [ ] Radio is physically secured and not at risk of being knocked loose
- [ ] Ethernet cable is connected to the correct **"RIO"** port on the VH-109

---

## Driver Station

- [ ] Driver Station shows **green communication** with the robot
- [ ] No robot code errors or warnings in the DS log
- [ ] Robot mode is set to **Teleop** (or the correct mode for the situation) before enabling
- [ ] Joysticks and controllers are recognized and responding correctly in the DS
- [ ] Battery voltage reading in the DS is reasonable (12.0V or above when idle)

---

## Final Physical Check

- [ ] All access panels, bumper brackets, and covers that were moved during pit work are properly secured
- [ ] No tools, loose hardware, or foreign objects are sitting inside the robot
- [ ] Robot is in its starting configuration for the match

---

## After a Match — Quick Damage Check

Run this quick check after every match before the robot goes back to the pit:

- [ ] No smoke, burning smell, or visible scorch marks anywhere on the robot
- [ ] No wires visibly pulled out or dangling
- [ ] No breakers tripped on the PDH
- [ ] Main breaker is still in the ON position
- [ ] Battery connector is still fully seated

If any of these fail, do not run the robot again until the issue is found and resolved.
