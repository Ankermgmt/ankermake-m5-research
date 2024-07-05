Ankermake MQTT message types
============================

The Ankermake M5 supports an MQTT-based protocol between the printer, the slicer and the mobile app.

The following message types have been put together from reverse engineering, but is expected to be fairly complete in terms of message types.

More work is needed to determine the exact usage of each command type.

| Value | Hex   | Official name                        | Description                                                               | Fields                                  |
|-------|-------|--------------------------------------|---------------------------------------------------------------------------|-----------------------------------------|
| 1000  | 0x3e8 | `ZZ_MQTT_CMD_EVENT_NOTIFY`           |                                                                           |                                         |
| 1001  | 0x3a9 | `ZZ_MQTT_CMD_PRINT_SCHEDULE`         |                                                                           |                                         |
| 1002  | 0x3ea | `ZZ_MQTT_CMD_FIRMWARE_VERSION`       | Not implemented?                                                          | -                                       |
| 1003  | 0x3eb | `ZZ_MQTT_CMD_NOZZLE_TEMP`            | Set nozzle temperature in units of 1/100th deg C (i.e.31337 is 313.37C)   | value=int                               |
| 1004  | 0x3ec | `ZZ_MQTT_CMD_HOTBED_TEMP`            | Set hotbed temperature in units of 1/100th deg C (i.e. 1337 is 13.37C)    | value=int                               |
| 1005  | 0x3ed | `ZZ_MQTT_CMD_FAN_SPEED`              | Set fan speed                                                             | value=?                                 |
| 1006  | 0x3ee | `ZZ_MQTT_CMD_PRINT_SPEED`            | ? Set print speed                                                         | value=number                            |
| 1007  | 0x3ef | `ZZ_MQTT_CMD_AUTO_LEVELING`          | (probably) Perform auto-levelling procedure                               | value=?                                 |
| 1008  | 0x3f0 | `ZZ_MQTT_CMD_PRINT_CONTROL`          |                                                                           | value=?, userName=string, filePath=path |
| 1009  | 0x3f1 | `ZZ_MQTT_CMD_FILE_LIST_REQUEST`      | Request on-board file list (`value == 1`) or usb file list (`value != 1`) | value=int                               |
| 1010  | 0x3f2 | `ZZ_MQTT_CMD_GCODE_FILE_REQUEST`     |                                                                           | filepath=path, type=?                   |
| 1011  | 0x3f3 | `ZZ_MQTT_CMD_ALLOW_FIRMWARE_UPDATE`  |                                                                           | value=?                                 |
| 1020  | 0x3fc | `ZZ_MQTT_CMD_GCODE_FILE_DOWNLOAD`    |                                                                           |                                         |
| 1021  | 0x3fd | `ZZ_MQTT_CMD_Z_AXIS_RECOUP`          | ?                                                                         | value=?                                 |
| 1022  | 0x3fe | `ZZ_MQTT_CMD_EXTRUSION_STEP`         | (probably) run the extrusion stepper                                      | value=int                               |
| 1023  | 0x3ff | `ZZ_MQTT_CMD_ENTER_OR_QUIT_MATERIEL` | maybe related to filament change?                                         | flag=?, stepLen=?, temperature=?        |
| 1024  | 0x400 | `ZZ_MQTT_CMD_MOVE_STEP`              |                                                                           |                                         |
| 1025  | 0x401 | `ZZ_MQTT_CMD_MOVE_DIRECTION`         |                                                                           |                                         |
| 1026  | 0x402 | `ZZ_MQTT_CMD_MOVE_ZERO`              | (probably) Move to home position                                          | value=?                                 |
| 1027  | 0x403 | `ZZ_MQTT_CMD_APP_QUERY_STATUS`       |                                                                           |                                         |
| 1028  | 0x404 | `ZZ_MQTT_CMD_ONLINE_NOTIFY`          |                                                                           |                                         |
| 1029  | 0x405 | `ZZ_MQTT_CMD_APP_RECOVER_FACTORY`    |                                                                           |                                         |
| 1031  | 0x407 | `ZZ_MQTT_CMD_BLE_ONOFF`              | (probably) Enable/disable Bluetooth Low Energy ("ble") radio              | value=int                               |
| 1032  | 0x408 | `ZZ_MQTT_CMD_DELETE_GCODE_FILE`      | (probably) Delete specified gcode file                                    | filePath="path"                         |
| 1032  | 0x409 | `ZZ_MQTT_CMD_RESET_GCODE_PARAM`      | ?                                                                         | -                                       |
| 1034  | 0x40a | `ZZ_MQTT_CMD_DEVICE_NAME_SET`        |                                                                           | devName="str"                           |
| 1035  | 0x40b | `ZZ_MQTT_CMD_DEVICE_LOG_UPLOAD`      |                                                                           |                                         |
| 1036  | 0x40c | `ZZ_MQTT_CMD_ONOFF_MODAL`            | ?                                                                         | value=?, nickName=?                     |
| 1037  | 0x40d | `ZZ_MQTT_CMD_MOTOR_LOCK`             | ?                                                                         | value=?                                 |
| 1038  | 0x40e | `ZZ_MQTT_CMD_PREHEAT_CONFIG`         | ?                                                                         | ?                                       |
| 1039  | 0x40f | `ZZ_MQTT_CMD_BREAK_POINT`            |                                                                           | value=?                                 |
| 1040  | 0x410 | `ZZ_MQTT_CMD_AI_CALIB`               |                                                                           |                                         |
| 1041  | 0x411 | `ZZ_MQTT_CMD_VIDEO_ONOFF`            | ?                                                                         |                                         |
| 1042  | 0x412 | `ZZ_MQTT_CMD_ADVANCED_PARAMETERS`    | ?                                                                         | ? (*many*)                              |
| 1043  | 0x413 | `ZZ_MQTT_CMD_GCODE_COMMAND`          | Run custom GCode command                                                  | cmdLen=int, cmdData=str                 |
| 1044  | 0x414 | `ZZ_MQTT_CMD_PREVIEW_IMAGE_URL`      |                                                                           |                                         |
| ....  | ....  | ....                                 | ....                                                                      | ....                                    |
| 1049  | 0x419 | `ZZ_MQTT_CMD_SYSTEM_CHECK`           | ?                                                                         | value=?                                 |
| 1050  | 0x41a | `ZZ_MQTT_CMD_AI_SWITCH`              | ?                                                                         | AISwitch=?, AIValue=?, AIPausePrint=?   |
| 1051  | 0x41b | `ZZ_MQTT_CMD_AI_INFO_CHECK`          |                                                                           |                                         |
| 1052  | 0x41c | `ZZ_MQTT_CMD_MODEL_LAYER`            |                                                                           | total_layer=int, real_print_layer=int   |
| 1053  | 0x41d | `ZZ_MQTT_CMD_MODEL_DL_PROCESS`       |                                                                           |                                         |
| ....  | ....  | ....                                 | ....                                                                      | ....                                    |
| 1055  | 0x41f | `ZZ_MQTT_CMD_PRINT_MAX_SPEED`        |                                                                           |                                         |
| ....  | ....  | ....                                 | ....                                                                      | ....                                    |
| 2018  | 0x7e2 | `ZZ_STEST_CMD_GCODE_TRANSPOR`        | ?                                                                         | ?                                       |
| ....  | ....  | ....                                 | ....                                                                      | ....                                    |
| 3000  | 0xbb8 | `ZZ_MQTT_CMD_ALEXA_MSG`              |                                                                           |                                         |
