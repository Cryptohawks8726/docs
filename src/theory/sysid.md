(Needs proofreading! Written by: Keshav)

# System Identification

## What is System Identification?

In the previous page, we discussed how constants such as \\(k_S\\), \\(k_V\\), \\(k_A\\), and \\(k_G\\) can be manually tuned. Manual tuning works well for many mechanisms, but it can become difficult when we want an accurate value for multiple constants at the same time. This is especially true for \\(k_A\\), since it is difficult to measure acceleration without also seeing the effects of friction and velocity.

**System Identification**, usually shortened to **SysId**, is the process of collecting data from a real system and using that data to create a mathematical model of it. Instead of guessing constants, testing them, and repeatedly adjusting them, we apply known voltages to the mechanism and record how it moves. We can then use the relationship between the voltage we applied and the motion we measured to estimate the constants in our feedforward equation.

For a simple rotating mechanism, SysId is usually trying to fit the data to the equation

\\[
V = k_S \times sgn(v) + k_V \times v + k_A \times a
\\]

where \\(V\\) is the voltage applied to the motors, \\(v\\) is the measured velocity, and \\(a\\) is the measured acceleration.

For mechanisms affected by gravity, the model can also contain a \\(k_G\\) term. For example, an elevator uses a constant \\(k_G\\), while an arm uses a \\(k_G cos(\theta)\\) term because the effect of gravity changes with the arm's angle.

SysId does not invent a new control algorithm. It finds the constants that describe an existing physical mechanism. Those constants can then be placed into a feedforward controller so we can calculate how much voltage the mechanism should need at a given velocity and acceleration.

## Why use SysId in FRC?

FRC mechanisms are not perfect theoretical systems. Two robots built from the exact same CAD can still behave differently because of friction, manufacturing tolerances, chain tension, wheel wear, battery voltage, mechanism weight, and many other small differences. A mathematical model calculated only from motor specifications will not perfectly account for all of these effects.

SysId uses the actual assembled mechanism, so the resulting model includes much more of its real behavior.

SysId is especially useful when:

- We need accurate feedforward constants.
- \\(k_A\\) is important and is difficult to tune manually.
- We are characterizing a drivetrain for trajectory following.
- We want a reliable starting point for feedback gains.
- We want to compare the behavior of a mechanism before and after a mechanical change.

However, SysId is not magic. It cannot fix a loose chain, a slipping wheel, an incorrectly configured encoder, or a mechanism that binds during part of its movement. It will only create a model from the data it receives. Bad data will produce a bad model.

## Characterization vs Tuning

The words **characterization** and **tuning** are sometimes used as if they mean the same thing, but there is an important difference.

Characterization is the process of determining how the physical mechanism behaves. SysId characterizes the mechanism by finding feedforward constants such as \\(k_S\\), \\(k_V\\), and \\(k_A\\).

Tuning is the process of adjusting a controller until the complete system behaves how we want it to behave. The feedback gains suggested by SysId are useful starting points, but they may still need to be manually tuned on the robot.

In other words, SysId can give us a good model, but we still have to verify that the finished controller works.

# How SysId Collects Data

To identify a system, we need to give it known inputs and measure its outputs. In our case, the input is voltage and the outputs are position and velocity from an encoder. Acceleration can then be estimated from the change in velocity over time.

WPILib's standard SysId routine performs two types of tests in both directions. This gives us four tests in total:

1. Quasistatic forward
2. Quasistatic reverse
3. Dynamic forward
4. Dynamic reverse

Testing in both directions matters because friction and mechanical behavior may not be identical in both directions. It also gives the analysis tool more data to work with.

## Quasistatic Tests

During a quasistatic test, the applied voltage slowly increases from zero. The mechanism should smoothly speed up as the voltage rises.

The word "quasistatic" basically means "almost static." Since the voltage increases slowly, the mechanism's acceleration is relatively small. This makes it easier for SysId to determine how much voltage is being used to overcome static friction and maintain velocity.

The quasistatic tests provide most of the useful information for calculating \\(k_S\\) and \\(k_V\\).

## Dynamic Tests

During a dynamic test, a constant step voltage is applied to the mechanism immediately. This causes the mechanism to accelerate much more strongly than it does during the quasistatic test.

