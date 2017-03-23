---
layout: post
title: "Making an open source security system: part 1"
date: "2017-03-21 18:45:54 +0100"
author: L-Cafe
category: SafeSpace
tags: security IoT GitHub
---

It has been a while since I wanted to make an alarm system. This idea has been on my mind ever since I got an alarm from [Chuango](http://www.chuango.com/english/). They are a burglar alarm vendor that releases kits you can install yourself. These kits communicate over wired and wireless connections, have no maintenance fees and have varying features depending on the kit chosen (some kits have GSM and PSTN, some others have WiFi, etc).

**First issue**: lack of encryption. After a little investigation I found out that, despite their claims on the official website, the communications with the wireless sensors were not encrypted.

These devices use a standard 433 Mhz radio band, and communicate with a central alarm panel, that has GSM and PSTN features. The GSM in this control panel is GPRS, so, again, not very secure, because GPRS is vulnerable to somewhat easy attacks, can be spoofed, intercepted and decrypted, jammed, etc. I found this out after I ordered an SDR dongle and tuned it to the appropriate frequency. Their command sequences work as follows: Every device seems to have a unique identifier, that is sent right before the command. So, on every command sent from a remote, the remote sends its own ID, and the disarm/arm command. This works because the control panel, when pairing devices, reads the first part of the command and stores it in an internal database, so every time the control panel receives a command, it first compares the first part of the command, containing the ID, and if it matches what's stored in memory, it executes the second part.

My investigation suggests there are at least 9 remote commands: arm, partial arm (home arm), disarm, panic button, duress alarm, sensor trigger, low battery, tamper trigger, and finally, normal alarm.

Some devices only listen for signals, like sirens, which are triggered on arming and disarming, and obviously on panic triggers and normal alarms.

Some other devices only send signals, such as remote controls and presence sensors.

The **second issue** with these home alarms is that their GSM module isn't really configurable, and seems to be making regular connections to (presumably) a control center you can access from a smartphone application. There is no way to disable network Internet connectivity unless the SIM card carrier offers this possibility (the alarm panel can be controlled through SMS).

**Third issue** is that their PSTN (Public Switched Telephone Network), or basically, what's known as "landline phones" feature is extremely intrusive and doesn't respect the whitelisted phone number list, so anyone who dials in the PSTN phone number, will get 5 dial tones, and after that, the alarm's PSTN will be triggered, letting anyone enter the code to set up the alarm, disarm, arm, and so on. This is both a good and a bad thing: Good because you can access your alarm from anywhere in the world you have access to a landline phone. Bad because it tends to interrupt conversations if you pick up before the 5th tone, or might interfere picking up after the 5th tone: It won't stop talking, asking for a passcode, on regular intervals even when there's another phone picked up in the line.

**Fourth issue**: it's not a very fully featured device and lacks modern functionality such as IoT connectivity, interoperability with other brands and devices, video feeds.

**Fifth issue**: it's not very reliable. The alarm will sometimes fail to be triggered on wireless sensors (I'm still investigating if this is a problem with the sensors themselves not sending the signal, but I do know that the PIR sensor doesn't detect presence very well). The alarm panel is touch enabled, but sometimes stops responding to touch, so you can't really interact with it.

In general, it's a device that might suit some people's needs, but it isn't very reliable. I believe that using open source hardware and software, and the help of the community, we can bring a new meaning to DIY alarm systems, and create a more reliable, modular, and customisable solution that will fit everyone's need while staying as cheap as the hardware can get.

I will soon start developing an alternative open source burglar alarm in [GitHub](https://github.com/L-Cafe/SafeSpace). Based on Arduino, and maybe Raspberry Pi.
