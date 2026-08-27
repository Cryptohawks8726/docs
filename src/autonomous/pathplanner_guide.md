# Pathplanner Guide

## What is Pathplanner?

Pathplanner is a tool we use to help coordinate different pieces of paths and to schedule commands. Choreo is what we mainly use to actually create paths. Additionally, pathplanner offers sequential and parallel command groups which are very important.

## Installation and Setup

There are 2 main ways of setting up Pathplanner on your device.

### Microsoft Store

1. Head to https://pathplanner.dev/home.html and click the "Download from Microsoft Store" button. Then install Pathplanner.

### Manual Install

1. Head to https://pathplanner.dev/home.html and click the "Manual Installs can be found Here" button.

2. Download the most recent version of from Github and follow setup instructions.

## Syncing Choreo Paths into Pathplanner

1. Open up Pathplanner

2. Click the plus button and then Choreo Path button

3. Select the corresponding Choreo path and import it into Pathplanner

## Types of Commands

Pathplanner offers a plethora of different commands for the user.

 - Follow Path: This command allows the user to make the robot follow a path or section of a path.
 - Named Command: This command allows the user to run a custom named command that exists within the code.
 - Wait Command: This command allows the user to make the robot pause it's actions for a certain amount of seconds.

### Special Command Groups

We only use 2 of the command groups:
 - Sequential Command Group: Each command follows one after another.
 - Parallel Command Group: Multiple commands run at the same time.

## Implementing Commands into Paths

The reason we use Pathplanner is for it's abilities to implement commands and organize sections of paths. Usually this is the final process before a path is finalized. In order to split paths into different sections to insert commands in between, go to Choreo and select the beginning and ending waypoints of the section we want and mark the split check box.

Go back to Pathplanner and then create a Follow Path command. Select the specific section as the path.

## Reviewing Paths

Once a path is finalized, run the path in motion and make sure everything is correct.