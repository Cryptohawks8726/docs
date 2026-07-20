(Needs proofreading! Written by: Keshav)

# RoboRIO

![image alt text](image_of_part.jpg)

This part can be found in Control Cabinet 1. 

## What is this?

The roboRIO is the robot's main controller. It runs the robot program we
deploy with WPILib Java, communicates with the Driver Station, and connects
the code to sensors and motor controllers. Commands and subsystems execute on
the roboRIO, while motor controllers do the high-current work of driving the
motors. The roboRIO is therefore a control computer, not a replacement for a
PDP/PDH or a motor controller.

## Ports

| Port | Wire type | Required | Where is it? | Additional info |
| --- | --- | --- |  --- | --- |
| Power input | Uh red and black power wires | &check; | Power connector on the roboRIO | Connect to the PDP/H. Verify polarity before powering on. |
| Ethernet | Standard Ethernet cable  | &check; | Ethernet jack | Wired to the robot radio |
| USB device (Type-B) | USB Type-B cable | &cross; | USB device jack | Used to connect a computer for roboRIO imaging |
| CAN bus | CAN-H/CAN-L twisted pair using the team's approved CAN wiring | &check; | CAN connector | Connects CAN motor controllers and sensors. Keep CAN-H and CAN-L paired, observe polarity, and terminate the bus correctly. |
| A whole lot of others | Varies - there is multiple types |&cross; | Everywhere | We never use these and they are very old. They are for older moters which don't use the CAN protocol but we use modern motors which support CAN |

## Status Lights

The LEDs are useful for diagnosing a robot before opening the code. The
roboRIO status-light meanings are:

| Light | Normal meaning | If it is not normal |
| --- | --- | --- |
| Power | Green: power is good. | Amber indicates brownout protection; red indicates a power fault or user-rail problem. Check robot power, connectors, and wiring. |
| Status | Off after a normal boot. | Two blinks indicate a software error; three blinks indicate Safe Mode; four blinks indicate repeated software crashes. Reboot and reimage if the problem remains. |
| Comm | Solid green: good Driver Station communication. | Off means no communication; solid red means the Driver Station is connected but user code is not running; blinking red means E-stop. |
| Mode | Off when outputs are disabled, orange in autonomous, green in teleoperated, and red in test. | A mode that does not match the Driver Station usually indicates a communication or enable-state issue. |

Use the [WPILib status light quick reference](https://docs.wpilib.org/en/stable/docs/hardware/hardware-basics/status-lights-ref.html)
for the complete list. Do not repeatedly enable a robot while a power or
software fault is present.

## Software Updating

The RIO Firmware has to be manually updated every season using the [RIO Imaging Tool](../software/rio_tool.md).

## Official Documentation and Manuals

- [WPILib: Imaging a roboRIO 1](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-3/imaging-your-roborio.html)
- [WPILib: Imaging a roboRIO 2](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-3/roborio2-imaging.html)
- [WPILib: Status light quick reference](https://docs.wpilib.org/en/stable/docs/hardware/hardware-basics/status-lights-ref.html)
- [WPILib: Recovering a roboRIO using Safe Mode](https://docs.wpilib.org/en/stable/docs/software/roborio-info/recovering-a-roborio-using-safe-mode.html)
- [WPILib: FRC Game Tools](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-2/frc-game-tools.html)
- [Our RoboRIO Imaging Tool page](../software/rio_tool.md)
