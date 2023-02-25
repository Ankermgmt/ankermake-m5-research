PPPP file upload protocol
=========================

It is possible to upload gcode files to the Ankermake M5 for printing, using the
PPPP protocol. Currently, no known Open-Source implementations of PPPP exist,
but we have managed to transfer files successfully to a printer in a controlled
setting. That code is far from production quality at the moment.

Sending bulk data to the printer is done over the `PPPP` protocol using `DRW`
messages. No, `DRW` is not short for `DRAW`, no matter how much your brain likes
to think that. It's an acronym for `Device Read/Write`, in other words generic
input/output type messages.

Each `DRW` packet contains an additional field, `chan`, which specifies which
"channel" data is destined for. Think of this like a port number for TCP or UDP,
except limited to the range `{0..15}`.

To facilitate sending data larger than a single network packet, a framing
protocol is used to chunk the file into smaller segments.

Or rather, because the people who made the Ankermake M5 seem to *love* making
things very complicated for no reason, there are 2 different framing protocols:
`AABB` and `XZYH`.

AABB frames
-----------

The structure of an AABB frame is as follows:

| Name    | Type   | Decription                                 |
|---------|--------|--------------------------------------------|
| magic   | u16    | 2 bytes (always 0xAA, 0xBB)                |
| unknown | u16    | 2 bytes                                    |
| command | u32    | 4 bytes                                    |
| length  | u32    | 4 bytes                                    |
| payload | byte[] | <length> bytes                             |
| crc16   | u16    | [CRC16/XMODEM checksum](crc16-checksum.md) |

Proof-of-concept code for parsing the header:

```python
# parse AABB header from first 12 bytes of given payload
def parse_aabb(p):
    d_magic, unk, cmd, len = struct.unpack("<2sHII", p[:12])
    assert d_magic == b'\xaa\xbb'
    return (d_magic, cmd, len, unk)
```

When sending an AABB frame, generate the entire transfer including header and
crc, then send it using `DRW` messages for each 1024 bytes or so.

This means the `AABB` header is only present in the first network packet for
this transfer, and the checksum is only present in the last.

XZYH frames
-----------

Another framing format is XZYH, also named after the magic bytes at the start.

Unlike AABB framing, XZYH has no checksum, but uses a larger header:

| Name    | Type   | Decription              |
|---------|--------|-------------------------|
| magic   | u32    | 4 bytes (always "XZYH") |
| command | u16    | 2 bytes                 |
| length  | u32    | 4 bytes                 |
| unknown | u8[6]  | 6 bytes                 |
| payload | byte[] | <length> bytes          |

Proof-of-concept code for parsing the header:

```python
# parse XZYH header from first 16 bytes of given payload
def parse_xzyh(p):
    d_magic, cmd, len, unk = struct.unpack("<4sHI6s", p[:16])
    assert d_magic == b'XZYH'
    return (d_magic, cmd, len, unk)
```

File transfer
-------------

Ankermake Slicer uses the "Machine ID", which is read from the registry key

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\MachineGuid
```

This is a unique, machine-specific, randomly-generated UUID.

For this example, suppose our Machine ID is
`07dfc78a-a5f0-49b9-b757-fd7564cab99f`.

Sending a file involves sending 3 AABB frames:

 1. `<first 16 bytes of the machine guid>`

 1. `<file transfer header>`

 1. `<file contents>` (the file contents, byte-for-byte)

Once this is done, printing should start automatically.

File transfer structure
-----------------------

The first message is simply the first 16 bytes of the machine GUID, in text form:

```
07dfc78a-a5f0-49
```

The reason for this is unclear, to say the least. Especially since the next
message contains the *complete* machine guid.

The second message is a comma-separated list of values relating to the print job, in this form:

```
<int>,<file-name>,<file-size>,<file-md5>,<printer-nick>,<eufy-account-id>,<host-machine-guid>
```

The function of the first `<int>` is not known at this point. Only 0 has been
observed, so it seems safe to always set this to 0 for now.

If we send `filename.zip`, which is 31337 bytes, and has an md5sum of
`2ab96390c7dbe3439de74d0c9b0b1767` to the printer named `PrintyMcPrintyFace`, we
would get this example:

```
0,filename.zip,31337,2ab96390c7dbe3439de74d0c9b0b1767,PrintyMcPrintyFace,0beec7b5ea3f0fdbc95d0dd47f3c5bc275da8a33,07dfc78a-a5f0-49b9-b757-fd7564cab99f
```

Here, `0beec7b5ea3f0fdbc95d0dd47f3c5bc275da8a33` would be our Eufy account id,
and `07dfc78a-a5f0-49b9-b757-fd7564cab99f` our complete machine guid.

The third transfer is the contents of `filename.zip`, again in `AABB` form.

If all has gone well, the printer will start extracting `filename.zip` and print your g-code.
