(Needs proofreading! Written by: Keshav)

# Feedback Algorithms

While feedforward algorithms are powerful, they can't account for disturbances which (realistically speaking) always occur. Feedback algorithms are exactly what they sound like. They take feedback from the system and use the error (distance between measurement and setpoint) to calculate the desired voltages. In FRC, we primarily use 2 types of feedback algorithms. PIDs and Bang Bang Controllers. PIDs are more accurate yet take slightly longer to reach their setpoint while bang bang controllers are fast at reaching their setpoint but often overshoot and aren't as accurate. Both of these controllers have their own unique use cases which is why we use them. We will to further into detail about these 2 feedback algorithms now.
