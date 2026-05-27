(Needs proofreading! Written by: Keshav)

# Constants- What they are and why they matter

You are probably reading this now and wondering what the heck this means in this application. Remember how earlier we said that feedforeward is a mathematical model which represents a system and is different for every system? Well that was mostly true but for our situations, we can use a generic equation for all feedforeward systems and simply change some of the numbers (constants) and tune the model for specific systems. The basic equation that we use is: 

\\[ V = k_S * sgn(v) + k_V * v + k_A * a + k_G cos(θ)\\]

In this equation, 
V = voltage (output of the equation, send to motors)
a - the desired acceleration (an input to the equation)
v - the desired velocity (an input to the equation)
kS - a constant representing the voltage required to overcome static friction
kV - a constant representing the voltage required to coast at a velocity
kA - a constant representing the voltage required to induce an acceleration

For most of our purposes, we usually ignore kA and a
