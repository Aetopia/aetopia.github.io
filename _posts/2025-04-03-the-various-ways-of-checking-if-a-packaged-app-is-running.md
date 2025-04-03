---
layout: post
title: The various ways of checking if a packaged app is running.
---

I've been dealing with APIs that interact with packaged apps for a while. 
In one of my projects, I needed a way to check if a packaged app was running or not.
The answer to this might be simple but it isn't.

<!--more-->

The most straight forward way to verify if a process is running or not is to:

- Enumerate a list of running processes.

- Filter for the desired process.

- Verify if the target process is running.

I could have just used this, its reliable & battle tested.