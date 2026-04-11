(Needs proofreading! Written by: Dhruva)

# Building and Installing

Since this is a normal Flutter project it can be built with `flutter build windows` and `flutter run` will run
the project in debug mode. However, you may need to do a few things first.

Since this project relies on the ntcoreffi binaries published by WPILib to interface with the ntcore library,
you will need to download them first.
These binaries can be downloaded from [wpilib's maven releases](https://frcmaven.wpi.edu/ui/native/release), however, there is also
a script in this project to automatically download them for you. It's in tool/download_ntcore.dart---run it using `dart run tool/download_ntcore.dart`.

`flutter run` will run the project in debug mode and `flutter build {platform}`
will build and places a bundle in build/{platform}/{architecture}/{debug or release}/bundle
containing the executable and all project assets/libraries.
On macos, you'll need to install the [cocoapods](https://cocoapods.org/) package manager for xcode. To do this, you can use the [homebrew package manager](https://brew.sh/) (download it through github or through the terminal as shown on the website). Run `brew install cocoapods` in the terminal to install cocoapods, then run `pod setup` to complete the setup. You may need to restart your IDE and manually type `flutter run` after initially installing.

Windows additionally requires enabling developer mode to allow flutter to create symlinks. Thanks, Microslop.

## Building installers

We primarily use installers to quickly get Aluminum onto all of our laptops,
and the installer will also automatically create start menu shortcuts.

First, build the project normally (`flutter build windows --release`).
Installers are built using NSIS and the setup.nsi script in the repository root.
The fastest way to downloaded NSIS is using winget: `winget install NSIS.NSIS`.
Alternatively, you can download the installer from [here](https://nsis.sourceforge.io/Download).
Then, run the installed NSIS app and select "Compile NSI scripts", then open
the setup.nsi file in this repository. An installer will be produced in the build directory.

> [!IMPORTANT]
> Please remember to update the version number when publishing new releases -
> all you need to do is change the number at the top of pubspec.yaml!
