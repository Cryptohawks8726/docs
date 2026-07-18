(Needs proofreading! Written by: Keshav)

# Using Feedback and Feedforward Control Together

## The Main Idea

So far, we have treated feedback and feedforward as two separate ways to control a mechanism. In reality, they are usually most useful when we combine them.

For many position-controlled FRC mechanisms, our combined controller will use:

- \\(k_P\\) to correct the current position error
- \\(k_D\\) to damp the movement and reduce overshoot
- \\(k_G\\) to counteract gravity
- \\(k_S\\) to overcome static friction

The feedback terms react to the difference between where the mechanism is and where it should be. The feedforward terms account for known forces that we can predict before they create an error.

This gives us the general equation

\\[
V =
k_P e
+
k_D \frac{de}{dt}
+
k_G(\text{gravity model})
+
k_S\,sgn(\text{desired motion})
\\]

Not every mechanism uses these terms in exactly the same way. For an elevator, gravity is approximately constant. For an arm, gravity's effect changes with its angle. However, the main idea stays the same: **PD corrects error while feedforward handles the predictable physics of the mechanism.**

## Why Not Just Use PID?

A PID controller knows absolutely nothing about the mechanism it is controlling. It does not know that an elevator is heavy, that gravity pulls an arm downward, or that friction prevents a mechanism from beginning to move. It only knows the setpoint, measurement, and resulting error.

Imagine using only proportional control to hold an elevator at a height. At exactly zero error, the proportional output is also zero:

\\[
V_P = k_P(0) = 0
\\]

Zero volts cannot hold the elevator against gravity, so it begins to fall. Once it falls, an error appears and the proportional controller finally creates an upward voltage. The elevator may eventually settle, but it will settle slightly below its setpoint because it needs a permanent error to produce the voltage that holds it up.

This difference between the setpoint and the position where the mechanism settles is called **steady-state error**.

We could use \\(k_I\\) to accumulate that error over time. Eventually, the integral term would build enough output to counter gravity. That technically works, but we already know what is causing the error. Gravity is not a mysterious disturbance that only appears after several seconds. It is a predictable force that is always there.

Instead of waiting for integral to discover the needed voltage, we can supply it immediately with \\(k_G\\).

## Replacing the Job of Integral

The integral term is commonly used to remove steady-state error. It adds together error over time:

\\[
V_I = k_I \int e(t)\,dt
\\]

If a mechanism remains below its setpoint, the integral output continues increasing until the controller produces enough voltage to move it closer. The problem is that this correction takes time to build, and it does not know *why* the error exists.

This can cause:

- Slow correction of steady-state error
- Overshoot after the output has accumulated
- Integral windup when the mechanism cannot move
- Different behavior after the mechanism has been held away from its setpoint
- More difficult and less predictable tuning

Feedforward takes a different approach. We identify the predictable forces that would have caused steady-state error and calculate the voltage needed to counter them directly.

- \\(k_G\\) accounts for gravity.
- \\(k_S\\) accounts for the voltage needed to overcome static friction.

When those constants are accurate, the mechanism no longer needs a permanent position error to hold itself or begin moving. Because the main sources of steady-state error have already been handled, we can usually set

\\[
k_I = 0
\\]

and use a PD controller for feedback.

This does not mean feedforward and integral are mathematically identical. Integral can learn corrections for unknown constant disturbances, while feedforward only accounts for forces included in its model. The important FRC lesson is that we should model known forces first instead of using integral to hide them.

# What Each Term Does

## \\(k_P\\): Correcting Position Error

The proportional term looks at the current error:

\\[
V_P = k_P(\text{setpoint} - \text{measurement})
\\]

If the mechanism is far away from its setpoint, \\(k_P\\) produces a larger correction. As the mechanism approaches the setpoint, the correction becomes smaller.

In the combined controller, \\(k_P\\) does not need to provide all the voltage required to hold or move the mechanism. Feedforward handles the expected forces, while \\(k_P\\) corrects the remaining difference between the model and reality.

This allows us to use a less aggressive \\(k_P\\) than we might need with feedback alone. A smaller, reasonable proportional gain is usually smoother and less likely to cause oscillation.

## \\(k_D\\): Damping the Motion

The derivative term responds to how quickly the error is changing:

\\[
V_D = k_D \frac{de}{dt}
\\]

As the mechanism approaches the setpoint quickly, \\(k_D\\) opposes that rapid change and helps slow the mechanism down. This reduces overshoot and oscillation.

A useful way to think about \\(k_D\\) is as a shock absorber. \\(k_P\\) pulls the mechanism toward the setpoint, while \\(k_D\\) prevents it from bouncing around the setpoint.

Derivative control can be sensitive to noisy sensor data, so it should only be increased as much as necessary. If the output becomes noisy or rapidly changes even while the mechanism is nearly still, \\(k_D\\) may be too large or the measurement may need attention.

