(Needs proofreading! Written by: Keshav)

# Feedback vs Feedforward

### Feedforward Control

Feedforward Control is using a mathematical model of a system to calculate voltage given a setpoint. Note how it doesn't say error and it only says setpoint. That is intentional. Feedforward Control doesn't rely on the state or measurements or error of the system. This means that if a system is being controlled using purely feedforward, the voltage applied as an input will always stay constant throughout - even when the system reaches its setpoint. Usually when you use this type of control the setpoints are almost always velocities. Keep in mind that feedforward models vary from system to system because each behaves differently. Theoretical Equations can be found through physics since all movement is caused by a force and motors are applying a force when voltage is applied for them. However, data collection is usually more accurate in reflecting a system as these equations are theoretical. When tuning feedforward we typically use the actual system and collect data on that to get our model. 

### Feedback Control

Feedback Control is
