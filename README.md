Ankermake M5 Research
=====================

Welcome! This repository contains research notes, reverse engineering work,
protocol descriptions, etc, for the [Ankermake M5](https://www.ankermake.com/m5?ref=naviMenu) 3d printer.

This work is in ABSOLUTELY NO WAY endorsed, affiliated with, or supported by Anker.

The goal of this project is to enable the Ankermake M5 printer to be used freely
and without limitations. This will enable it to be integrated with existing and
future Open Source tools and programs.

This repository is probably *not* very helpful for people who just want to use
their printer. Rather, it is for programmers and power users who want to help
get the most out of the hardware they purchased.

As we learn more about the internals of the Ankermake printer, we will document
the findings here. Learn more about:

 - How Ankermake Slicer [stores your saved credentials](ankerslicer/login.md).
 - The types of [MQTT message](mqtt/message-types.md) that the printer understands.
 - How to calculate the [specific type of crc16](pppp/crc16-checksum.md) used in the pppp protocol.
 - The [complicated file transfer protocol](pppp/file-transfer-protocol.md) used for sending print jobs.

This repository is meant to document how the printer works, so don't expect any
code here, except perhaps minor snippets for documentation purposes.
