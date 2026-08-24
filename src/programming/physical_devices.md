(Needs proofreading! Written by: Keshav)

# Interfacing with Physical Devices

Robot code is only useful when it can read what is happening on the robot and make physical hardware do something. Motors, encoders, gyroscopes, switches, pneumatics, LEDs, cameras, and power-distribution devices all need a Java object that allows our code to communicate with the real device.

This page explains the patterns shared by almost every physical device in FRC. The pages on [REV motors](rev_motors.md), [CTRE motors](ctre_motors.md), [sensors](sensors.md), and [cameras](cameras.md) explain their specific APIs in more detail.

# The Complete Path from Code to Hardware

When we write something like

```java
motor.setVoltage(6.0);
```

Java is not directly changing the voltage on a wire. Several layers are involved:

1. Our subsystem calls a method on a Java object.
2. WPILib or a vendor library turns that call into data understood by the device.
3. The roboRIO or SystemCore sends that data through CAN.
4. The physical controller receives the data and changes an output.
5. Sensors send measurements back through the same interface.
6. Our code reads those measurements during a later robot loop.

The Java object is our software representation of the physical device. Constructing it does not construct new hardware, obviously. It tells the library what device exists and how to reach it.

Because every layer must agree, a problem that looks like a code problem may actually be:

- An incorrect CAN ID or port number
- A missing vendor library
- An unplugged or incorrectly wired device
- A device with old firmware
- A sensor reporting different units than expected
- Two devices configured with the CAN ID
- A configuration that never reached the device

Understanding the entire path makes these problems much easier to diagnose.

# WPILib and Vendor Libraries

## What WPILib Provides

WPILib contains Java classes for hardware connected directly to standard robot-controller ports and for several common FRC devices. Some examples include:

- `PWMMotorController` implementations for PWM motor controllers
- `Math`
- `Units`
- and many many more

These classes are included in a normal WPILib Java project. Their packages usually begin with:

```java
edu.wpi.first.wpilibj
```

Command-based classes use:

```java
edu.wpi.first.wpilibj2.command
```

Math and controller classes generally use:

```java
edu.wpi.first.math
```

Imports matter because two libraries may have classes with similar names. Let VS Code autocomplete the class and inspect the import it adds instead of guessing.

## Why Vendor Libraries Exist

WPILib cannot contain the complete API for every piece of hardware made by every FRC vendor. Manufacturers publish their own Java libraries for devices that have features beyond the standard WPILib interfaces.

Our most common examples are:

- **REVLib** for SPARK motor controllers and other REV devices
- **Phoenix 6** for CTRE Talon FX, CANcoder, Pigeon, and other CTRE devices
- **YAGSL** for swerve control
- **PathPlanner** and **Choreo** for autos

A vendor library contains:

- Java classes used by our code
- Code for communicating with the device
- Configuration objects and status/error types
- Native libraries needed on the robot
- Sometimes simulation support

The hardware may be wired and visible on the CAN bus while our code still cannot use it until the correct vendor dependency is installed.

## Installing a Vendor Dependency

Vendor dependencies are managed **per project**. Installing REVLib in one robot project does not automatically add it to another project.

In current WPILib VS Code:

1. Click the WPILib icon in the Activity Bar to open the Dependency Manager.
2. Find the required library.
3. Select **Install**.
4. Allow the project to rebuild.

Libraries can also be installed from a vendor JSON URL or from an offline vendor installation. The dependency description is copied into the project's `vendordeps/` folder. That JSON file tells Gradle which Java, native, and simulation libraries the project needs.

Commit files in `vendordeps/` to Git. Everyone building the project and the robot deployment must use compatible dependency versions.

> [!IMPORTANT]
> Do not copy a random vendor JSON from a different season without checking compatibility. WPILib and vendor libraries release year-specific versions, and an old library may not work with the current robot project or firmware.

## Updating Vendor Libraries

Updating a library changes code used by the entire robot, so treat it like a real code change:

