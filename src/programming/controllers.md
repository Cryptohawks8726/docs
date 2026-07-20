(Needs proofreading! Written by: Keshav)

# Using WPILib's Controller Classes

The [Control Theory chapter](../theory/theory.md) explains how feedback and feedforward work. This page focuses on implementing the most common controllers in WPILib Java:

- `PIDController`
- `SimpleMotorFeedforward`, `ElevatorFeedforward`, and `ArmFeedforward`
- `BangBangController`

These classes calculate outputs. They do not automatically control a motor, read a sensor, enforce limits, or choose units. Our subsystem is still responsible for collecting the measurement, calling the controller, and safely applying its output.

# `PIDController`

`PIDController` is WPILib's basic feedback controller. It compares a measurement to a setpoint and returns a correction based on the error.

## Constructing the Controller

```java
import edu.wpi.first.math.controller.PIDController;

private final PIDController controller =
    new PIDController(kP, kI, kD);
```

The three constructor arguments are the proportional, integral, and derivative gains.

WPILib assumes the controller is called every 20 milliseconds by default. If the loop intentionally runs at a different fixed period, provide that period:

```java
private final PIDController controller =
    new PIDController(kP, kI, kD, 0.01);
```

The example above represents a 10-millisecond loop. Do not change the period just because one robot loop happened to take slightly longer. It should describe the controller's intended regular update period.

## Calculating an Output

The normal argument order is measurement first and setpoint second:

```java
double feedbackVolts = controller.calculate(
    measuredPositionMeters,
    desiredPositionMeters
);
```

The result has whatever output units the gains create. On our robots, we normally tune the gains so the result represents volts:

```java
motor.setVoltage(feedbackVolts);
```

Calling `calculate()` does not apply the output by itself.

For velocity control:

```java
double feedbackVolts = controller.calculate(
    measuredVelocityRadPerSec,
    desiredVelocityRadPerSec
);
```

Measurement and setpoint must use the same units.

## Storing a Setpoint

The setpoint can be passed into every calculation:

```java
controller.calculate(measurement, setpoint);
```

It can also be stored:

```java
controller.setSetpoint(setpoint);
double output = controller.calculate(measurement);
```

Both approaches work. Passing both arguments is often easier to read because the source of the setpoint is visible at the calculation.

## Tolerance and `atSetpoint()`

Tolerance defines how close the controller must be before we consider it at the setpoint:

```java
controller.setTolerance(
    0.01, // Position-error tolerance in meters
    0.05  // Error-derivative tolerance in meters per second
);
```

After calling `calculate()`:

```java
if (controller.atSetpoint()) {
    // The latest error and error derivative are within tolerance.
}
```

Using both tolerances prevents a command from finishing merely because the mechanism passed through the correct position at high speed.

Tolerance only affects `atSetpoint()`. It does not create a deadband or force the controller output to zero.

## Reading Controller State

Useful diagnostic methods include:

```java
controller.getSetpoint();
controller.getError();
controller.getErrorDerivative();
controller.atSetpoint();
```

Log these alongside the actual measurement and output. Looking only at motor voltage makes it difficult to tell whether the problem began with the goal, sensor, controller, or actuator.

## Continuous Input

Angles sometimes wrap. For a mechanism that can safely rotate through the wrap point:

```java
controller.enableContinuousInput(
    -Math.PI,
    Math.PI
);
```

The controller will recognize that `179°` and `-179°` are only two degrees apart.

Do not enable continuous input for a mechanism whose wires or hard stops prevent continuous rotation. The mathematically shortest path might be physically unsafe.

## Resetting

```java
controller.reset();
```

Reset clears accumulated integral error and the controller's stored derivative information. Reset when starting a logically new control action or after a change that makes the previous error history invalid.

Do not reset every robot loop. Doing so prevents the integral and derivative terms from working correctly.

## Limiting Integral

Most of our FRC mechanisms should use feedforward for known forces such as gravity and static friction instead of relying on integral. If integral is actually necessary, limit it:

```java
controller.setIZone(0.05);
controller.setIntegratorRange(-1.0, 1.0);
```

`setIZone()` clears or prevents accumulation when error is too large. `setIntegratorRange()` limits the integral term's contribution to the output.

These tools reduce windup, but they do not fix incorrect units, an impossible setpoint, voltage saturation, or missing feedforward.

# WPILib Feedforward Classes

Feedforward calculates the voltage a mechanism should need based on its desired movement and physical model. It does not compare a measurement to a setpoint.

WPILib provides three common feedforward classes:

