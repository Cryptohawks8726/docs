# Choreo Guide

## What is Choreo?

Choreo is the main tool we use for creating autos. It is a trajectory planner where we can place waypoints and constraints on the robot/field to generate a optimal path that our robot can travel in. At CryptoHawks, we mainly use Choreo to generate the paths and then Pathplanner to set timings, commands, and split the Choreo paths.

## Installation and Setup

1. Download the latest Choreo version from the official GitHub Page. Link: https://github.com/SleipnirGroup/Choreo/releases

2. Open the downloaded application and follow the steps for installation.

3. Once Choreo is running, open Document Settings in the Main Menu and configure the Robot Configurations.

4. In order to run Choreo paths in robot code, make sure that ChoreoLib is installed. Follow the instructions here: https://choreo.autos/choreolib/getting-started/

## Creating Projects

Projects are basically a way to keep all your paths organized in one group for each competition/season. To create a project, click the New Project button in the main menu. To use an existing project, click the Open Project button in the main menu. To save a project, click the Save As button in the main menu. Once you are in a project for the first time, make sure to configure the robot settings before doing any other work. This is done by pening the Document Settings in the main menu.

## Project Files

When using Choreo, you will mainly be dealing with 2 file types.
1. .chor files - Overall project/configuration
2. .traj files - Each individual path

## Creating Basic Paths

Points on the field where you want the robot to go are called waypoints. Setting waypoints on the field lets the robot know where to go. The first waypoing is where the robot starts, and it will move to each waypoint numbered after it.

There are three different waypoint types (However we almost always ONLY deal with Pose Waypoints):

1. Pose Waypoint - These waypoints move the robot's location and heading
2. Translation Waypoint - These waypoints move the robot's location and not heading.
3. Empty Waypoint - These waypoints can be used to visualize the shape of the path or apply constraints without moving the robot's location or heading.

After you have created your path, click the generate button which will find the most optimal path for your robot. If it succeeds, you will be able to run your path and see how it progresses. In addition, if it generates, your work for that path will be saved. If generation fails, your work for that path is not saved and you must find a way to allow generate to pass.

## Constraints

Constraints allow us to extend our control over the path of the robot. We can control various things for example velocity, acceleration, angular velocity, and where the robot must stay in.

The tools we will mainly be using include:
 - Stop Points - These points tell the robot which waypoints it should stop briefly at.
 - Max Velocity - This allows us to control how fast the robot is moving. We mainly use it so that the robot doesn't go flying accross the field into a field piece.
 - Max Angular Velocity - This allows us to control how fast the robot is rotating. We mainly use this to stop the robot from spinning wildly.
 - Keep in Circle - This tool allows us to control where the robot MUST stay in.
 - Keep in Rectangle - This tool allows us to control where the robot MUST stay in.
 - Keep in Lane - This tool allows us to keep the robot in a straight lane if it tries to swing wildly or out.

 **WARNING: Adding too many constraints can cause the generation to fail. Start simple and try to add as little constraints as possible. In addition, most restraints will cause the path to take longer to fully finish since the robot will be moving slower.**

 ## Reviewing Paths

 After a path has generated, make sure to review the path to ensure that the robot does not hit any field elements or exceed realistic behavior.

 ## Testing Paths

 When it is time to test auto paths, start with short low risk ones first. Use a clear testing area, annouce before enabling, keep the driver station ready to disable, and verify the RSL works. Have someone keep their hands on the emergency stop at all times.