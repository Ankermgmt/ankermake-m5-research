Ankermake M5 Research
=====================

Welcome! This repository contains research notes, reverse engineering work,
protocol descriptions, etc, for the [Ankermake M5](https://www.ankermake.com/m5?ref=naviMenu) 3d printer.

This work is in ABSOLUTELY NO WAY endorsed, affiliated with, or supported by Anker.

The goal of this project is to enable the Ankermake M5 printer to be used freely
and without limitations. This will enable it to be integrated with existing and
future Open Source tools and programs.

As we learn more about the internals of the Ankermake printer, we will document
the findings here:

 - Learn how Ankermake Slicer [stores your saved credentials](ankerslicer/login.md).
 - View of list of [MQTT message types](mqtt/message-types.md).
 - How to calculate the [specific type of crc16](pppp/crc16-checksum.md) used in the pppp protocol.

This repository is meant to document how the printer works, so don't expect any
code here, except perhaps minor snippets for documentation purposes.
