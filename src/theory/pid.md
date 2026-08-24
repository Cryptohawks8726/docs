(Needs proofreading! Written by: Keshav)

# PIDs

PID, short for Proportional-Integeral-Derivitive, is the most common feedback controller in robotics. It's job is to minimize the error(setpoint - measurement). This computation is recalculated every 20ms by default in WPILIB.

There are 3 primary ways to look at the error and calculate the voltage required to move the state which are all taken into account by the PID. 

The first way to look at the error is the simplest. How large is the error right now? The second way is how long has the error existed. And the third way is how quickly is the error changing. All 3 of these terms form the PID equation which we will see on the next page. 

Looking at only the current error is often not good enough and causes fluctuation in position around the setpoint. The PID combines all three of these factors into a single controller which produces smooth, accurate, and stable motion in many systems. This is why the PID is very common throughout many robotics fields and is the most common controller you will use on this team.