Because the acceleration is large, the dynamic tests give SysId the information it needs to estimate \\(k_A\\). A mechanism's dynamic response also helps the tool determine whether its calculated model accurately predicts the real motion.

> [!WARNING]
> A dynamic test can move a mechanism very quickly. The person running the test must be ready to stop it before the mechanism reaches a hard stop, collides with something, or leaves the available testing area.

# WPILib's SysId Tools

WPILib provides two related pieces that are used together:

- The `SysIdRoutine` class runs the tests in robot code and records the data.
- The SysId desktop application loads the recorded data, analyzes it, and calculates the model constants.

The SysId application is included with the WPILib installation. It can be opened from the WPILib Tools folder or through the **WPILib: Start Tool** command in VS Code.

## Creating a `SysIdRoutine`

The current WPILib workflow runs SysId through our normal robot project. This is useful because we can use the subsystem code, motor configuration, limits, and sensors that we have already tested.

A `SysIdRoutine` has two main parts:

- A `Config`, which contains settings such as the quasistatic voltage ramp rate, dynamic step voltage, timeout, and optional logging callback.
- A `Mechanism`, which tells SysId how to apply voltage and how to log the mechanism's position, velocity, and applied voltage.

A simplified Java example looks like this:

```java
private final SysIdRoutine sysIdRoutine =
    new SysIdRoutine(
        new SysIdRoutine.Config(),
        new SysIdRoutine.Mechanism(
            this::setVoltage,
            this::logMotors,
            this
        )
    );
```

The exact implementation of `setVoltage` and `logMotors` depends on the motors and encoders used by the subsystem. The drive callback must apply the requested **voltage**, not a percentage output. The log callback must record the actual applied voltage, position, and velocity in consistent units.

The routine then creates commands for each test:

```java
public Command sysIdQuasistatic(SysIdRoutine.Direction direction) {
    return sysIdRoutine.quasistatic(direction);
}

public Command sysIdDynamic(SysIdRoutine.Direction direction) {
    return sysIdRoutine.dynamic(direction);
}
```

These commands can be placed in an autonomous sequence or bound to controller buttons. Binding each command to a button that must be held is usually safer because releasing the button can immediately stop the test.

> [!NOTE]
> WPILib provides SysId example projects in VS Code. Open the Command Palette, select **WPILib: Create a new project**, and look through the example projects for `SysIdRoutine` if you need a complete implementation.

## Choosing Test Settings

The default `SysIdRoutine.Config` uses a quasistatic ramp rate of 1 volt per second, a dynamic step voltage of 7 volts, and a 10 second timeout. These defaults may not be safe or practical for every mechanism.

For example, an arm with a small range of motion may reach its hard stop long before ten seconds. A drivetrain may run out of space before completing a test. The ramp rate, step voltage, and timeout should be selected based on the mechanism and the available testing area.

Lowering the test voltage or stopping a test early is much better than damaging the robot. We only need enough clean data for the tool to identify the system.

# Preparing the Robot

The quality of a SysId result mostly depends on the quality of the test data. Before running anything:

- Make sure the mechanism moves freely and does not bind.
- Check that chains, belts, gears, wheels, and fasteners are secure.
- Confirm that the encoder reports the correct direction.
- Confirm that the motor voltage and encoder velocity have matching signs.
- Use meaningful and consistent position and velocity units.
- Make sure all motors that should follow one another are configured correctly.
- Disable code that would fight the SysId voltage command.
- Set safe soft limits when possible.
- Clear enough space for the complete test.
- Keep people away from the moving mechanism.

Units are extremely important. If velocity is logged in motor rotations per minute but the analysis is configured for mechanism radians per second, the calculated constants will be wrong. Gear ratios must either be included when converting the encoder measurements or entered as a scaling factor during analysis.

It is also helpful to begin with a charged battery. A large voltage drop or inconsistent battery condition can make the collected data less reliable.

# Running the Tests

Deploy the robot code and run each of the four tests. The tests can technically be performed in any order, but it is usually convenient to perform a forward test followed by its reverse test so the mechanism returns closer to where it started.

Hold each test only while the mechanism can move safely. Watch the mechanism itself instead of staring only at the Driver Station. If anything sounds wrong, moves in an unexpected direction, or approaches a limit, stop immediately.

