# Core Components Reviewed

Welcome to the Electrical Docs! On this page, we're going to go over the basic components that make up our robots. This includes:
- Power Distribution Hub
- Battery and Breaker
- System Core and Roborio
- Radio
- Motor Controllers
- VRM
- Can Bus

> [!Note]
> This page only offers a quick review of components and ties them to the electrical 
> and wiring process. For more information on each of these parts, view the [Parts](../parts/parts.md)
> page.

## The Power Distribution Hub
<img src="../images/pdh.png" alt="Image of the REV Power Distribution Hub" width="300" height="300">

The Power Distribution Hub (PDH) is a central component that connects to the battery and every other component to supply power to the robot. Using fuses, it also controls the max amount of current sent to each part of the robot. 8726 has one PDH, and it is used on our competition bots. We also have an older, similar part known as the Power Distribution Panel (PDP). The PDP functions in the same way, without the small screen displaying the battery voltage. The PDH is a necesary component to the function of the robot, and it is important to place and wire it in a way that keeps it accessible for quick fixes. The PDH can also be used as a terminator of the CAN bus. 


## The Battery and Breaker
<img src="../images/robot-battery.png" alt="Image of 12V Robot Battery" width="200" height ="200">
<img src="../images/120a-breaker.png" alt="Image of 120A  Breaker" width="200" height ="200">

The 12V Battery is what supplies power to the robot. It is connected to the PDH using 6 AWG lugs and the 120A breaker. Our batteries have [SB50 connectors](https://www.andersonpower.com/product-lines/sb-connector-series/), which are used for easy connection to the robot. The negative/ground output from the SB50 connects directly to the PDH/PDP using a 6 AWG lug. The positive output from the SB50 connects to one end of the breaker, and the other end of breaker connects to the positive end of the PDH/PDP. This allows the breaker to open or close the circuit of power, providing a way to switch the power on or off. 


## System Core and RoboRio
<img src="../images/systemcore.png" alt="Image of SystemCore" width="250" height="250">
<img src="../images/roborio.png" alt="Image of NI RoborRio" width="200" height="200">

The Systemcore and RoboRio are the brains of the robot. They are what connect to the radio and the CanBus to communicate the robot code to each component. The RoboRio has been used until the 2027 FRC season, where is is being replaced by the SystemCore. The two units can be used for Pulse Width Modulation (PWM) on older motor controllers and servos. They are also used as terminators for the CAN bus, alongside the PDH.


## Radio





## Motor Controllers





## VRM





## Can Bus