```java
import edu.wpi.first.math.controller.ArmFeedforward;
import edu.wpi.first.math.controller.ElevatorFeedforward;
import edu.wpi.first.math.controller.SimpleMotorFeedforward;

SimpleMotorFeedforward simple =
    new SimpleMotorFeedforward(kS, kV, kA);

ElevatorFeedforward elevator =
    new ElevatorFeedforward(kS, kG, kV, kA);

ArmFeedforward arm =
    new ArmFeedforward(kS, kG, kV, kA);
```

The returned output is in volts when the constants use the correct voltage-based units.

## `SimpleMotorFeedforward`

Use `SimpleMotorFeedforward` for mechanisms whose model does not need gravity compensation, such as:

- Flywheels
- Rollers
- Horizontal drivetrains
- Other simple rotating mechanisms

```java
double feedforwardVolts =
    flywheelFeedforward.calculate(
        desiredVelocityRadPerSec
    );
```

When desired acceleration is known:

```java
double feedforwardVolts =
    flywheelFeedforward.calculate(
        desiredVelocityRadPerSec,
        desiredAccelerationRadPerSecSq
    );
```

The class accounts for:

- \(k_S\): static friction
- \(k_V\): voltage needed for velocity
- \(k_A\): voltage needed for acceleration

## `ElevatorFeedforward`

Use `ElevatorFeedforward` for vertical linear mechanisms where gravity acts with approximately constant force:

```java
double feedforwardVolts =
    elevatorFeedforward.calculate(
        desiredVelocityMetersPerSecond
    );
```

With desired acceleration:

```java
double feedforwardVolts =
    elevatorFeedforward.calculate(
        desiredVelocityMetersPerSecond,
        desiredAccelerationMetersPerSecondSq
    );
```

Its \(k_G\) term supplies the voltage needed to oppose gravity. This allows the mechanism to hold or move without requiring a permanent PID error just to generate supporting voltage.

## `ArmFeedforward`

Use `ArmFeedforward` for rotating arms whose gravity load changes with angle:

```java
double feedforwardVolts =
    armFeedforward.calculate(
        desiredAngleRadians,
        desiredVelocityRadPerSec
    );
```

With desired acceleration:

```java
double feedforwardVolts =
    armFeedforward.calculate(
        desiredAngleRadians,
        desiredVelocityRadPerSec,
        desiredAccelerationRadPerSecSq
    );
```

The angle must use the same convention assumed while determining \(k_G\). WPILib's arm model expects the gravity calculation to be based on an angle measured from the horizontal reference used by the model.

An incorrect encoder zero can make a correct \(k_G\) value apply gravity compensation in the wrong direction.

## Feedforward Units

Java does not enforce feedforward units. The constants and inputs must agree.

If SysId produced gains using radians per second:

- Velocity must be radians per second.
- Acceleration must be radians per second squared.
- Arm angle must be radians.

Do not pass RPM into a feedforward created with radians-per-second constants.

# Combining PID and Feedforward

PID and feedforward solve different parts of the problem:

- Feedforward supplies the expected voltage for the mechanism's physics.
- PID corrects the remaining difference between the desired and measured state.

Add their voltage outputs:

```java
double feedbackVolts = pid.calculate(
    measuredPosition,
    desiredPosition
);

double feedforwardVolts = armFeedforward.calculate(
    desiredPosition,
    desiredVelocity,
    desiredAcceleration
);

double requestedVolts =
    feedbackVolts + feedforwardVolts;

motor.setVoltage(
    MathUtil.clamp(
        requestedVolts,
        -MAX_VOLTS,
        MAX_VOLTS
    )
);
```

Use `setVoltage()` instead of percent output because both controller results represent volts.

For the PD-plus-feedforward approach described in [Using Feedback and Feedforward Together](../theory/fb_and_ff.md), construct the PID with zero integral:

```java
private final PIDController feedback =
    new PIDController(kP, 0.0, kD);
```

Then use \(k_G\) and \(k_S\) in feedforward to handle predictable gravity and friction rather than waiting for \(k_I\) to accumulate steady-state error.

# `BangBangController`

`BangBangController` is an asymmetric velocity controller. Its output is:

- `1.0` when the measurement is below the setpoint
- `0.0` when the measurement is at or above the setpoint

This makes it extremely aggressive in one direction and passive in the other.

## When to Use It

Bang-bang control is primarily useful for high-inertia shooter flywheels:

- The wheel should accelerate as quickly as possible.
- The wheel naturally slows from friction when output becomes zero.
- Rapid recovery after shooting a game piece matters.
- Small overspeed can safely coast away.

