ismail - not proofread nor complete

# SparkMax

Sparkmaxes are the [motor controllers](https://en.wikipedia.org/wiki/Motor_controller) of choice for 8726, at least for our [REV motors](rev_motors.md). They contain ports for both encoder and CAN wires. They can also be configured, analyzed, and controlled via a USB connection using [REV Hardware Client](rhc.md).

# Ports

| Port         | Wire Type   | Current Draw  | Required | Where is it?            | Additional Info        |
| ------------ | ----------- | ------------- | -------- | ----------------------- | ---------------------- |
| CAN port   | 22 gauge |  amps  | &check;  | Side connecting to PDH, next to USB-C port | Used to connect the [CAN bus](../electrical/electrical.md) |
| Encoder port   | 22 gauge    |  amps | &check;  | Side connecting to motor  | Enables connection to the motor's encoder |
