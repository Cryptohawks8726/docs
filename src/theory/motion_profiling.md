(Needs proofreading! Written by: Keshav)

# Motion Profiling

## The Problem with Instant Setpoints

Suppose an elevator is currently at 0 meters and we suddenly give its PID controller a setpoint of 1 meter. To the controller, the entire 1-meter error exists immediately. A large error creates a large output, so the elevator may accelerate as hard as it can, approach the target too quickly, overshoot it, and then reverse direction to correct itself.

Increasing or decreasing the PID gains may change the behavior, but it does not fix the original problem: we asked the mechanism to move from rest at one position to rest at another position instantly. No real mechanism can do that.

A **motion profile** creates a sequence of reachable setpoints between the starting state and the final goal. Instead of immediately telling the controller "be at 1 meter," it tells the controller where the mechanism should be and how fast it should be moving every loop along the way.

This lets us control:

- Maximum velocity
- Maximum acceleration
- The position, velocity, and sometimes acceleration expected at each moment

Motion profiling produces smoother, safer, and more repeatable movement. It is commonly used for elevators, arms, turrets, and other position-controlled mechanisms in FRC.

## Goal, Setpoint, and Measurement

These three terms are easy to confuse:

- The **goal** is the final state we want the mechanism to reach.
- The **profiled setpoint** is the planned state for the current moment.
- The **measurement** is the state reported by the real mechanism's sensor.

For example, an elevator's goal may be 1 meter with a final velocity of 0 meters per second. Halfway through the motion, the profile may produce a setpoint of 0.45 meters moving at 0.8 meters per second. The encoder may report that the real elevator is at 0.43 meters.

The PID compares the measurement to the current profiled position, not directly to the final goal. This prevents the full distance to the goal from becoming an immediate error.

## Position, Velocity, and Acceleration

A motion profile describes more than position.

- **Position** tells us where the mechanism should be.
- **Velocity** tells us how quickly its position should be changing.
- **Acceleration** tells us how quickly its velocity should be changing.

The profile's position can be used as a PID setpoint. Its velocity and acceleration can be used by feedforward to predict how much voltage the planned motion will need.

This creates a complete controller:

\\[
V_{total} =
V_{feedback}(\text{position error})
+
V_{feedforward}(\text{velocity}, \text{acceleration})
\\]

The motion profile plans the movement, feedforward provides the expected effort, and feedback corrects the error between the plan and the real mechanism.

# Trapezoidal Motion Profiles

The most common motion profile in FRC is a **trapezoidal profile**. The name comes from the shape of its velocity-versus-time graph.

A normal trapezoidal move has three phases:

1. **Acceleration:** velocity increases at the maximum allowed acceleration.
2. **Cruise:** velocity remains at the maximum allowed velocity.
3. **Deceleration:** velocity decreases until the mechanism reaches the goal at its requested final velocity.

When graphed, these phases form a trapezoid.

For short moves, the mechanism may need to decelerate before it ever reaches the maximum velocity. The velocity graph then forms a triangle instead of a trapezoid. WPILib handles this automatically.

## Constraints

A trapezoidal profile needs two primary constraints:

- Maximum velocity
- Maximum acceleration

These are limits for the **planned setpoint**, not guarantees about the real mechanism. If the robot cannot physically produce enough voltage or torque, it will fall behind the profile even though the profile itself obeys the constraints.

Choose constraints based on:

- The mechanism's physical speed and acceleration limits
- Motor current and breaker limits
- The load the mechanism must carry
- The available travel before a hard stop
- How much tipping or robot movement is acceptable
- How quickly the mechanism actually needs to move during a match

The highest possible values are rarely the best values. A slightly slower profile that the mechanism follows accurately is usually more useful than an aggressive profile that causes voltage saturation and large tracking error.

## Trapezoidal Profiles Do Not Limit Jerk

Acceleration changes instantly at the boundaries between the acceleration, cruise, and deceleration phases. The rate of change of acceleration is called **jerk**, so a trapezoidal profile does not limit jerk.

For most FRC mechanisms, trapezoidal profiles are simple and smooth enough. More advanced S-curve profiles limit jerk as well, but they are usually only necessary when a mechanism is especially sensitive to sudden changes in acceleration.

# Using `TrapezoidProfile` in WPILib

WPILib provides the `TrapezoidProfile` class. A profile uses constraints and moves from a current state toward a goal state. Each state contains both position and velocity.

```java
private static final double LOOP_PERIOD = 0.02;

private final TrapezoidProfile.Constraints constraints =
    new TrapezoidProfile.Constraints(
        MAX_VELOCITY,
        MAX_ACCELERATION
    );

private final TrapezoidProfile profile =
    new TrapezoidProfile(constraints);

private TrapezoidProfile.State goal =
    new TrapezoidProfile.State(0.0, 0.0);

private TrapezoidProfile.State setpoint =
    new TrapezoidProfile.State(0.0, 0.0);
```

Every robot loop, calculate the next state:

```java
setpoint = profile.calculate(
    LOOP_PERIOD,
    setpoint,
    goal
);
```

With the normal 20-millisecond robot loop, `LOOP_PERIOD` is `0.02` seconds. If the control loop runs at a different period, use its real period.

The returned setpoint contains:

```java
setpoint.position
setpoint.velocity
```

The position can be sent to a PID controller, and the velocity can be sent to a feedforward controller.

```java
double feedbackVolts =
    pid.calculate(encoder.getPosition(), setpoint.position);

double feedforwardVolts =
    elevatorFeedforward.calculate(setpoint.velocity);

motor.setVoltage(feedbackVolts + feedforwardVolts);
```

If acceleration feedforward matters, calculate the desired acceleration from the change in profiled velocity:

