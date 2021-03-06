---
layout: page
title: "LimitlessLED"
description: "Instructions on how to setup LimitlessLED within Home Assistant."
date: 2015-12-03 13:00
sidebar: true
layout: page
comments: false
sharing: true
footer: true
ha_category: Light
ha_iot_class: "Assumed State"
---

`limitlessled` can control your [LimitlessLED](http://www.limitlessled.com/) lights from within Home Assistant. The lights are also known as EasyBulb, AppLight, AppLamp, MiLight, LEDme, dekolight or iLight.

### Setup

Before configuring Home Assistant, make sure you can control your bulbs with the Milight mobile application. Discover your bridge(s) IP. You can do this via your router, or a mobile application like Fing ([android](https://play.google.com/store/apps/details?id=com.overlook.android.fing&hl=en), [itunes](https://itunes.apple.com/us/app/fing-network-scanner/id430921107?mt=8)). Keep in mind that LimitlessLED bulbs are controlled via groups. You cannot control an individual bulb via the bridge, unless it is in a group by itself. Note that you can assign an `rgbw` and `white` group to the same group number, effectively allowing 8 groups (4 `rgbw` and 4 `white`) per bridge.

To add `limitlessled` to your installation, add the following to your `configuration.yaml` file:

```yaml
light:
  platform: limitlessled
  bridges:
```

Next, list your bridges and groups. Here's an example. See the next section for a full explanaton of each configuration variable.

```yaml
light:
  platform: limitlessled
  bridges:
    - host: 192.168.1.10
      version: 5
      port: 8899
      groups:
      - number: 1
        type: rgbw
        name: Bedroom
      - number: 2
        type: white
        name: Craft Room
      - number: 2
        type: rgbw
        name: Bathroom
    - host: 192.168.1.11
      groups:
      - number: 1
        type: rgbw
        name: Living Room & Hall
```

### Configuration variables

- **bridges** (*Required*): (list)
  - **host** (*Required*): IP address of the device, eg. `192.168.1.32`
  - **version**: Bridge version (default is `5`). Don't use if you aren't sure.
  - **port**: Bridge port (default is `8899`). Normally not necessary to specify. 
  - **groups** (*Required*): (list)
    - **number** (*Required*): Group number (`1`-`4`). Corresponds to the group number on the remote.
    - **name** (*Required*): Any name you'd like. Must be unique among all configured groups.
    - **type**: Type of group. Choose either `rgbw` or `white`. `rgbw` is the default if you don't specify.

### Properties
Refer to the [light]({{site_root}}/components/light) documentation for general property usage, but keep in mind the following notes specific to LimitlessLED.

- **RGBW**
  - *Color*: There are 256 color possibilities along the LimitlessLED color spectrum. Color properties like saturation and lightness can't be used - only hue. The only exception is white (which may be warm or cold depending on the type of RGBW bulb). If you select a color with saturation or lightness, Home Assistant will calculate the nearest valid LimitlessLED color.
  - *Brightness*: There are 25 brightness steps.
- **White**
  - As you can observe on the Milight mobile application, you cannot select a specific brightness or temperature - you can only step each property up or down. There is no indication of which step you are on. This restriction, combined with the unreliable nature of LimitlessLED transmissions, means that setting white bulb properties is done on a best-effort basis. The only very reliable settings are the minimum and maximum of each property.
  - *Temperature*: There are 10 temperature steps.
  - *Brightness*: There are 10 brightness steps.
- **Transitions**
  - If a transition time is set, the group will transition between the current settings and the target settings for the duration specified. Transitions from or to white are not possible -  the color will change immediately.

### Initialization & Synchronization

When starting Home Assistant, your LimitlessLED bulbs will be set to known default values. This ensures a consistent user interface and uninterrupted turning on/off. If you control your LimitlessLED lights via the Milight mobile application or other means while Home Assistant is running, Home Assistant can not track those changes and you may observe unexpected behavior. This is due to a LimitlessLED limitation.
