(Needs proofreading! Written by: Keshav)

# CTRE Motors

This page covers CTRE Talon FX motor controllers using Phoenix 6 and WPILib Java. On our robots, Talon FX controllers are commonly integrated into Kraken x60 motors.

# Installing Phoenix 6

Install Phoenix 6 with the WPILib Dependency Manager and commit its vendor JSON. Use Phoenix Tuner to set CAN IDs, update firmware, test devices, and inspect faults. 

Phoenix 6 Java packages begin with:

```java
com.ctre.phoenix6
```

# Constructing a Talon FX

```java
private final TalonFX motor = new TalonFX(10);
```

For a named CAN bus:

```java
private final TalonFX motor =
    new TalonFX(10, "rio");

private final TalonFX swerveMotor =
    new TalonFX(11, "swerve");
```

The ID and bus must match Phoenix Tuner X.

# Configuration

Use a `TalonFXConfiguration` to describe the complete desired state:

```java
TalonFXConfiguration config =
    new TalonFXConfiguration();

config.MotorOutput.Inverted =
    InvertedValue.CounterClockwise_Positive;
config.MotorOutput.NeutralMode =
    NeutralModeValue.Brake;

config.CurrentLimits.SupplyCurrentLimit = 40.0;
config.CurrentLimits.SupplyCurrentLimitEnable = true;

motor.getConfigurator().apply(config);
```

A new full configuration contains factory-default values for fields we did not change. Applying it establishes a repeatable baseline without a separate factory-default call.

# Mechanism Units

The Talon FX integrated sensor natively measures rotor rotations and rotations per second. `SensorToMechanismRatio` lets device status signals and closed-loop requests use mechanism rotations.

For a 100:1 reduction:

```java
config.Feedback.SensorToMechanismRatio = 100.0;
```

After applying it, a position of `0.25` represents one quarter of a mechanism rotation, not 0.25 motor rotations.


# Control Requests

Phoenix 6 represents each control mode with a reusable request object. 

## Voltage

```java
import com.ctre.phoenix6.controls.VoltageOut;

private final VoltageOut voltageRequest =
    new VoltageOut(0.0);

public void setVoltage(double volts) {
    motor.setControl(
        voltageRequest.withOutput(volts)
    );
}
```

Voltage requests are preferred when applying calculated feedforward or when repeatable response across battery voltage matters.

## Stopping

```java
motor.stopMotor();
```

Phoenix also provides neutral and static-brake requests for specific behaviors. Understand the difference between sending zero, neutral output, brake neutral mode, and an active static-brake request before using them.

# Onboard Closed-Loop Control

## Configuring Gains

```java
config.Slot0.kP = kP;
config.Slot0.kI = 0.0;
config.Slot0.kD = kD;
config.Slot0.kS = kS;
config.Slot0.kV = kV;
config.Slot0.kA = kA;
config.Slot0.kG = kG;
```

Phoenix supports multiple gain slots for mechanisms that need different configurations. Configure the correct gravity type and static-feedforward sign behavior for the mechanism and request type.

## Position

```java
import com.ctre.phoenix6.controls.PositionVoltage;

private final PositionVoltage positionRequest =
    new PositionVoltage(0.0).withSlot(0);

public void setPositionRotations(double rotations) {
    motor.setControl(
        positionRequest.withPosition(rotations)
    );
}
```

## Velocity

```java
import com.ctre.phoenix6.controls.VelocityVoltage;

private final VelocityVoltage velocityRequest =
    new VelocityVoltage(0.0).withSlot(0);

public void setVelocityRps(double rotationsPerSecond) {
    motor.setControl(
        velocityRequest.withVelocity(
            rotationsPerSecond
        )
    );
}
```

# Current Limits and Output Limits

Phoenix distinguishes **supply current**, drawn from the battery, from **stator current**, flowing through motor windings. They protect different parts of the system and affect behavior differently.

Also configure:

- Peak forward and reverse voltage
- Hardware and software limits
- Closed-loop output limits
- Neutral mode
- Ramp rates when needed

Current limiting is not a substitute for preventing a mechanism from repeatedly hitting a hard stop.

# Faults and Diagnostics

Phoenix exposes active and sticky fault signals. Useful diagnostics include:

- Supply voltage
- Motor voltage
- Supply and stator current
- Device temperature
- Position and velocity
- Closed-loop reference and error
- Reset-during-enable faults
- Hardware, undervoltage, and communication faults

Use Phoenix Tuner X self-test and logs alongside robot telemetry.

# Additional Resources

- [Phoenix 6 Documentation](https://v6.docs.ctr-electronics.com/)
- [Phoenix 6 General API Usage](https://v6.docs.ctr-electronics.com/en/stable/docs/api-reference/api-usage/index.html)
- [Talon FX Control](https://v6.docs.ctr-electronics.com/en/stable/docs/api-reference/device-specific/talonfx/index.html)
