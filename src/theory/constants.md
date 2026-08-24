(Needs proofreading! Written by: Keshav)

# Constants

## What they are and why they matter

You are probably reading this now and wondering what the heck this means in this application. Remember how earlier we said that feedforward is a mathematical model which represents a system and is different for every system? Well that was mostly true but for our situations, we can use a generic equation for all feedforward systems and simply change some of the numbers (constants) and tune the model for specific systems. The basic equation that we use is: 

\\[ V = k_S \times sgn(v) + k_V \times v + k_A \times a + k_G cos(θ)\\]

In this equation, 

V = voltage (output of the equation, send to motors)

a - the desired acceleration (an input to the equation)

v - the desired velocity (an input to the equation)

kS - a constant representing the voltage required to overcome static friction

kV - a constant representing the voltage required to coast at a velocity

kA - a constant representing the voltage required to induce an acceleration

θ - an angle that determines the effect of gravity on the system -> measured from the location which the force of gravity is the greatest (parrallel to the ground) since \\(cos(0) = 1\\) 

- This would be an example θ for an arm but for a different system like an elevator you wouldn't need to include the θ since gravity is always having the same effect on the system. 
- In essense, the entire cos(θ) term varies from system to system and is simply just a measure of how much of an impact gravity will be having on the system at the current state

For most of our purposes, we usually ignore kA since we usually are controlling the velocity but in a scenario in which we needed to control the acelleration we would use that constant. These constants are not always used and in some situations we use more constants than the ones listed aboce. [^1] We will now dive in depth into these specific constants, how to tune them, and when they will be used. 

## \\(k_S\\)

\\(k_S\\) is a constant that that is very commonly used and it's purpose is to overcome static friction. In a system, there will always be a voltage that is too small to make the system move due to just not having enough power. We can make our system more accurate by accounting for this variable every time and adding this voltage to whatever voltage we want to run our motors at which is decided by the rest of the algorithm. As you can see in the equation, this constant is being multiplied by the sign of the velocity because we want to add it in the direction of the velocity and not oppose it. 

### When to use \\(k_S\\)

You will use \\(k_S\\) is mostly all of your systems. It functions to reduce the effects of static fricion on your system so unless the system doesn't have static friction then you should be using this constant. 

### Tuning \\(k_S\\)

\\(k_S\\) is probably the easiest constant to tune on this list. Start by applying a very low voltage to the system using the [motor tester](../projects/motortester.md) on [aluminum](../projects/aluminum.md)'s debug page. If the voltage you applied makes the system move, decrease the voltage. If it does not make the system move, increase the voltage. Repeat this process until you have a number with 2 decimal digits such that it itself does not cause the system to move but adding 0.01 to the number will cause the system to move. In this scenario, 2 is an arbitrary number and heavily relies on the neccessary accuracy of the system. If your mechanism can't handle being 0.01 volts off then (to put it the gen Z way) you're fried. This number can now be used as the \\(k_S\\) in the equation and you can move on to tuning other constants. 

## \\(k_V\\)

\\(k_V\\) is a constant which represents how much voltage is required to maintain a given velocity. Unlike \\(k_S\\), which only has an effect when the mechanism first starts moving, \\(k_V\\) is responsible for overcoming the continuous losses in the system such as friction and back EMF while the motor is already spinning.

Notice that in the feedforward equation, \\(k_V\\) is multiplied directly by the desired velocity. This means that if you double the velocity, the voltage contribution from \\(k_V\\) also doubles. This relationship is approximately linear for most FRC mechanisms because of how permanent-magnet DC motors work, making \\(k_V\\) one of the most important constants in a feedforward model. 

### When to use \\(k_V\\)

\\(k_V\\) should be used whenever you are controlling the velocity of a mechanism. A lot of mechanisms that we use in FRC have a target velocity so \\(k_V\\) is almost always used. 

Some of the reasons you wouldn't use \\(k_V\\) are if the system is intended to remain stationary or a different control strategy, such as a PID, is being used instead. However there are some situations in which you would use both which we will go over later. 

### Tuning \\(k_V\\)

\\(k_V\\) should be tuned after tuning \\(k_S\\)!

In order to tune \\(k_V\\) you should start with a low value for the constant and set a desired velocity. You should graph the setpoint and measurement to see how much error there is and which direction it is in.

- If the mechanism moves slower than the desired velocity you should increase \\(k_V\\). 
- If the mechanism moves faster than the desired velocity you should decrease \\(k_V\\). 

Repeat this process with multiple velocities and not only at 1 speed. Make sure you test both low values and high values if you plan for the mechanism to be at those speeds. For example, if you have flywheels trying to shoot into a goal but the distance from the goal is unknown and changing, you should tune \\(k_V\\) so that you have have one value for \\(k_V\\) that allows it to get to speed for the entire range of its velocities. 

