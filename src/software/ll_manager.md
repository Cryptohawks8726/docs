(Needs proofreading! Written by: Keshav)

# Limelight Hardware Manager

## Where to download/update

Download: [Official Limelight Downloads](https://docs.limelightvision.io/docs/resources/downloads)

Download the version that matches the computer's operating system and processor. On Linux, the program is distributed as an AppImage and may need to be marked as executable before it can run.

The Hardware Manager does not automatically update itself. Check the official downloads page for a newer version, especially before flashing a Limelight or configuring Limelight.

LimelightOS is also updated manually. The Hardware Manager and LimelightOS are separate:

- **Limelight Hardware Manager** runs on the programmer's computer.
- **LimelightOS** runs on the Limelight camera.

Installing a new Hardware Manager does not update the camera. The correct LimelightOS image must be downloaded and flashed separately.

> [!IMPORTANT]
> Limelight provides different OS images for different hardware models. Verify whether the camera is a Limelight 1/2/2+, Limelight 3, Limelight 3G, Limelight 3A, or Limelight 4 before downloading an image. Flashing should follow the quick-start instructions for that exact model.

## Description

Limelight Hardware Manager is the desktop utility used to find and maintain Limelight cameras. Its two main jobs are:

1. Discover Limelights connected to the same network and open their configuration pages.
2. Flash a Limelight with a new LimelightOS image over USB.

The Manager also displays useful discovery and network information, making it easier to determine which cameras are connected and how to reach them.

The Hardware Manager is **not** where most Limelight configuration happens. Double-clicking a discovered device opens the camera's built-in web interface. That browser interface is where we configure:

- Team number
- Hostname
- Static IP address
- Camera position on the robot
- AprilTag field map
- Vision pipelines
- Exposure, gain, resolution, and frame rate
- Crosshair and targeting settings
- LED and stream settings

Those settings are hosted by the Limelight itself and are saved to the camera.

## How to use

### Find a Limelight on the robot network

1. Power the Limelight and connect it to the robot radio with Ethernet.
2. Connect the programming laptop to the robot's network.
3. Open Limelight Hardware Manager.
4. Select **Scan**.
5. Wait for the camera to appear in the device list.
6. Double-click the camera to open its web interface.

A Limelight with the default hostname may also be opened directly at:

```text
http://limelight.local
```

If a static IP has already been assigned, it can be opened with:

```text
http://<limelight-ip>
```

(If it says not found or something put :5801 at the end of the url)

The Hardware Manager is especially useful when we do not know the camera's hostname or IP address.

### Configure a new Limelight for Team 8726

After opening the camera's web interface, go to the **Settings** tab.

#### Set the team number

Set the FRC team number to:

```text
8726
```

The team number is required for the Limelight to connect to the correct NetworkTables server.

#### Set a hostname

One Limelight can use the normal `limelight` hostname. If the robot has multiple Limelights, every camera must have a unique hostname.

Use descriptive names based on physical location or purpose:

```text
limelight-front
limelight-back
limelight-left
limelight-right
```

The hostname is also used by LimelightHelpers and NetworkTables, so code must use the same name.

### Open the web interface without Hardware Manager

Hardware Manager is convenient, but it is not required every time. Once networking is configured, open the camera directly from a browser using its hostname or static IP.

The web interface provides:

- **Settings:** network, team number, hostname, and system configuration
- **Pipeline tabs:** AprilTag, color, neural-network, Python, and other vision pipelines
- **Camera and Crosshair:** exposure, gain, resolution, orientation, LED, and targeting calibration
- **3D Visualizer:** AprilTag detections and field-space localization

Changes made in the web interface are saved automatically by LimelightOS. Even so, important pipelines should be backed up before major changes.

### Update LimelightOS

Use the instructions for the exact Limelight model. The general process for current Hardware Manager releases is:

1. Back up pipelines, scripts, and important settings.
2. Power off the Limelight.
3. Download the newest Hardware Manager.
4. Download the newest compatible LimelightOS image for the camera model.
5. Connect the Limelight directly to the laptop using a USB data cable.
6. Put the camera into flash mode as described by its official quick-start guide.
7. Open Hardware Manager and select the **Flash OS** tab.
8. Select the downloaded OS image and wait for it to extract.
9. Select **Refresh Device List**.
10. Select the Limelight flash device.
11. Select **Flash Device**.
12. Wait for flashing to finish before disconnecting the camera.
13. Remove USB, reconnect normal robot power and Ethernet, and allow the camera to boot.
14. Restore and verify its network settings, camera pose, pipelines, scripts, and calibration.

Some models enter flash mode as soon as USB is connected, while others require holding the configuration button while connecting USB. Follow the model-specific guide instead of guessing.

Use a USB cable that supports **data**, not a charge-only cable. Do not disconnect the camera or close the program while flashing.

### Verify the update

After flashing:

1. Scan for the Limelight with Hardware Manager.
2. Open its web interface.
3. Confirm the reported LimelightOS version.
4. Verify the team number, hostname, and static IP.
5. Restore pipelines and scripts if necessary.
6. Confirm the correct FRC field map is installed.
7. Confirm the camera pose relative to the robot.
8. Verify NetworkTables communication.
9. Test every pipeline used by robot code.
10. Test localization with real field AprilTags before competition.

A successful flash only proves that LimelightOS was installed. It does not prove that the camera is configured correctly for the robot.

### Configure multiple Limelights

Every camera must have:

- A unique hostname
- A unique static IP
- The correct team number
- A camera pose matching its real location and orientation

For example:

| Camera | Hostname | Static IP |
| --- | --- | --- |
| Front | `limelight-front` | `10.87.26.11` |
| Back | `limelight-back` | `10.87.26.12` |

Robot code must request results from the correct hostname:

Do not leave multiple cameras with the default hostname or the same IP address. That creates discovery and NetworkTables conflicts.

### Troubleshoot a Limelight that does not appear

Work through the connection from the bottom upward.

#### Check power and wiring

- Confirm the Limelight is powered.
- Check its status lights.
- Confirm Ethernet link lights are active.
- Reseat the Ethernet cable.
- Confirm the laptop is connected to the same robot network.
- Try a known-good Ethernet cable.

#### Scan again

- Close and reopen Hardware Manager.
- Select **Scan** again.
- Allow the Limelight enough time to boot.
- Temporarily disable unrelated network adapters or VPNs if they interfere with discovery.

#### Try direct access

Try:

```text
http://limelight.local
```

and the last known static IP.

For Limelight models that support USB networking, connect with USB and use the address listed in that model's quick-start guide.

#### Check hostname resolution

If the static IP works but the `.local` hostname does not, the camera is reachable and name resolution is the problem. Older Windows installations may require Bonjour, which is available from the Limelight downloads page.

#### Check network configuration

An incorrect static IP can place the Limelight on another subnet. Use Hardware Manager discovery, a supported USB-network connection, or the model's documented network-reset procedure to recover it.

Do not flash LimelightOS merely because the browser cannot find the camera. First verify power, Ethernet, laptop networking, hostname resolution, and IP configuration.

#### The Limelight does not appear in the Flash OS tab

- Confirm the USB cable supports data.
- Connect directly to the laptop instead of through a hub.
- Follow the correct button procedure for that Limelight model.
- Wait for the computer to enumerate the flash device.
- Install the official USB/RPIBoot driver when required.
- Select **Refresh Device List** again.

If the device still does not appear, follow the model-specific flashing instructions on the official quick-start page.

### When to use Hardware Manager

Use Hardware Manager when:

- Setting up a new Limelight
- Finding a camera with an unknown IP address
- Opening the web interface
- Diagnosing which Limelights are visible
- Updating or recovering LimelightOS

You normally do not need it open while programming or operating the robot. Once configured, the Limelight communicates with robot code and dashboards over the robot network.

## Official Resources

- [Limelight Downloads](https://docs.limelightvision.io/docs/resources/downloads)
- [Limelight Documentation](https://docs.limelightvision.io/)
- [Limelight 2/2+ Quick-Start](https://docs.limelightvision.io/docs/docs-limelight/getting-started/limelight-2)
- [Limelight 3 Quick-Start](https://docs.limelightvision.io/docs/docs-limelight/getting-started/limelight-3)
- [Limelight 3G Quick-Start](https://docs.limelightvision.io/docs/docs-limelight/getting-started/limelight-3g)
- [Limelight 4 Quick-Start](https://docs.limelightvision.io/docs/docs-limelight/getting-started/limelight-4)
- [Limelight APIs and Libraries](https://docs.limelightvision.io/docs/docs-limelight/apis/limelight-lib)
