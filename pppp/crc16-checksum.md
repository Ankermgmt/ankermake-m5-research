PPPP (PPCS peer-to-peer protocol)
=================================

Ankermake M5 uses the PPPP protocol for status updates, file listing, gcode
uploads, camera streaming, and more.

Some types of PPPP messages have a 16-bit checksum at the end, while others have none.

This document describes how the checksum type was identified, using the open
source program [reveng](https://reveng.sourceforge.io/).

CRC checksum on packets
-----------------------

Let's search for "aabb020000000000000000008ade".

Assumption: `aabb` is a header/prefix, and `8ade` is the crc. Let's try brute-forcing possible CRC-16s:

```
$ for algo in $(./reveng -D | grep width=16 | cut -f 2 -d '"'); do printf "%-30s: %s\n" ${algo} "$(./reveng -m ${algo} -c 02000000000000000000)"; done
CRC-16/CDMA2000               : cac6
CRC-16/TELEDISK               : 4e46
CRC-16/T10-DIF                : 532a
CRC-16/USB                    : 2e32
CRC-16/MODBUS                 : d1cd
CRC-16/CMS                    : 4ecb
CRC-16/DDS-110                : 666b
CRC-16/MAXIM-DOW              : 5e35
CRC-16/ARC                    : a1ca
CRC-16/UMTS                   : 402b
CRC-16/OPENSAFETY-B           : 4168
CRC-16/LJ1200                 : f876
CRC-16/M17                    : 4eb9
CRC-16/OPENSAFETY-A           : 222c
CRC-16/DNP                    : 4234
CRC-16/EN-13757               : 88d9
CRC-16/PROFIBUS               : a574
CRC-16/IBM-SDLC               : 363b
CRC-16/MCRF4XX                : c9c4
CRC-16/GENIBUS                : c04c
CRC-16/IBM-3740               : 3fb3
CRC-16/ISO-IEC-14443-3-A      : b416
CRC-16/RIELLO                 : 4897
CRC-16/TMS37157               : 82e5
CRC-16/SPI-FUJITSU            : 5a73
CRC-16/KERMIT                 : 4e58
CRC-16/GSM                    : 2175
CRC-16/XMODEM                 : de8a
CRC-16/NRSC-5                 : 102e
CRC-16/DECT-R                 : 9a93
CRC-16/DECT-X                 : 9a92
```

We got a hit! It's reversed though (`de8a` -> `8ade`)

Let's verify with a more complex example (with last two bytes manually swapped):

```
./reveng -w 16 -s 00000000000089000000302c6...[REDACTED]...200cc1c
width=16  poly=0x1021  init=0x0000  refin=false  refout=false  xorout=0x0000  check=0x31c3  residue=0x0000  name="CRC-16/XMODEM"
```

Bingo! The message data, except `aabb` header, has a 16-bit CRC16/XMODEM checksum appended on the end, in big endian orientation.

The following is a python proof-of-concept of calculating the checksum:

```python
import crcmod

def ppcs_crc16(data):
    crc16 = crcmod.mkCrcFun(0x11021, rev=False, initCrc=0x0000, xorOut=0x0000)
    return struct.pack("<H", crc16(data))
```