## \\(k_G\\): Accounting for Gravity

The gravity term provides the voltage required to counter the mechanism's weight.

For an elevator, gravity pulls in approximately the same direction and with the same force across its entire range. Its gravity voltage is therefore approximately constant:

\\[
V_G = k_G
\\]

For a rotating arm, the torque caused by gravity changes with the arm's angle. If zero radians is defined as parallel to the ground, the model is commonly:

\\[
V_G = k_G cos(\theta)
\\]

At the horizontal position, gravity creates its greatest torque. At the vertical position, the gravity torque is approximately zero. The arm feedforward changes its output to match this difference.

Without \\(k_G\\), the feedback controller needs some error to generate the voltage that fights gravity. With a correctly tuned \\(k_G\\), the mechanism can theoretically hold its position with zero feedback error, and PD only needs to correct imperfections in the model.

> [!IMPORTANT]
> The arm angle given to the gravity calculation must use the same zero position and units expected by the model. An incorrect encoder offset can make a correct \\(k_G\\) behave incorrectly.

## \\(k_S\\): Accounting for Static Friction

Static friction prevents a stationary mechanism from beginning to move until enough voltage is applied. A small PD output may not overcome this friction, causing the mechanism to remain slightly away from its setpoint.

The \\(k_S\\) term adds the approximate voltage needed to overcome that friction:

\\[
V_S = k_S\,sgn(\text{desired motion})
\\]

The sign matters because friction must be overcome in the direction we want to move. If the mechanism should move forward, we add \\(k_S\\). If it should move backward, we subtract \\(k_S\\).

For a motion-profiled mechanism, the sign normally comes from the desired velocity. When holding a stationary position, we usually should not blindly apply \\(k_S\\) based on tiny, noisy position errors. Doing that can make the output rapidly switch directions around the setpoint. The exact behavior near zero velocity should be tested, and a small deadband may be useful.

\\(k_S\\) helps the mechanism begin moving when commanded, while \\(k_G\\) helps it resist gravity. Together, they remove two common reasons a PD-controlled mechanism would otherwise stop with a small steady-state error.

# The Complete Controller

For a stationary elevator position, the combined controller may be simplified to:

\\[
V =
k_P e
+
k_D \frac{de}{dt}
+
k_G
+
k_S\,sgn(v_{desired})
\\]

For an arm, it may look like:

\\[
V =
k_P e
+
k_D \frac{de}{dt}
+
k_G cos(\theta)
+
k_S\,sgn(v_{desired})
\\]

If we use a motion profile, we may also include \\(k_V\\) and \\(k_A\\) to account for the desired velocity and acceleration:

\\[
V_{total} = V_{PD} + V_{FF}
\\]

\\[
V_{PD} =
k_P e
+
k_D \frac{de}{dt}
\\]

\\[
V_{FF} =
k_S\,sgn(v)
+
k_G(\text{gravity model})
+
k_Vv
+
k_Aa
\\]

The feedforward is our best prediction of the voltage the motion should require. The PD output is the correction for the difference between that prediction and what the real robot actually does.

# A WPILib Example

WPILib keeps the feedback and feedforward calculations separate. We calculate both in volts, add them together, and send the result to the motor.

An elevator example could look like this:

```java
private final PIDController feedback =
    new PIDController(kP, 0.0, kD);

private final ElevatorFeedforward feedforward =
    new ElevatorFeedforward(kS, kG, kV, kA);

public void setPosition(
    double desiredPosition,
    double desiredVelocity
) {
    double feedbackVolts = feedback.calculate(
        elevatorEncoder.getPosition(),
        desiredPosition
    );

    double feedforwardVolts = feedforward.calculate(
        desiredVelocity
    );

    elevatorMotor.setVoltage(
        feedbackVolts + feedforwardVolts
    );
}
```

Notice that \\(k_I\\) is set to zero. Gravity and static friction are handled by `ElevatorFeedforward`, so feedback only uses the P and D terms.

An arm uses the same structure, but its feedforward also needs the arm angle:

```java
private final PIDController feedback =
    new PIDController(kP, 0.0, kD);

private final ArmFeedforward feedforward =
    new ArmFeedforward(kS, kG, kV, kA);

double feedbackVolts = feedback.calculate(
    armEncoder.getPosition(),
    desiredPosition
);

double feedforwardVolts = feedforward.calculate(
    desiredPosition,
    desiredVelocity
);

armMotor.setVoltage(
    feedbackVolts + feedforwardVolts
);
```

The exact method arguments depend on the WPILib version and whether acceleration is included, but the structure remains the same: calculate PD, calculate the matching mechanism feedforward, and add their voltage outputs.

Use `setVoltage()` instead of percentage output. Feedforward constants describe real volts, and voltage control compensates for the battery voltage changing throughout a match.

