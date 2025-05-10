+++
author = "mukund"
title = "Writing a Burp Extension to Bypass Checksum"
date = "2024-12-20"
description = "Writing a Burp Extension to Bypass Checksum"
tags = [
    "general","burp","web",
]
+++

Wrote a burp extension that can calculate the hash value of the request and add it to the checksum header. Sometimes checksum is used to not let the proxy tools like Burp Suite to modify the request, as the checksum is calculated and checked in the backend.

[https://payatu.com/blog/stream-ciphers-cryptography-for-ctfs/](https://payatu.com/blog/stream-ciphers-cryptography-for-ctfs/)