If the mechanism works well at high speeds and not as well for lower speeds there is a good chance you need to retune \\(k_S\\) and then do \\(k_V\\). 

## \\(k_A\\)

\\(k_A\\) is a constant which represents the voltage required to accelerate a mechanism. If you want a mechanism to change it's velocity, or accelerate, extra voltage may sometimes be needed. 

In FRC, however, we are usually desire constant speeds in things like flywheeels. For that reason, it is often 0 and we completely ignore the \\(k_A\\). In some mechanisms though, it is useful so it is still here. The docs on it may not be as detailed because we don't use it that much so for more info check out WPILIB docs or other sources that are listed at the end of this chapter. 

### When to use \\(k_A\\)

\\(k_A\\) isn't used in a lot of FRC mechanisms so usually we can set it to 0 or remove the term altogether. If the mechanism only needs a desired speed or desired position the other feedforward terms or a PID controller are usually good enough. 

\\(k_A\\) becomes useful when you need to have a very accurate trajectory following predictable changes in velocity. 

### Tuning \\(k_A\\)

\\(k_A\\) should only be tuned after having accurate values for \\(k_S\\) AND \\(k_V\\). 

Start by picking an arbitrary (small) number for \\(k_A\\). Then start repeatedly accelerating and decelerating the mechanism and graph the different velocities to see how closely it follows the desired motion profile. 

- If the mechanism accelerates slower than expected, increase \\(k_A\\). 
- If the mechanism accelerates faster than expected, decrease \\(k_A\\). 

\\(k_A\\) can be difficult to manually tune so if there was a case you needed to tune \\(k_A\\), you would most likely use WPILIB's (or any other) System Idenfication tool. More on System Identification later in the chapter. As stated earler, for most mechanisms we can just leave this at 0. 

## \\(k_G\\)

\\(k_G\\) is a constant which represents the voltage required to counteract the force of gravity's effect on your mechanism. Unlike the other constants, \\(k_G\\) does not compensate for friction or inertia-it simply provides enough voltage to keep the mechanism from falling under its own weight.

The amount that gravity affects a mechanism often changes depending on its position. This is why the feedforward equation multiplies \\(k_G\\) by cos(θ). As the mechanism rotates, gravity contributes more or less torque, and the cosine term models that changing effect. For example, in an arm, (with the angle measured assuming that 0 is parallel to the ground) the torque applied by the force of gravity follows the cosine model accurately because of basic physics. (\(\tau = r F \sin\theta\), F and r are constants so are accounted for by the \\(k_G\\) term itself; the effect of gravity is greatest at the point where it is perpendicular to the ground, which we define as 0. Since cos(0)=1 we use cosine)

For mechanisms where gravity always acts with the same force, such as elevators, the cosine term is unnecessary since gravity's effect never changes. In those cases, \\(k_G\\) is simply added as a constant voltage.

### When to use \\(k_G\\)

\\(k_G\\) should be used in every mechanism in which gravity affects the system. (Uh but that's all of them.) Wrong. Mechanisms that are moving horizontally, such as the drivetrain don't need a \\(k_G\\) term because gravity doesn't affect their direction of motion. If it isn't going up and down then most likely you won't need a \\(k_G\\) term. 

Common examples include arms and pivots but you will find it used in other situations as well. 

### Tuning \\(k_G\\)

\\(k_G\\) should be tune after \\(k_S\\). 

Start by move the mechanism to the point at which gravity has the greatest effect. For most common mechanisms, this is parrallel to the ground. Make sure later that this point is set to 0 on the encoder reading. The mechanism does not always have to start as it's zero position - usually we make it start there if the actual value of the number doesn't matter as much, such as with flywheels. However, for an arm, we want the 0 position to be parrallel to the ground so we can take the cosine of it to find out how much of an effect gravity has on the torque of the arm at a given moment. 

Start by applying a very small voltage and then letting the arm go. 

- If the mechanism starts falling, increase \\(k_G\\)
- If the mechanism starts rising, decrease \\(k_G\\)

Repeat this process until the arm is able to hold itself still, without drifting up or down. 

THIS IS NOT YOUR \\(k_G\\) TERM!!

Remember earlier that we are also adding \\(k_S\\) which accounts for the static friction in the mechanism. Because this \\(k_G\\) accounts for both the effect of gravity and static friction, we have to subtract the \\(k_S\\) term from the voltage it was able to hold itself still at and then that will be the \\(k_G\\) term. 


[^1]: See [page on unorthadox control fixes](../theory/hacky_control_theory_fixes.md)
