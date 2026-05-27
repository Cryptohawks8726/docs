(Needs proofreading! Written by: Keshav)

# Previous Unorthodox Control Fixes 

Obviously when working on an FRC team, not all the solutions that we have are "clean" or "regular" but there is a lot that can be learnt and this is documented so when potentially similar problems in the future, we can look back into the past and see how the team has handled them. Some of these solutions are also really funny and are being documented just to remember and look back at the times we made such wonky solutions that some how just worked. The goal is to keep this updated as we continue as a FRC team and solve more problems with our control systems.

## FRC 2026: Rebuilt

### The \\(k_Spring\\) Incident

#### Background Info and The Problem

Our 2026 Robot, LeJohn James, had a turret. Originally for wiring the turret, we planned on having an e-chain which would have the wires inside of it and would be wrapped inside the turret. After 3d-printing 2 potential e-chain solutions, we realized that it really wasn't working due to some mechanical constraints. In order to successfully have a turret, we would have to have an e-snake on a different board which was attached to a very very strong spring because we didn't have any flexable wires and a weaker spring wasn't able to provide the tension we needed to rotate in both directions. This spring moved our turret irregularly due to its strength so in order to counter its effect on our system, we created the feedforward constant: \\(k_Spring\\). 

#### The Solution, How it Worked, & Learnings

In order to have an accurate turret, we couldn't have its position fluctuating due to an external force(the spring) which was unnaccounted for. The only way to account for the force of the spring would be some type of feedforeward. The spring was a constant force spring but the force required to move it was not constant because the angle at which the cable was being pulled changed and so the force required changed with the sine of the angle from 0. You can see the actual code [here](https://github.com/Cryptohawks8726/FRC-8726-2026/blob/main/src/main/java/frc/robot/subsystems/TurretShooterSubsystem.java#L302) to see how we calculated the voltage required to counteract the spring force. The actual numbers and specific math we used for this isn't that important but we learnt a lot from this unorthodox solution. We learnt that feedforward can be used to counter other forces that would otherwise mess with our PID and remove accuracy. Also - just as a side note - we did have a backup plan in case the math didn't work like this and a constant like \\(k_Spring\\) didn't help us as much. That was to do a lookup table using the angle of the turret as an input and the voltage required to keep the current in place based on the spring force as an output. While this would have taken more time to tune, I am mentioning it in the docs here in case a situation like this does arrise in the future and the system moves more irregularly due to the external force.
