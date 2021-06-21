
# Home Assistant Dahua Integration
The `Dahua` [Home Assistant](https://www.home-assistant.io) integration allows you to integrate your [Dahua](https://www.dahuasecurity.com/) cameras and doorbells in Home Assistant. It's also confirmed to work with some Lorex cameras.

Supports motion events, alarm events (and others), enabling/disabling motion detection, switches for infrared, illuminator (white light), security lights (red/blue flashers), sirens, doorbell button press events, and more.

Also exposes several services to enable/disable motion detection or set the text overlay on the video.

**NOTE**: Using the switch to turn the infrared light on/off will disable the "auto" mode. Use the service to enable auto mode again (or the camera UI).

## Installation

If you want live-streaming, make sure to add the following to your config.yaml:
```
ffmpeg:
stream:
```
See [ffmpeg](https://www.home-assistant.io/integrations/ffmpeg/) and [stream](https://www.home-assistant.io/integrations/stream/).


### HACS install
To install with [HACS](https://hacs.xyz/):

1. Click on HACS in the Home Assistant menu
2. Click on `Integrations`
3. Click the `EXPLORE & ADD REPOSITORIES` button
4. Search for `Dahua`
5. Click the `INSTALL THIS REPOSITORY IN HACS` button
6. Restart Home Assistant
7. Configure the camera by going to `Configurations` -> `Integrations` -> `ADD INTERATIONS` button, search for `Dahua` and configure the camera.

### Manual install
To manually install:

```bash
# Download a copy of this repository
$ wget https://github.com/rroller/dahua/archive/dahua-main.zip

# Unzip the archive
$ unzip dahua-main.zip

# Move the dahua directory into your custom_components directory in your Home Assistant install
$ mv dahua-main/custom_components/dahua <home-assistant-install-directory>/config/custom_components/
```

> :warning: **After executing one of the above installation methods, restart Home Assistant. Also clear your browser cache before proceeding to the next step, as the integration may not be visible otherwise.**

### Setup
1. Now the integration is added to HACS and available in the normal HA integration installation, so...
2. In the HA left menu, click `Configuration`
3. Click `Integrations`
4. Click `ADD INTEGRATION`
5. Type `Dahua` and select it
6. Enter the details:
    1. **Username**: Your camera's username
    2. **Password**: Your camera's password
    3. **Address**: Your camera's address, typically just the IP address
    4. **Port**: Your camera's HTTP port. Default is `80`
    5. **RTSP Port**: Your camera's RTSP port, default is `554`. Used to live stream your camera in HA
    6. **RTSP Streams**: The RTSP stream you want to use (Main, Sub, or Both). If both, two camera entities will be created
    7. **Events**: The integration will keep a connection open to the camera to capture motion events, alarm events, etc.
       You can select which events you want to monitor and report in HA. If no events are selected then the connection will no be created.
       If you want a specific event that's not listed here open an issue and I'll add it.

![Dahua Setup](static/setup1.png)


# Known supported cameras
This integration should word with most Dahua cameras and doorbells. It has been tested with very old and very new Dahua cameras.

Doorbells will have a binary sensor that captures the doorbell pressed event.

These devices are confirmed as working:

* IPC-T5442T-ZE
* IPC-HDW5831R-ZE
* IPC-HDW2831T-ZS-S2
* IPC-HDW3849HP-AS-PV
* IPC-HFW4433F-ZSA
* IPC-HFW1435S-W
* IPC-HFW1435S-W-S2
* IPC-HDW4233C-A
* LNB8005-C
* LNE8964AB
* IPC-A26
* DHI-VTO2202F-P
* DHI-VTO2211G-P
* Please let me know if you've tested with additional cameras

# Known Issues
* IPC-D2B20-ZS doesn't work. Needs a [wrapper](https://gist.github.com/gxfxyz/48072a72be3a169bc43549e676713201), [7](https://github.com/bp2008/DahuaSunriseSunset/issues/7#issuecomment-829513144), [8](https://github.com/mcw0/Tools/issues/8#issuecomment-830669237)

# Events
Events are streamed from the device and then fired on the Home Assistant event bus.

TODO: Expand on this and how examples of how to consume the events.

# Services and Entities
Note for ease of use, the integration tries to determine if your device supports certain services, entities and will conditionally add them. But that's sometimes a little hard so it'll just add the entity even if your devices doesn't support.
I'd rather opt into extra entities than to create a complicated flow to determine what's supported and what isn't. You can simply disable the entities you don't want. An example of this is the "door open state" for doorbells. Not all doorbells support this.

## Services
Service | Parameters | Description
:------------ | :------------ | :-------------
`camera.enable_motion_detection` | | Enables motion detection
`camera.disable_motion_detection` | | Disabled motion detection
`dahua.set_infrared_mode` | `target`: camera.cam13_main <br /> `mode`: Auto, On, Off <br /> `brightness`: 0 - 100 inclusive| Sets the infrared mode. Useful to set the mode back to Auto
`dahua.set_video_profile_mode` | `target`: camera.cam13_main <br /> `mode`: Day, Night| Sets the video profile mode to day or night
`dahua.set_channel_title` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `text1`: The text 1<br /> `text2`: The text 2| Sets the channel title
`dahua.set_text_overlay` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `group`: The group, used to apply multiple of text as an overly, e.g.: 1 <br /> `text1`: The text 1<br /> `text3`: The text 3 <br /> `text4`: The text 4 <br /> `text2`: The text 2 | Sets the text overlay on the video
`dahua.set_custom_overlay` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `group`: The group, used to apply multiple of text as an overly, e.g.: 0 <br /> `text1`: The text 1<br /> `text2`: The text 2 | Sets the custom overlay on the video
`dahua.enable_channel_title` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `enabled`: True to enable, False to disable | Enables or disables the channel title overlay on the video
`dahua.enable_time_overlay` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `enabled`: True to enable, False to disable | Enables or disables the time overlay on the video
`dahua.enable_text_overlay` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `group`: The group, used to apply multiple of text as an overly, e.g.: 0 <br /> `enabled`: True to enable, False to disable | Enables or disables the text overlay on the video
`dahua.enable_custom_overlay` | `target`: camera.cam13_main <br /> `channel`: The camera channel, e.g.: 0 <br /> `group`: The group, used to apply multiple of text as an overly, e.g.: 0 <br /> `enabled`: True to enable, False to disable | Enables or disables the custom overlay on the video


## Camera
This will provide a normal HA camera entity (can take snapshots, etc)

## Switches
Switch |  Description |
:------------ | :------------ |
Motion | Enables or disables motion detection on the camera
Siren | If the camera has a siren, will turn on the siren. Note, it seems sirens only stay on for 10 to 15 seconds before switching off
Disarming Linkage | Newer firmwares have a "disarming" feature (not sure what it is, but some people use it). This allows one to turn it on/off. This is found in the UI by going to Event -> Disarming

## Lights
Light |  Description |
:------------ | :------------ |
Infrared | Turns on/off the infrared light. Using this switch will disable the "auto" mode. If you want to enable auto mode again then use the service to enable auto. When in auto, this switch will not report the on/off state.
Illuminator | If the camera has one, turns on/off the illuminator light (white light). Using this switch will disable the "auto" mode. If you want to enable auto mode again then use the service to enable auto. When in auto, this switch will not report the on/off state.
Security | If the camera has one, turns on/off the security light (red/blue flashing light). This light stays on for 10 to 15 seconds before the camera auto turns it off.

## Binary Sensors
Sensor |  Description |
:------------ | :------------ |
Motion | A sensor that turns on when the camera detects motion
Button Pressed | A sensor that turns on when a doorbell button is pressed
Others | A binary senor is created for evey event type selected when setting up the camera (Such as cross line, and face detection)

# Local development
If you wish to work on this component, the easiest way is to follow [HACS Dev Container README](https://github.com/custom-components/integration_blueprint/blob/master/.devcontainer/README.md). In short:

* Install Docker
* Install Visual Studio Code
* Install the devcontainer Visual Code plugin
* Clone this repo and open it in Visual Studio Code
* View -> Command Palette. Type `Tasks: Run Task` and select it, then click `Run Home Assistant on port 9123`
* Open Home Assistant at http://localhost:9123

# Debugging
Add to your configuration.yaml:

```yaml
logger:
  default: info
  logs:
    custom_components.dahua: debug
```
