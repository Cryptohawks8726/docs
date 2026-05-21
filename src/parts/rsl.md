ismail - not proofread
# RSL

This part can be found in Control Cabinet 2

## What is this?

The RSL is a large LED light used to determine the current status of the robot; it is mandatory for competition.

## Ports

| Port         | Wire Type   | Current Draw  | Required | Where is it?            | Additional Info        |
| ------------ | ----------- | ------------- | -------- | ----------------------- | ---------------------- |
| La | 22 gauge | 60 miliamps  | &check;  | Bottom of RSL | Positive Terminal |
| N   | 22 gauge    | 60 miliamps | &check;  | Bottom of RSL     | Negative Terminal  |
| Lb   | 22 gauge    | 60 miliamps | &check;  | Bottom of RSL      | Positive Terminal  |

The RSL has 3 power ports. The middle port is for the negative terminal (black wire). The two outer ports are for the positive terminal (red wires).

<img src="../images/rsl.jpg" alt="RSL image" width="500" height="auto">

Run the main red wire through `La`, then connect `La` and `Lb` with a separate red wire that will act as a jumper cable. Connect `La` to `S` and `N` to Ground on the roboRIO.

## Status Lights

The robot's status is determined by 3 different modes:

| Mode           | Meaning                              |
| -------------- | ------------------------------------ |
| ON and SOLID   | Robot is on and disabled             |
| ON and BLINKING| Robot is on and enabled              |
| OFF            | Robot is off, RSL not wired properly |



## Official Documentation and Manuals

[WPILIB Hardware Overview](https://docs.wpilib.org/en/stable/docs/controls-overviews/control-system-hardware.html)

[WPILIB Status Light Reference](https://docs.wpilib.org/en/stable/docs/hardware/hardware-basics/status-lights-ref.html)
