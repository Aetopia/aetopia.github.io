---
layout: post
title: The various ways of checking if a packaged app is running.
---

I've been dealing with APIs that interact with packaged apps for a while. 
In one of my projects, I needed a way to check if a packaged app was running or not.
The answer to this might be simple but it isn't.

<!--more-->

The most straight forward way to verify if a process is running:

- Enumerate a list of running processes.

- Filter for the desired process.

- Verify if the target process is running.

I could have just used this, its reliable & battle tested.

> - This approach requires us to know about the target process beforehand.
> - Packaged apps are subject to internal changes, thus not fully persistent.

## IPackageDebugSettings::GetPackageExecutionState()

`IPackageDebugSettings::GetPackageExecutionState` can be to query a package's execution state.

It's usage is also straight forward:

```cpp
IPackageDebugSettings::GetPackageExecutionState([In] <Package Full Name>, [Out] <Package Execution State>)
```

This is actually the simplest way to determine if a packaged app is running but its cracks start to show when you put a package into debug mode using `IPackageDebugSettings::EnableDebugging` without a debugger specified.