> [!IMPORTANT]
> Every value must use consistent units. If the feedforward constants were found using radians and seconds, the angle and velocity passed into the controller must also use radians and seconds.

# How to Tune the Combined Controller

Feedforward should be tuned before feedback because we want the model to handle the predictable physics.

## 1. Verify the Mechanism

Before tuning anything, check:

- Motor and encoder directions agree.
- Position and velocity conversion factors are correct.
- The arm's encoder zero matches the gravity model.
- The mechanism moves freely without unexpected binding.
- Current limits and software limits are configured safely.

No controller can fix incorrect units, a reversed sensor, or a broken mechanism.

## 2. Tune \\(k_S\\)

Find the smallest voltage that causes the mechanism to begin moving. Test both directions because friction may not be perfectly symmetric.

The goal is not to make \\(k_S\\) do all the moving. It should only account for the voltage lost to static friction.

## 3. Tune \\(k_G\\)

Tune the voltage required to hold the mechanism against gravity.

For an elevator, test whether it drifts upward or downward while holding. For an arm, begin at the horizontal position where gravity has its greatest effect, then verify the result at several other angles.

Be careful not to accidentally include the effect of \\(k_S\\) twice while manually finding \\(k_G\\). The constants page explains this process in more detail.

## 4. Tune \\(k_P\\)

With feedforward active and \\(k_I = k_D = 0\\), slowly increase \\(k_P\\).

- If the mechanism is weak at correcting position error, increase \\(k_P\\).
- If it overshoots or oscillates, reduce \\(k_P\\).

Because \\(k_G\\) and \\(k_S\\) already handle predictable resistance, \\(k_P\\) should only need to correct the remaining error.

## 5. Tune \\(k_D\\)

Increase \\(k_D\\) gradually until the mechanism approaches the target with acceptable overshoot and settles smoothly.

- If it bounces around the target, it may need more damping.
- If the output becomes noisy or the mechanism feels hesitant, \\(k_D\\) may be too large.

## 6. Test the Entire Range

Test more than one position and one direction. An arm should be tested above and below horizontal. An elevator should be tested while moving both upward and downward. Also test with the real game piece or load when possible.

Graph:

- Goal and setpoint
- Measured position
- Measured and desired velocity
- Feedforward voltage
- Feedback voltage
- Total requested voltage

If the feedback output constantly supplies a large voltage just to hold still, \\(k_G\\), \\(k_S\\), the encoder offset, or the model probably needs more work.

# When Would We Still Use \\(k_I\\)?

For most FRC position mechanisms, a good feedforward model plus PD control is enough. However, \\(k_I\\) is not forbidden.

A small integral term may still be useful when the mechanism has a persistent disturbance that the feedforward model cannot predict, such as a changing load or a consistent model error. Before adding it, make sure the problem is not actually caused by:

- An incorrect \\(k_G\\) or \\(k_S\\)
- Incorrect units or encoder offsets
- Mechanical binding
- Voltage saturation
- A load that should be included in the model

If integral is used, limit the range over which it accumulates and watch for windup. We should not reach for \\(k_I\\) first when a known physical force can be described with feedforward.

# Common Problems

## The Mechanism Holds Below Its Setpoint

Check \\(k_G\\) first. If feedback must maintain a positive output just to hold the mechanism still, the gravity feedforward is probably too small.

## The Mechanism Holds Above Its Setpoint

The gravity feedforward may be too large, or the sign of the gravity term may be incorrect.

## The Mechanism Will Not Begin a Small Movement

Check \\(k_S\\), friction, and the requested direction. The static-friction term may be too small or may have the wrong sign.

## The Mechanism Jitters Near the Setpoint

The \\(k_S\\) direction may be switching because of sensor noise or tiny errors. Add appropriate tolerance logic, inspect the measurement, and make sure \\(k_D\\) is not amplifying noise.

## The Mechanism Overshoots

Reduce \\(k_P\\), increase \\(k_D\\) carefully, or use a motion profile with more conservative acceleration and velocity constraints.

## The Controller Works at One Arm Angle but Not Another

Check the encoder zero and the angle used in the cosine gravity model. A constant gravity voltage is not accurate across the full range of a rotating arm.

# Final Takeaway

The P and D terms correct the difference between the planned state and the real mechanism. The \\(k_G\\) and \\(k_S\\) terms account for gravity and static friction before those forces create a permanent error.

By giving the known physics to feedforward, we no longer need integral to slowly discover the voltage required to hold or begin moving the mechanism. The result is usually a more accurate, responsive, and predictable FRC controller:

\\[
\boxed{\text{PD correction} + \text{feedforward model}}
\\]

Tune the model first, use P to correct position, use D to add damping, and only add I when a real unmodeled steady-state error remains.