```java
double acceleration =
    (setpoint.velocity - previousSetpoint.velocity) / LOOP_PERIOD;
```

Save the previous state before calculating the next one, and keep every unit consistent.

# Using `ProfiledPIDController`

WPILib also provides `ProfiledPIDController`, which combines a PID controller with an internally generated trapezoidal profile. This is usually the simplest choice when we want a roboRIO-based PID to follow a profiled position.

```java
private final ProfiledPIDController controller =
    new ProfiledPIDController(
        kP,
        kI,
        kD,
        new TrapezoidProfile.Constraints(
            MAX_VELOCITY,
            MAX_ACCELERATION
        )
    );
```

The user gives the controller a final goal. Internally, it creates the current profiled setpoint and calculates PID feedback using that setpoint.

```java
double feedbackVolts =
    controller.calculate(
        encoder.getPosition(),
        goalPosition
    );

motor.setVoltage(feedbackVolts);
```

The important difference from a normal `PIDController` is that `goalPosition` is the destination, not the PID setpoint used during that loop. The actual setpoint moves toward the goal while obeying the constraints.

The controller's current profiled state is available with `getSetpoint()`:

```java
TrapezoidProfile.State setpoint =
    controller.getSetpoint();
```

That state can be used for feedforward:

```java
double feedbackVolts =
    controller.calculate(
        encoder.getPosition(),
        goalPosition
    );

TrapezoidProfile.State setpoint =
    controller.getSetpoint();

double feedforwardVolts =
    elevatorFeedforward.calculate(setpoint.velocity);

motor.setVoltage(feedbackVolts + feedforwardVolts);
```

For an arm, also pass the appropriate profiled position to `ArmFeedforward` so it can calculate gravity compensation. For a drivetrain trajectory, use the drivetrain and trajectory-following tools designed for that purpose instead of independently profiling each wheel position.

## Resetting the Controller

A profiled controller must begin with a state close to the real mechanism. Otherwise, it may assume the mechanism starts at zero and generate a profile from the wrong location.

When enabling the subsystem or starting a new control command, reset it using the current measured state:

```java
controller.reset(
    encoder.getPosition(),
    encoder.getVelocity()
);
```

This is especially important after robot code restarts, the mechanism is moved by hand, or a command is interrupted.

## Continuous Input

Mechanisms such as turrets may wrap from one angle to another. For example, 359 degrees and 1 degree are only 2 degrees apart, not 358 degrees apart. A `ProfiledPIDController` can be configured for continuous input:

```java
controller.enableContinuousInput(-Math.PI, Math.PI);
```

Only enable continuous input when the mechanism can safely rotate through the wraparound point. A turret with wires that cannot rotate continuously still needs software limits and must not take a mathematically short path that damages its wiring.

# Choosing and Tuning Constraints

Start with conservative maximum velocity and acceleration values. Test the mechanism while graphing:

- Goal position
- Profiled position
- Measured position
- Profiled velocity
- Measured velocity
- Requested and applied voltage
- Position error

Increase maximum velocity if the mechanism follows the profile accurately and needs to finish the move sooner. Increase maximum acceleration if it reaches speed too slowly and the robot has enough available voltage, current, and mechanical stability.

Decrease the constraints when:

- The requested voltage stays near its limit.
- The measured state falls far behind the profile.
- The mechanism overshoots or oscillates even with reasonable controller gains.
- The robot tips, flexes, or experiences large current spikes.
- Game pieces move or fall out because the motion is too abrupt.

Tune feedforward before making the profile extremely aggressive. If \\(k_V\\), \\(k_A\\), or \\(k_G\\) is incorrect, the PID has to compensate for a bad model and may not follow the profile consistently.

# Common Mistakes

## Confusing the Goal with the Setpoint

The goal is the final destination. The setpoint is the planned state for the current loop. Log both so it is clear whether the profile itself or the physical mechanism is causing a problem.

## Incorrect Units

Position, velocity, acceleration, constraints, encoder conversions, and feedforward constants must all use compatible units. If the position is in rotations, maximum velocity should be in rotations per second and acceleration in rotations per second squared.

## Forgetting to Reset

Starting a profile from a stale or default state can make the first setpoint jump. Reset the controller to the measured position and velocity when beginning control.

## Impossible Constraints

A profile can mathematically request motion that the mechanism cannot physically follow. Check voltage saturation and measured tracking instead of assuming that obeying software constraints means the motion is achievable.

## Using a Profile as the Entire Controller

A motion profile only generates setpoints. It does not make the mechanism follow them. We still need feedback, feedforward, or a correctly configured onboard motor controller.

## Ignoring Interruptions

Commands can be canceled, disabled, or replaced. Decide whether a new profile should start from the previous planned state or the current measured state. For most mechanism commands, restarting from the measurement prevents a jump after an interruption.

# Motion Profiles vs Drivetrain Trajectories

A one-dimensional trapezoidal profile controls one position, such as an elevator height or arm angle. A drivetrain trajectory also plans the robot's position and orientation across the field and may contain curvature, chassis velocity, and wheel-speed information.

Both ideas create reachable intermediate states, but they are not interchangeable. Use WPILib trajectory tools or the team's autonomous path tools for field paths. Use `TrapezoidProfile` or `ProfiledPIDController` for individual mechanism motion.

# Final Takeaway

A motion profile turns an impossible instant position change into a planned movement with limited velocity and acceleration. The profile creates reachable setpoints, feedforward predicts the effort needed to follow them, and feedback corrects the remaining error. For FRC arms, elevators, turrets, and other position-controlled mechanisms, this combination usually produces movement that is faster, smoother, safer, and easier to tune.