Do not use it for position-controlled arms, elevators, turrets, drivetrains, or low-inertia mechanisms.

> [!WARNING]
> Configure the motor controllers in **coast mode** before using WPILib's asymmetric bang-bang controller. Brake mode actively opposes coasting and can create destructive oscillation.

## Constructing and Calculating

```java
import edu.wpi.first.math.controller.BangBangController;

private final BangBangController controller =
    new BangBangController();
```

```java
double output = controller.calculate(
    measuredVelocityRadPerSec,
    desiredVelocityRadPerSec
);

motor.setVoltage(output*12);
```

There are no \(k_P\), \(k_I\), or \(k_D\) gains to tune.

## Tolerance

Tolerance is used by `atSetpoint()`:

```java
controller.setTolerance(
    SUBSYSTEM_CONSTANTS.MOTOR67_TOLERANCE
);

if (!controller.atSetpoint()) {
    motor.setVoltage(controller.calculate(
        measuredVelocity,
        desiredVelocity
    ) * 12)
}
else {
    // idk meow bro
}
```

Tolerance does not change the controller's on/off output. For a shooter-ready condition, require the wheel to remain within tolerance for several loops so one noisy measurement does not feed a game piece early.

## Combining Bang-Bang and Feedforward

Feedforward can supply most of the voltage required to maintain speed, while bang-bang adds a strong correction whenever the flywheel falls below its target:

```java
double bangBangVolts =
    bangBang.calculate(
        measuredVelocity,
        desiredVelocity
    ) * 12.0;

double feedforwardVolts =
    flywheelFeedforward.calculate(
        desiredVelocity
    );

double requestedVolts =
    bangBangVolts
    + 0.9 * feedforwardVolts;

motor.setVoltage(
    MathUtil.clamp(
        requestedVolts,
        0.0,
        12.0
    )
);
```

The feedforward is reduced slightly in this example because bang-bang cannot actively correct overspeed. If feedforward alone maintains a speed above the setpoint, the controller can only wait for the flywheel to coast down.

The `0.9` value is only an example. Test and tune it on the real mechanism.

See [Bang-Bang Controllers](../theory/bbc.md) for the complete theory, limitations, and tuning procedure.

# Controller Placement in a Subsystem

An arm using PID and feedforward may look like:

```java
public class ArmSubsystem extends SubsystemBase {
    private final PIDController feedback =
        new PIDController(kP, 0.0, kD);

    private final ArmFeedforward feedforward =
        new ArmFeedforward(kS, kG, kV, kA);

    private double desiredAngleRadians;
    private double desiredVelocityRadPerSec;

    @Override
    public void periodic() {
        double measuredAngle =
            getAngleRadians();

        double feedbackVolts =
            feedback.calculate(
                measuredAngle,
                desiredAngleRadians
            );

        double feedforwardVolts =
            feedforward.calculate(
                desiredAngleRadians,
                desiredVelocityRadPerSec
            );

        motor.setVoltage(
            MathUtil.clamp(
                feedbackVolts + feedforwardVolts,
                -MAX_VOLTS,
                MAX_VOLTS
            )
        );
    }
}
```

A shooter using bang-bang and feedforward follows the same structure but uses measured and desired velocity instead of arm position.

# Tuning Workflow

## PID and Feedforward

1. Verify motor and sensor directions.
2. Verify units and controller period.
3. Tune or identify feedforward.
4. Test feedforward over the mechanism's operating range.
5. Set PID gains to zero.
6. Increase \(k_P\) until errors correct without unacceptable oscillation.
7. Add \(k_D\) when damping is needed.
8. Add \(k_I\) only for a remaining unmodeled steady-state error.
9. Test the full mechanism range and real loads.

## Bang-Bang and Feedforward

1. Verify velocity direction and units.
2. Put motors in coast mode.
3. Begin with a safe low velocity.
4. Tune feedforward below the amount that causes overspeed.
5. Test full-power acceleration and coasting.
6. Test recovery by safely shooting a real game piece.
7. Choose and stabilize the shooter-ready tolerance.

# What to Log

For every controller, log:

- Setpoint
- Measurement
- Error
- Output(with components if needed)
- Total requested voltage
- Applied voltage
- Current
- `atSetpoint()` state


# Additional Resources

- [WPILib PIDController](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/controllers/pidcontroller.html)
- [WPILib Feedforward](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/controllers/feedforward.html)
- [WPILib BangBangController](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/controllers/bang-bang.html)
