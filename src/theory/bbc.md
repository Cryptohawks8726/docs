(Needs proofreading! Written by: Keshav)

# Bang-Bang Controllers

## What is a Bang-Bang Controller?

A bang-bang controller is one of the simplest feedback controllers we use in FRC. Instead of calculating a different output for every possible error like a PID does, it switches between two outputs. For the version provided by WPILib, those outputs are fully on and fully off.

For a flywheel with a velocity setpoint, the logic is basically:

- If the measured velocity is below the setpoint, output full power.
- If the measured velocity is at or above the setpoint, output no power.

In equation form, this can be written as

\\[
u =
\begin{cases}
1, & \text{measurement} < \text{setpoint} \\\\
0, & \text{measurement} \geq \text{setpoint}
\end{cases}
\\]

The name comes from the controller rapidly switching, or "banging," between its two possible outputs. You can think of it as an extremely aggressive proportional controller, except it only corrects in one direction.

## Why Would We Use One?

At first, full power or no power sounds like a terrible way to control a mechanism. For most mechanisms, it is. Bang-bang control is useful because a few FRC mechanisms have a large amount of inertia and naturally slow down when power is removed.

The most common example is a shooter flywheel. We want the wheel to reach its target speed as quickly as possible, and after a game piece is shot we want it to recover that speed quickly. Applying full power whenever the flywheel is too slow gives us the fastest possible acceleration. When the flywheel is too fast, the controller turns off and lets friction, air resistance, and the next shot slow it down.

This response is **asymmetric**:

- The controller can actively speed the mechanism up.
- It cannot actively slow the mechanism down.

That asymmetry is what keeps it from continuously applying full power in opposite directions and creating destructive oscillations.

## When to Use Bang-Bang Control

Bang-bang control is a good option when all of the following are true:

- We are controlling **velocity**, not position.
- The mechanism has enough inertia to coast when power is removed.
- Getting up to speed and recovering from a disturbance quickly are more important than having a perfectly smooth output.
- Overspeed can safely disappear through the mechanism's natural friction.

Shooter flywheels are the primary FRC use case. A bang-bang controller may also work for another high-inertia wheel, but that should be considered carefully instead of assuming every velocity-controlled mechanism needs one.

## When Not to Use It

Do not use a bang-bang controller for an arm, elevator, turret, drivetrain position, or any mechanism that can slam into a hard stop. These mechanisms usually need the controller to apply different amounts of effort in both directions and slow down before reaching their target.

Bang-bang control is also a poor choice for a low-inertia mechanism. If the mechanism can speed up and slow down almost instantly, the output may switch rapidly and create oscillation, current spikes, heat, and unnecessary mechanical stress.

If accuracy, smooth motion, or controlled deceleration matters, use a PID controller and usually a motion profile instead.

> [!WARNING]
> A WPILib bang-bang controller should only be used when the motor controllers are configured to **coast mode**. In brake mode, the motor controller actively resists motion whenever the bang-bang output becomes zero. This fights the intended coasting behavior and can cause violent oscillation.

## Using `BangBangController` in WPILib

WPILib provides a `BangBangController` class. It has no gains to tune because its output is always either `1.0` or `0.0`.

A basic Java example looks like this:

```java
private final BangBangController controller = new BangBangController();

public void periodic() {
    double output = controller.calculate(
        flywheelEncoder.getVelocity(),
        targetVelocity
    );

    flywheelMotor.set(output);
}
```

As with every controller, the measurement and setpoint must use the same units. If the encoder reports rotations per minute, the setpoint must also be in rotations per minute. If the encoder reports rotations per second, the setpoint must use rotations per second.

The controller can also be given a tolerance:

```java
controller.setTolerance(50.0);

if (controller.atSetpoint()) {
    // The flywheel is within 50 RPM of the target.
}
```

Tolerance does not change the output of the controller. It only changes when `atSetpoint()` returns true. For a shooter, being "ready" may also require the velocity to remain within tolerance for several loops so one noisy measurement does not release a game piece too early.

## Combining Bang-Bang with Feedforward

A bang-bang controller works best when feedforward supplies most of the voltage needed to maintain the desired speed. The bang-bang output can then provide an extra burst of voltage whenever the mechanism falls below its target.

The combined output is

\\[
V = V_{FF} + V_{BB}
\\]

where \\(V_{FF}\\) is the predicted voltage from feedforward and \\(V_{BB}\\) is either an added voltage or zero.

For example:

```java
double bangBangVolts =
    controller.calculate(measuredVelocity, targetVelocity) * 12.0;

double feedforwardVolts =
    feedforward.calculate(targetVelocity);

flywheelMotor.setVoltage(
    bangBangVolts + 0.9 * feedforwardVolts
);
```

The feedforward estimate is sometimes reduced slightly so it does not maintain a speed above the setpoint. Since the bang-bang controller cannot actively slow the flywheel down, a feedforward value that is too large will cause overspeed that the controller cannot correct. The `0.9` in this example is only a starting point and should be tested on the real mechanism.

The final voltage should be limited to what the motor and robot can actually supply. In practice, `setVoltage()` and the motor controller will limit the command, but it is still useful to log the requested voltage so we know when the controller is asking for more than the available battery voltage.

## Tuning and Testing

There are no \\(k_P\\), \\(k_I\\), or \\(k_D\\) gains to tune, but the complete system still needs testing.

1. Verify the encoder direction and units.
2. Put the motors in coast mode.
3. Begin with a low, safe setpoint.
4. Graph the target velocity, measured velocity, controller output, and applied voltage.
5. Increase to the real operating range and watch for excessive overshoot or rapid output switching.
6. Test recovery by safely introducing the normal disturbance, such as shooting a game piece.
7. Set a realistic velocity tolerance for deciding when the mechanism is ready.

If the mechanism stays above its setpoint for too long, reduce the feedforward estimate or use PID control instead. If it repeatedly crosses the setpoint and switches rapidly, check coast mode, sensor noise, mechanism inertia, and whether bang-bang control is appropriate for the mechanism.

## Final Takeaway

Bang-bang control is not a less advanced PID. It is a specialized controller that trades smoothness and precision for extremely fast acceleration and recovery. In FRC, that makes it powerful for shooter flywheels and inappropriate for almost everything else. Use it only when the mechanism's inertia and natural slowdown make its one-direction correction safe.
