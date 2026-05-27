ismail - not proofread nor complete

# SparkMax

![Image of SparkMax](../images/sparkmax.png)

Can be found in the mechanical room on the gray shelves (not to be confused with the toolcarts). They will be in a bin labeled "Spark Max". 

## Overview

Sparkmaxes are the [motor controllers](https://en.wikipedia.org/wiki/Motor_controller) of choice for 8726, at least for our [REV motors](rev_motors.md). They can be configured, analyzed, and controlled via a USB connection using [REV Hardware Client](../software/rhc.md).


## Ports

| Port         | Wire Type   | Current Draw  | Required | Where is it?            | Additional Info        |
| ------------ | ----------- | ------------- | -------- | ----------------------- | ---------------------- |
| CAN port   | Custum REV Spark Max CAN Adapter - 22 AWG |  amps  | &check;  | Side connecting to PDH, next to USB-C port | Used to connect to the [CAN bus](../electrical/CAN.md) |
| 6-pin Encoder port   | 6 x 24 AWG Encoder Wire |  amps | &check;  | Side connecting to motor  | Enables connection to the motor's encoder |
| USB-C port | USB-C cable | -? amps | &cross; | Side connecting to PDH, next to CAN port | Used to connect to [REV Hardware Client] (../software/rhc.md) |
| PDH Power Wire port | 2 x 12 AWG Power wire | ? amps | &check; | Side connecting to PDH | Used to power the SparkMax and the Motor it is controlling |
| Motor-Side Power Port | 3 x (14 iirc maybe 12) AWG Power wire | ? amps | &check; | | |

## Status Lights
