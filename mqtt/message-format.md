MQTT Message structure
======================

| Offset       | Size           | Type     | Name             | Description                                                   |
|--------------|----------------|----------|------------------|---------------------------------------------------------------|
| 0            | 1              | char     |                  | Magic constant: 'M'                                           |
| 1            | 1              | char     |                  | Magic constant: 'A'                                           |
| 2            | 2              | short    | `.length`        | length of packet, including header and checksum (minimum 65). |
| 4            | 1              | char     |                  | Magic constant: 5                                             |
| 5            | 1              | char     |                  | Magic constant: 1                                             |
| 6            | 1              | char     |                  | Magic constant: 2                                             |
| 7            | 1              | char     |                  | Magic constant: 5                                             |
| 8            | 1              | char     |                  | Magic constant: 'F'                                           |
| 9            | 1              | byte     | `.package_type`  |                                                               |
| 10           | 2              | short    | `.package_num`   | maybe for fragmented messages?                                |
| 12           | 4              | int      | `.time`          | `gettimeofday()` in whole seconds                             |
| 16           | 40             | char[40] | `.device_guid[]` | device guid, as hex string                                    |
| 56           | 8              | char[8]  |                  | padding bytes, allways zero                                   |
| 64           | `.length` - 65 | byte[]   | `.data`          | message data                                                  |
| `.length` -1 | 1              | byte     | `.checksum`      | xor-based checksum. Must equal the xor of all previous bytes. |
