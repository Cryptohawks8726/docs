(Needs proofreading! Written by: Keshav)

# Tuning PID Controllers

## Why Tuning Matters

A PID controller is only as good as the constants used in the equation. While the PID algorithm itself remains the same for every system, the values of \(k_P\), \(k_I\), and \(k_D\) must be chosen specifically for the system being controlled.

Unfortunately, there is no universal set of PID gains that work for every application. A controller that performs perfectly on one system may be completely unstable on another. Factors such as inertia, friction, damping, motor power, sensor resolution, and external disturbances all influence how a system responds to a given set of gains.

Because of this, PID controllers must be **tuned**. Tuning is the process of adjusting the controller gains until the system behaves in the desired manner. While there are several mathematical methods for selecting PID gains, manual tuning remains one of the most common approaches because it is simple, intuitive, and works well for many systems.

This page focuses on manual tuning. Rather than attempting to find the mathematically optimal gains, the objective is to develop a stable controller that reaches its target quickly while minimizing overshoot and oscillation.

---

## Before You Begin

Before attempting to tune a PID controller, it is important to verify that the system itself is functioning correctly.

A poorly designed or damaged mechanical system cannot be fixed by changing controller gains. Excessive friction, loose components, sensor inaccuracies, and actuator limitations all affect the performance of a controller. Attempting to compensate for these problems by increasing the PID gains usually results in a controller that is difficult to tune and behaves unpredictably.

Likewise, only one gain should be adjusted at a time. Changing multiple gains simultaneously makes it nearly impossible to determine which adjustment produced the observed behavior.

A common starting point is

\[
k_P>0,\qquad
k_I=0,\qquad
k_D=0.
\]

Beginning with only the proportional term allows the effects of each gain to be observed independently.

---

# Tuning \\(k_P\\)

The proportional gain should almost always be tuned first because it provides the primary driving force of the controller.

Begin with a very small value of \(k_P\).

Command the system to move toward its desired position and observe its response.

If the system moves slowly or appears sluggish, increase the proportional gain.

As the proportional gain increases, the controller produces larger corrections for the same amount of error. This causes the system to respond more quickly and generally decreases the time required to reach the desired position.

Continue increasing the proportional gain until the system begins oscillating around the target.

At this point the controller has become too aggressive. Rather than smoothly approaching the desired position, it repeatedly overshoots and corrects itself.

Once oscillation begins, gradually decrease \(k_P\) until the oscillation disappears.

For many systems, this value provides a good starting point for the remaining tuning process.

---

## Symptoms of Incorrect \\(k_P\\)

If \(k_P\) is too small:

- The system responds slowly.
- The controller appears weak or sluggish.
- Large steady-state errors may remain.

If \(k_P\) is too large:

- The system overshoots the target.
- Oscillation becomes more likely.
- The controller may become unstable.

---

# Tuning \\(k_D\\)

Once a reasonable proportional gain has been established, the derivative gain can be introduced.

The purpose of the derivative term is not to make the system move faster.

Instead, it improves stability by reducing overshoot and damping oscillations.

Begin with

\[
k_D=0.
\]

Gradually increase the derivative gain while repeatedly commanding changes in the desired position.

As the derivative gain increases, the controller begins resisting rapid changes in the error.

The system should settle more smoothly and overshoot should decrease.

If the derivative gain becomes too large, however, the controller may become overly sensitive to measurement noise. The output may become erratic or appear to fluctuate rapidly even though the system itself is not changing significantly.

Increase the derivative gain only until the desired amount of damping has been achieved.

Additional derivative gain beyond this point usually provides little benefit.

---

## Symptoms of Incorrect \\(k_D\\)

If \(k_D\) is too small:

- Overshoot increases.
- Oscillations take longer to disappear.
- The system appears underdamped.

If \(k_D\) is too large:

- The controller becomes noisy.
- Small measurement errors produce large output changes.
- The system may appear hesitant or unresponsive.

---

# Tuning \\(k_I\\)

The integral gain should almost always be tuned last.

Unlike the proportional and derivative terms, the integral term primarily affects the long-term behavior of the controller rather than its initial response.

If the controller consistently settles slightly away from its desired position, a small amount of integral gain may eliminate the remaining error.

Begin with

\[
k_I=0.
\]

Increase the gain very gradually.

Observe whether the remaining steady-state error decreases.

If the controller begins oscillating slowly or becomes unstable after remaining near the target for an extended period of time, the integral gain is likely too large.

Since the integral term continuously accumulates error, excessively large values can produce integral windup and significantly increase overshoot.

For this reason, the integral gain is often much smaller than the proportional gain.

---

## Symptoms of Incorrect \\(k_I\\)

If \(k_I\) is too small:

- Steady-state error remains.
- Small persistent errors are never fully corrected.

If \(k_I\) is too large:

- The controller overshoots after long periods of error.
- Slow oscillations develop.
- Integral windup becomes more likely.

---

# Common Symptoms

Observing the behavior of the system often provides valuable clues about which gain should be adjusted.

| Behavior | Likely Cause |
|----------|--------------|
| Slow response | Increase \(k_P\) |
| Large overshoot | Increase \(k_D\) or reduce \(k_P\) |
| Continuous oscillation | Reduce \(k_P\) or increase \(k_D\) |
| Small steady-state error | Increase \(k_I\) slightly |
| Slow oscillation | Reduce \(k_I\) |
| Noisy output | Reduce \(k_D\) |

Remember that these are general guidelines rather than strict rules. Every system behaves differently, and successful tuning often requires careful observation and small incremental adjustments.

---

# Final Thoughts

PID tuning is both a science and an engineering skill. Although mathematical techniques exist for selecting controller gains, practical tuning often relies on observing how the system behaves and understanding why those behaviors occur.

As you gain experience, many tuning decisions become intuitive. Rather than viewing oscillation or overshoot as problems, experienced engineers recognize them as information about how the controller is interacting with the system.

A well-tuned PID controller should move the system quickly toward its desired state, minimize overshoot, eliminate steady-state error when necessary, and remain stable under changing conditions. Achieving this balance is the ultimate goal of PID tuning.
