Ankermake slicer
================

Stored credentials
------------------

Ankermake slicer stores your login information in an obfuscated format.

The file to look for is

```shell
%AppData%/Local/AnkerMake/AnkerMake_64bit_fp/login.json
```

Which, even though it is named `.json`, is a base64-encoded, encrypted json file.

This file can be decoded using a couple of unix commands. First, decode the base64:

```shell
$ base64 -d login.json > login.raw
```

Now `login.raw` will contain the raw (encrypted) contents. These can be decrypted using openssl:

```shell
$ openssl aes-128-ecb -d -nopad -in login.raw -K 1b55f97793d58864571e1055838cac97
```

The key `1b55f97793d58864571e1055838cac97` was extracted from Ankermake Slicer using Ghidra.

The `openssl` command above should yield output similar to this:

```json
{
    "code": 0,
    "data": {
        "ab_code": "REDACTED",
        "auth_token": "REDACTED",
        "avatar": "",
        "domain": "",
        "email": "REDACTED",
        "geo_key": "REDACTED",
        "invitation_code": "REDACTED",
        "inviter_code": "",
        "mac_addr": "",
        "nick_name": "",
        "params": null,
        "phone": "",
        "phone_code": "",
        "privilege": 0,
        "server_secret_info": {
            "public_key": "REDACTED"
        },
        "token_expires_at": "REDACTED",
        "trust_list": [
        ],
        "user_id": "REDACTED",
        "verify_code_url": ""
    },
    "msg": "Succeed."
}
```


Alternatively, the excellent CyberChef tool can be used.

Just click on [this recipe](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)AES_Decrypt(%7B'option':'Hex','string':'1b55f97793d58864571e1055838cac97'%7D,%7B'option':'Hex','string':''%7D,'ECB','Raw','Raw',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D)) and paste the contents of your `login.json` to see the contents.
