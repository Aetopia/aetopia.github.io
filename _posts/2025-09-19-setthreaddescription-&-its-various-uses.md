---
layout: post
title: SetThreadDescription & its various uses.
---

The function's name indicates it's use, assigning a description to a thread.
This eases & helps with application debugging but you can use this function for so much more!

<!--more-->

According to the documentation, `SetThreadDescription` accepts a **unicode string**.<br>
Internally, the thread description is stored as [`UNICODE_STRING`](https://learn.microsoft.com/en-us/windows/win32/api/ntdef/ns-ntdef-_unicode_string) structure hence we are limited by how long the description can be.

## Simple Handsake Mechanism

Say, you need to inject a dynamic link library via an injector using a thread but **you want to ensure the dynamic link library has been injected by your injector**.

You could get away by:

- Checking for the existence of a kernel object.

- But this requires the creator of the object to be alive.

- This might undesirable, if you want to do a "fire & forget" approach.

`SetThreadDescription` can be used here as follows:

- The injector & dynamic link library both contain a unique string.

- The injector assigns the unique string as a description for the thread responsible for injecting.

- The dynamic link library checks if the description matches the unique string.

    - If the string matches then proceed with initializing.

    - If the string doesn't match then silently load.

This is basically a simple handshake mechanism between the injector & dynamic link library.

## Interprocess & Interthread Communication
`SetThreadDescription` can be used send payload to a target process or thread. 

- This is useful, if you want to send simple serialized payloads.
- The amount of data, you can send is limited by the max length of `UNICODE_STRING`.

## Conclusion
`SetThreadDescription` can be used creatively outside of debugging. The scenarios shown here, are a fraction of the potential of this function.