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

I could have just used this, it's reliable & battle tested.

> - This approach requires us to know about the target process beforehand.
> - Packaged apps are subject to internal changes, thus not fully persistent.

## IPackageDebugSettings

[`IPackageDebugSettings::GetPackageExecutionState`](https://learn.microsoft.com/en-us/windows/win32/api/shobjidl_core/nf-shobjidl_core-ipackagedebugsettings-getpackageexecutionstate) can be used to query a package's execution state.

Usage of this API is straight forward, making this the simplest way to determine if a packaged app is running!
> This API returns the execution state of an entire package & not for an instance of it.

All is good with this API until you enable debug mode for a package using `IPackageDebugSettings::EnableDebugging`.
This API has a [chance of returning invalid information](https://stackoverflow.com/questions/75951964/how-to-determine-if-uwp-app-is-running-with-windows-open) about a package's execution state when debug mode is enabled.

## AppDiagnosticInfo

Back in 2017, Microsoft introduced ["UWP App Diagnostics"](https://blogs.windows.com/windowsdeveloper/2017/06/28/uwp-app-diagnostics/) into the Windows Runtime & one of the classes, they introduced was [`AppDiagnosticInfo`](https://learn.microsoft.com/mt-mt/uwp/api/windows.system.appdiagnosticinfo) which provides metadata about an app within a package.

`AppDiagnosticInfo` can be used to:

- Query all or specific apps running on a system.

- Query apps provided by a package family.

- Query metadata about an app belonging to a package family. 

We can easily enumerate instances of an app using:

```csharp
AppDiagnosticInfo.GetResourceGroups()
```

This returns a list of `AppResourceGroupInfo` objects which represent an instance of an app running on the system.

If we just want to determine if there is a running instance then:

```csharp
AppDiagnosticInfo.GetResourceGroups().Any()
```

Once again all is good until we have debug mode enabled for a package.

Now `AppDiagnosticInfo.GetResourceGroups()` has a chance of containing [invalid `AppResourceGroupInfo` objects.](https://stackoverflow.com/questions/68082731/startterminateasync-throws-type-e-elementnotfound)

So we can't simply use `AppDiagnosticInfo.GetResourceGroups().Any()` to determine if a running instance exists, thus we require some way to determine if an `AppResourceGroupInfo` object is valid.

An `AppResourceGroupInfo` object may deemed invalid if `AppResourceGroupInfo.GetProcessDiagnosticInfos()` contains nothing.

How can an app be running if there are no processes associated with it?

Thus, we can determine if there is a running instance using:

```csharp
AppDiagnosticInfo.GetResourceGroups().Any(_ => _.GetProcessDiagnosticInfos().Any())
```

## Conclusion

There are multiple ways to check if a packaged app is running:

- Process enumeration is straightforward. 

- `IPackageDebugSettings` is simple.

- `AppDiagnosticInfo` is the most reliable.

For the best balance of accuracy and ease, use:

```csharp
AppDiagnosticInfo.GetResourceGroups().Any(_ => _.GetProcessDiagnosticInfos().Any())
```

This method is straightforward, simple, and reliable.