WPILib records the routine state and mechanism measurements in a WPILog file on the roboRIO. After all four tests are complete, use the WPILib DataLogTool to download the log.

Only run one identification routine in a log file. If multiple unrelated routines are recorded into the same log, the SysId application may not be able to correctly analyze it. Download the log and power-cycle the roboRIO before characterizing another mechanism.

> [!WARNING]
> A drivetrain must be characterized while driving on the floor. Running it on blocks removes the normal interaction between the wheels and the ground, so the collected model will not represent the drivetrain's real behavior.

# Analyzing the Data

Open the SysId application and load the WPILog in the **Log Loader** pane. Select the logged test state and match the mechanism's position, velocity, and voltage signals to the corresponding fields. Check the units and scaling before loading the data into the analysis.

The tool will then display diagnostic graphs and calculate the feedforward constants.

## Check the Graphs Before Using the Results

Do not immediately copy the constants just because the application produced numbers. First, verify that the model actually fits the data.

A good quasistatic velocity graph should be close to a straight ramp. A good dynamic velocity graph should rapidly increase and then begin approaching a steady speed. The simulated response should also follow the measured response reasonably closely.

Common signs of bad data include:

- Large spikes or jumps in velocity
- Flat sections while voltage is increasing
- Position and velocity moving in the wrong direction
- Forward and reverse tests behaving completely differently
- The simulated response being far away from the measured response
- Data recorded after the mechanism hit a hard stop

These problems can be caused by encoder noise, incorrect units, an incorrect gear ratio, voltage and velocity signs that do not agree, wheel slip, mechanical binding, or unsuitable test settings.

The application provides measurements describing how closely the model fits the data. These are useful, but the graphs and the actual behavior of the robot should always be considered as well. A number cannot tell us that a chain was slipping during the test unless we look at the data and inspect the mechanism.

## Feedforward Results

For a simple mechanism, the analysis produces values for:

- \\(k_S\\), in volts
- \\(k_V\\), in volts per unit of velocity
- \\(k_A\\), in volts per unit of acceleration

Depending on the selected mechanism type, it may also calculate \\(k_G\\).

The units of \\(k_V\\) and \\(k_A\\) depend on the units used during analysis. If the mechanism was analyzed using radians and seconds, those constants must be used with radians per second and radians per second squared. We cannot calculate gains using one set of units and then give the feedforward controller values in another.

These gains can be placed into the matching WPILib feedforward class, such as `SimpleMotorFeedforward`, `ElevatorFeedforward`, or `ArmFeedforward`.

## Feedback Results

SysId can also suggest feedback gains based on the identified model and the selected controller settings. These values are best treated as educated starting points rather than perfect final gains.

The correct settings depend on where the feedback loop runs. A controller running on the roboRIO normally updates every 20 milliseconds, while a loop running directly on a smart motor controller may update at a different rate and may use different units. Selecting the wrong controller type or conversion settings can produce gains that do not behave as expected.

After applying the suggested gains, test the mechanism carefully and continue tuning if needed.

# After Characterization

System identification describes the mechanism as it existed during the test. If the mechanism changes significantly, it should be characterized again.

Changes that may require another SysId test include:

- Changing the gear ratio
- Changing the number or type of motors
- Adding a large amount of mass
- Changing wheels or wheel diameter
- Increasing friction or tension
- Moving an encoder to a different shaft

Even without a major modification, the final controller must still be tested at several setpoints and under realistic loads. A flywheel should be tested across the range of speeds it will use. A drivetrain should be tested while accelerating, turning, and following trajectories. An arm or elevator should be tested throughout its usable range of motion.

SysId gives us a data-based model of the robot. Feedforward uses that model to predict the voltage we should need, while feedback corrects the remaining error when the real robot does not perfectly match the model. Using both together is often the most accurate and reliable way to control an FRC mechanism.

## Official Resources

- [WPILib System Identification documentation](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/system-identification/index.html)
- [Creating a SysId routine](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/system-identification/creating-routine.html)
- [Running a SysId routine](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/system-identification/running-routine.html)
- [Loading and analyzing SysId data](https://docs.wpilib.org/en/stable/docs/software/advanced-controls/system-identification/loading-data.html)
