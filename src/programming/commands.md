(Needs proofreading! Written by: Keshav)

# WPILib Commands

Commands describe robot actions: intake a game piece, drive while a button is held, move an elevator, aim, or perform an autonomous routine. Subsystems own hardware; commands coordinate subsystem behavior over time.

# The Scheduler

`CommandScheduler` runs normally every 20 milliseconds. Each iteration it:

1. Runs subsystem `periodic()` methods.
2. Polls triggers and button bindings.
3. Schedules newly requested commands.
4. Executes scheduled commands.
5. Ends finished commands.
6. Schedules default commands for free subsystems.

The command-based template calls the scheduler from `Robot.robotPeriodic()`. Do not remove that call.

# Command Lifecycle

A command has four lifecycle methods:

```java
public class MoveArmCommand extends Command {
    public MoveArmCommand(ArmSubsystem arm) {
        addRequirements(arm);
    }

    @Override
    public void initialize() {}

    @Override
    public void execute() {}

    @Override
    public boolean isFinished() {
        return false;
    }

    @Override
    public void end(boolean interrupted) {}
}
```

- `initialize()` runs once when scheduled.
- `execute()` runs every scheduler iteration.
- `isFinished()` decides when normal completion occurs.
- `end(false)` follows normal completion.
- `end(true)` follows cancellation or interruption.

Cleanup must work for both endings.

# Requirements

A command must require every subsystem it controls:

```java
addRequirements(arm, intake);
```

Only one scheduled command may require a subsystem at a time. A new conflicting command normally interrupts the old one. This prevents two actions from sending different outputs to the same hardware.

Reading another subsystem does not always require it, but controlling it does. When uncertain, prefer explicit ownership over hidden conflicts.

# Command Factories

Most actions do not need a custom command class. WPILib factories are shorter and make lifecycle intent clearer.

## Run Once

```java
runOnce(() -> setGoalMeters(1.0));
```

Runs once and immediately finishes.

## Run Continuously

```java
run(() -> motor.setVoltage(4.0));
```

Runs every loop until interrupted.

# Decorators

Decorators modify a command:

```java
command
    .andThen(command2)
    .finallyDo(command3);
```

Common decorators include:

- `withTimeout(seconds)`
- `until(condition)`
- `onlyIf(condition)`
- `andThen(nextCommand)`
- `finallyDo(cleanup)`

# Default Commands

Default commands run whenever a subsystem is free:

```java
drivetrain.setDefaultCommand(
    drivetrain.driveCommand(
        driver::getLeftY,
        driver::getLeftX,
        driver::getRightX
    )
);
```

Give important commands descriptive names. When an action does not run, ask:

- Was its trigger true?
- Was it scheduled?
- Did a requirement conflict interrupt it?
- Did it finish immediately?
- Did a deadline or race cancel it?
- Is another default command taking over afterward?

# 8726 runNextCommand and Stateful Subsystem

Read about how we manage commands inside subsystems and control our state machine which is all through commands [here. ](../projects/swervebase.md)
