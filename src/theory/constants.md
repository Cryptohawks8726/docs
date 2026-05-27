(Needs proofreading! Written by: Keshav)

# Constants

## What they are and why they matter

You are probably reading this now and wondering what the heck this means in this application. Remember how earlier we said that feedforeward is a mathematical model which represents a system and is different for every system? Well that was mostly true but for our situations, we can use a generic equation for all feedforeward systems and simply change some of the numbers (constants) and tune the model for specific systems. The basic equation that we use is: 

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


[^1]: See [page on uncommon constants](../theory/uncommon_constants.md)