1. Read the vendor's release notes and migration guide.
2. Update through the WPILib Dependency Manager.
3. Build the project.
4. Fix changed or removed APIs.
5. Verify device firmware compatibility.
6. Test in simulation when supported.
7. Test every affected mechanism on the real robot.

Avoid unnecessary dependency updates immediately before an event. Once a robot is working reliably, known versions are usually more valuable than a new feature we do not need.

## Finding API Documentation

There are three particularly useful sources when working with a device:

- The vendor's programming guide explains intended workflows and important warnings.
- Java API documentation, or **Javadocs**, lists classes, constructors, and methods.
- Example projects show how the parts fit together in real robot code.

Use **WPILib: Open API Documentation** from the Command Palette for WPILib Javadocs. Vendor documentation is linked from the [REV](https://docs.revrobotics.com/) and [CTRE Phoenix](https://v6.docs.ctr-electronics.com/) sites.

Autocomplete shows what methods exist, but it does not replace reading the documentation. A method accepting a `double` does not tell us whether that number represents rotations, radians, meters, percent output, volts, or something else.

# Types of Physical Devices

## Actuators

An **actuator** changes the physical world.

Common FRC actuators include:

- Motors
- Pneumatics
- Servos
- Relays and switched power channels
- LEDs

Actuators receive outputs from robot code. Many smart actuators also return measurements, faults, temperature, current, or configuration information.

## Sensors

A **sensor** measures the robot or its environment.

Common examples include:

- Relative and absolute encoders
- Gyroscopes
- Limit switches and beam-break sensors
- Cameras and vision coprocessors

Sensors may be connected directly to the robot controller or built into another device. A SPARK or Talon FX, for example, can both control a motor and report encoder measurements.

## Infrastructure and Diagnostic Devices

Power distribution and pneumatic controllers are not mechanism actuators in the same way a motor is, but their data is extremely valuable:

- Battery voltage
- Per-channel current
- Total current
- Temperature

Reading these values can explain brownouts, stalled motors, disconnected mechanisms, and other problems that position alone cannot reveal.

# How Devices Connect

| Interface | Common devices | How devices are identified | Important characteristics |
| --- | --- | --- | --- |
| CAN | Smart motor controllers, CANcoders, Pigeons, PDH, Pneumatic Hub | CAN ID and sometimes CAN bus name | Two-way communication, configuration, telemetry, and faults |
| USB | Cameras, serial sensors, and coprocessors | USB device or serial port | General-purpose connection; enumeration can matter |
| Ethernet | Cameras, coprocessors, radio, robot controller | IP address, hostname, or network service | High-bandwidth network communication |

The electrical connection determines which Java class and constructor we use. A limit switch connected to DIO uses a different API from a CAN-based sensor even if both ultimately tell us whether something has reached a limit.

## CAN IDs

Every device on the same CAN bus must have an appropriate, unique CAN ID. The ID is stored on the device itself and is usually changed using vendor software such as REV Hardware Client or Phoenix Tuner.

The ID in code must match the device:

```java
private static final int LEFT_MOTOR_ID = 1;
```

```java
private final JohnMotor leftMotor =
    new JohnMotor(LEFT_MOTOR_ID);
```

Some APIs also accept a CAN bus name. Devices on different physical CAN buses can reuse an ID, but the code must identify the correct bus.

Do not choose CAN IDs in several different files without a plan. Store hardware mappings in one clear location or use consistently named subsystem constants so duplicate IDs are easy to find.

# Creating Hardware Objects

## Construct Devices Once

Hardware objects should normally be fields owned by the subsystem that physically contains them:

```java
public class IntakeSubsystem extends StatefulSubsystem {
    private final SomeMotorController intakeMotor =
        new SomeMotorController(5);
}
```

`SomeMotorController` is a placeholder used to demonstrate the shared structure; it is not a real WPILib class. The REV and CTRE pages replace it with the correct vendor type.

Do not construct a new device every robot loop:

```java
// Do not do this.
public void periodic() {
    DigitalInput beamBreak = new DigitalInput(0);
}
```

Creating the object repeatedly may attempt to allocate the same hardware resource more than once, reset state, waste time, or produce errors. Construct once, configure once, and reuse the object.

## One Owner per Device

Each physical device should have one clear software owner. Usually this is its subsystem.

If several unrelated classes create objects for the same CAN ID or channel, they can send conflicting commands or configurations. Other code should call meaningful subsystem methods instead of directly reaching into its motor and sensor objects.

Good:

```java
shooterSubsystem.runFlywheel(targetVelocity);
```

Avoid:

```java
shooterSubsystem.flywheelMotor.set(0.8);
```

The first version lets the subsystem enforce limits, use closed-loop control, log data, and change hardware later without rewriting every command.

# Configuring Devices

Smart devices retain or accept settings that change how they behave. Common configuration includes:

- Device inversion
- Neutral or idle mode
- Current limits
- Open-loop and closed-loop control constants
- Encoder conversion factors
- Feedback sensor selection

## Configuration is Part of the Program

Do not rely on a device happening to contain the correct settings from a previous robot or a vendor tool. Robot code should apply every setting required for safe, predictable operation.

This makes behavior repeatable when:

- A motor controller is replaced
- Firmware is updated
- A device is factory reset
- Code is deployed to a practice robot
- Someone changes a setting while debugging

The class and method names above are conceptual. Use the actual REV or CTRE API described in their dedicated pages.

# Reading Inputs

Input methods normally return the most recently received or measured value:

```java
boolean blocked = !beamBreak.get();
double position = encoder.getPosition();
double velocity = encoder.getVelocity();
```

## Raw Values vs Useful Values

A sensor may return:

- Counts
- Motor rotations
- Duty cycle from `0.0` to `1.0`
- Volts
- Degrees
- Radians
- Rotations per minute
- Rotations per second

Robot logic usually needs mechanism units such as meters, radians, meters per second, or degrees. Convert values at the device or subsystem boundary:

```java
public double getArmAngleRadians() {
    return motorRotations / GEAR_RATIO * 2.0 * Math.PI;
}
```

Do not make every command repeat the conversion. One conversion prevents different parts of the robot from disagreeing.

## Position, Velocity, and Absolute Position

These values are related but not interchangeable:

- **Position** measures accumulated movement from a zero point.
- **Velocity** measures how quickly position changes.
- **Absolute position** identifies a physical orientation within one rotation or range.

A relative encoder usually loses its reference when power is removed. An absolute encoder can restore that reference, but its magnet offset, discontinuity point, and direction still need configuration.

The [sensors page](sensors.md) covers calibration, filtering, and sensor types in detail.

## Cached and Timestamped Signals

CAN devices usually send status values periodically. Calling a getter may return the latest cached signal rather than forcing a new CAN transaction.

This means:

- Two values may have slightly different timestamps.
- A signal configured at a slow update rate should not be treated as fresh every loop.
- Unnecessary high-frequency status signals consume CAN bandwidth.
- Important fast control signals need appropriate update rates.

Use vendor mechanisms for refreshing, grouping, or timestamping signals when synchronized measurements matter. Do not maximize every update frequency by default.

# Writing Outputs

## Voltage Output

Voltage output requests a physical voltage:

```java
motor.setVoltage(6.0);
```

Voltage control is preferred for feedforward and many closed-loop controllers because the controller calculations have physical meaning. Don't use motor.set(percent) and just use this. 

## Position and Velocity Requests

Smart motor controllers can run control loops directly on the device. Instead of repeatedly sending motor voltage, robot code sends a position, velocity, torque-current, or motion-profile request.

Benefits can include:

- Faster control-loop update rates
- Less CAN traffic
- Vendor-provided motion profiling and compensation
- Control continuing between roboRIO loop updates

The request must specify:

- The correct control mode
- A setpoint in expected units
- The correct feedback sensor
- Tuned gains
- Any feedforward or gravity compensation
- Output and safety limits

REV and CTRE expose these features differently, so their dedicated pages cover the actual Java APIs.

## Stopping a Device

Common motor methods include:

```java
motor.setVoltage(0);
motor.stopMotor();
```

These are not always identical so check the class documentation to see what it specifically does for the specific device.

## Inversion

Motor inversion changes which physical direction is considered positive:

```java
motor.setInverted(true);
```

Sensor direction and motor direction must agree for closed-loop control. If positive voltage makes position decrease while the controller expects it to increase, feedback can run away at full output instead of correcting the error.

Do not fix every sign problem by adding random negative signs. Define a coordinate convention, then configure the motor and sensor to match it.

# Physical Devices in Command-Based Code

WPILib **commands** are not commands sent directly across CAN. A command is a robot action that tells one or more subsystems what to do over time.

The normal ownership chain is:

```text
Trigger or autonomous routine
        ↓
Command
        ↓
Subsystem method
        ↓
WPILib or vendor device object
        ↓
Physical hardware
```

## Subsystems Own Hardware

A subsystem should:

- Construct and configure its devices
- Convert raw measurements into meaningful units
- Expose mechanism-level operations
- Enforce safety limits
- Log important state
- Provide a safe stop behavior

```java
public class IntakeSubsystem extends SubsystemBase {
    private final DigitalInput beamBreak =
        new DigitalInput(0);

    private final SomeMotorController motor =
        new SomeMotorController(5);

    public void intake() {
        motor.setVoltage(5.0);
    }

    public boolean hasGamePiece() {
        return !beamBreak.get();
    }

    public void stop() {
        motor.stopMotor();
    }
}
```

## Commands Request Behavior

A command should describe the action, require the subsystem, and stop it when the action ends:

```java
public Command intakeUntilFull() {
    return run(this::intake)
        .until(this::hasGamePiece)
        .finallyDo(this::stop);
}
```

Because the command is created by the subsystem's `run()` factory, it automatically requires that subsystem. Requirements prevent two commands from controlling the same subsystem at the same time.

Detailed command composition and lifecycle are covered on the [Commands page](commands.md).

## `periodic()` and the 20-Millisecond Loop

The command scheduler calls each registered subsystem's `periodic()` method once per scheduler iteration, normally every 20 milliseconds.

Good uses of `periodic()` include:

- Updating derived mechanism state
- Applying control that must run continuously
- Logging measurements and faults
- Detecting disconnected or invalid sensors

Avoid blocking operations in `periodic()`. A long sleep, network request, or loop waiting for hardware prevents the rest of the robot code from updating on time.

## Robot Modes and Disabled Behavior

FRC robot code moves through disabled, autonomous, teleoperated, and test modes. WPILib disables normal actuator output when the robot is disabled, but software must still be designed around safe mode transitions.

Ask:

- What should happen if a command is interrupted?
- What should happen when the robot disables?
- Does the mechanism need brake or coast mode?
- Must a controller be reset when re-enabled?
- Can a sensor be re-zeroed safely?
- Should pneumatics remain in their last state?

Never assume that a command finishes normally. Disabling the robot, scheduling a conflicting command, or an exception can interrupt it.

# Common WPILib VS Code Commands

These are development-tool commands, not command-based robot actions. Open them with `Ctrl+Shift+P` and search for `WPILib`, or use the WPILib interface in VS Code.

## Build Robot Code

Compiles the project and reports Java or dependency errors. Build before deploying and after changing vendor libraries.

## Deploy Robot Code

Builds the project and sends it to the robot controller. Deployment does not prove that IDs, wiring, firmware, or units are correct, so watch the Driver Station and test carefully.

## Simulate Robot Code

Runs the robot program on the development computer. Use the **Sim GUI** option rather than VS Code's ordinary Java run button so WPILib configures simulation correctly.

## Test Robot Code

Runs the project's Java unit tests. WPILib Java projects include JUnit support, allowing conversion logic, state machines, and simulated mechanisms to be tested without a complete robot.

## Manage Vendor Libraries

Installs, removes, and updates third-party dependencies for the current project.

## Open API Documentation

Opens the installed WPILib Javadocs. This is often the fastest way to confirm constructor arguments, units, and method behavior for a WPILib class.

## Create a New Project

Creates a project from a template or example. WPILib and vendor examples are useful references, but copy only the relevant patterns and replace fake IDs, gains, and limits.

# Safety

Software controlling real hardware can damage the robot or injure someone. Treat every first deployment as if a sign, ID, or unit might be wrong.

## Before First Movement

- Keep the mechanism away from hard stops.
- Remove game pieces unless they are required for the test.
- Use a low current limit and low output at first.
- Have one person ready to disable the robot.
- Confirm which mechanism is expected to move.
- Keep hands and tools away.

## Current Limits

Current limits protect motors, controllers, breakers, wires, batteries, and mechanisms. They are not only performance settings.

Choose limits based on:

- Motor and controller capabilities
- Wire gauge and breaker
- **Expected normal load** (This is extremely important to consider so that you don't break the mechanism mechanically)
- How long the mechanism may stall
- Mechanical strength

A current limit that is never applied because configuration failed provides no protection. Check status and verify behavior.

## Soft and Hard Limits

Software soft limits stop motion based on a sensor position. Physical hard stops prevent motion mechanically. Limit switches provide another layer.

Each has failure modes:

- A soft limit fails if position is wrong.
- A limit switch fails if wiring or polarity is wrong.
- A hard stop may prevent travel but still allow the motor to stall destructively.

Use multiple layers for dangerous mechanisms, and ensure code always allows a safe direction away from a limit.

## Neutral Mode

In **brake** mode, a motor controller resists rotation when output is zero. In **coast** mode, the motor spins more freely.

Brake mode is often useful for arms, elevators, and drivetrains. Coast mode may be appropriate for flywheels and is required for the asymmetric behavior described on the [Bang-Bang Controllers page](../theory/bbc.md).

# Simulation and Testing

## Basic Device Simulation

WPILib simulation exposes many standard inputs and outputs in the Sim GUI.

Run **WPILib: Simulate Robot Code**, select Sim GUI. 

This can allow you to test how your code will react given manually hard coded situations and is especially useful when testing the state machine to make sure the commands are running like they are supposed to. 

# Debugging Physical Devices

When a device does not work, change one thing at a time and move through the layers in order.

## 1. Check Power and Wiring

- Is the device powered?
- Is the CAN chain terminated and continuous?
- Does the status LED indicate a fault?

## 2. Check the Vendor Tool

- Does the device appear?
- Does it have the expected CAN ID?
- Is firmware compatible?
- Are there duplicate IDs?
- Can the device be controlled safely from the vendor tool?

## 3. Check the Project

- Is the correct vendor dependency installed?
- Does the code construct the expected device type?
- Does the ID, channel, port, address, and bus name match?

## 4. Check Robot State

- Is the Driver Station enabled?
- Is the robot browned out or disabled?
- Is another command requiring the subsystem?
- Is a limit intentionally blocking output?
- Is the motor controller receiving a nonzero request?

## 5. Check Measurements and Units

- Does the raw sensor value change?
- Is positive direction correct?
- Are conversion factors correct?

## 6. Log the Whole Control Path

For a controlled mechanism, graph:

- Goal
- Controller setpoint
- Measurement
- Error
- Feedforward output
- Feedback output
- Requested voltage
- Applied voltage
- Current
- Faults

Logging only the final motor output hides where the wrong value was introduced.

# Additional Resources

- [WPILib Hardware APIs](https://docs.wpilib.org/en/stable/docs/software/hardware-apis/index.html)
- [WPILib Third-Party Libraries](https://docs.wpilib.org/en/stable/docs/software/vscode-overview/3rd-party-libraries.html)
- [WPILib Commands in VS Code](https://docs.wpilib.org/en/stable/docs/software/vscode-overview/wpilib-commands-vscode.html)
- [WPILib Robot Simulation](https://docs.wpilib.org/en/stable/docs/software/wpilib-tools/robot-simulation/index.html)
- [REV Robotics Documentation](https://docs.revrobotics.com/)
- [CTRE Phoenix 6 Documentation](https://v6.docs.ctr-electronics.com/)