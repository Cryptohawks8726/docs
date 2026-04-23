# Introduction to Control Theory

## Necessary Vocab

**System**: The thing you are trying to control. Examples: Climber, Shooter, Turret, Elevator, Arm, etc.

**States**: All systems have states. A state is simply the property of the system that you are trying to control. Examples: position of a turret,
velocity of a flywheel, height of an elevator, etc.

**Inputs**: Inputs are features of a system that have an ability to change its state. For FRC control, voltage is (almost always) the input
that we are controlling and it can change the state of the system.

**Measurement**: Also known as outputs, these are the states of a system as understood by sensors.
Sensors can be inaccurate, so the actual state may be hard to determine.
Measurements are usually estimates based on data, and are usually good enough for FRC.
We use a variety of sensors to get information but primarily [motor encoders](../parts/encoders.md).

**Setpoint**: Also known as references, this is the desired state of a system.
Examples: desired RPM of a flywheel, desired height of an elevator.

**Error**: Error is the difference between the setpoint and the measurement in the system.
For example, if a flywheel is at 2000RPM and its setpoint is 8726RPM then the error is \\( 8726 RPM - 2000 RPM = 6726 RPM \\).

**Control Algorithm**: A control algorithm will, using the setpoint and measurement, produce an input to feed into the system.
The input will affect the state of the system. A good control algorithm minimizes the error as quickly
as possible. However, moving _too_ quickly may result in overshooting the setpoint, so a trade-off must be made which may vary from system to system.

## States we Typically Control in FRC

### Position Control

This is a very common state that we control in FRC applications. Climbers, elevators, turrets, and arms are all clasified as position control.
This is because the setpoint and measurements that we are dealing with are both positions.
We have one position and want to go to another position so we are controlling the position and need position control.

> [!NOTE]
> Note on position control with angles:
>
> When a system is moving in circles, such as a turret, it sometimes be more effiecient
> to go in the opposite direction (since it's a circle). This also varies with the mechanical constraints of the system.

### Velocity Control

This is also very common to control in FRC. Flywheels, rollers, and drivetrains all rely on velocity control.
This is because the setpoint and measurements that we are dealing with are both velocities. Our goal is to reach a certain velocity,
which can be seen most obviously in flywheels.
