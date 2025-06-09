---
title: Powershell Basics
date: 2025-06-08 10:00:00 +0530
categories: [Powershell]
tags: [Windows, Underthewire]
image: /images/ps_powershell.png
---

> These are the wargames from [underthewire](https://underthewire.tech)

# Century

## Century 0-1
The password for century1 is `century1`. That we can find in the slack channel

## Century 1-2
Look for the build version of powershell 
![psversioninfo](/images/ps_psversion.png)

Hence password for `century2` is `10.0.14393.7870`

## Century 2-3
The equivalent of wget in Powershell is `Invoke-WebRequest`
And the file in desktop is `443`

![file](/images/ps_invokeweb.png)

Hence the password for `century3` would be `invoke-webrequest443`

## Century 3-4
We need to get the number of files in `Desktop`
![count](/images/ps_countoffiles.png)

The command - `(Get-ChildItem -File | Measure-Object).Count`
`Get-ChildItem` - List all the files and directories in a specific location
`-File` - This flag, lists only the files, excluding the directories
`Measure-Object` - It calculates count, sum etc.
`.Count` - Gets the count 

As all the objects in the `Desktop` directory are files, it can also be done as - 
```powershell
(dir | Measure-Object).count
```
Hence the password for `century4` is `123`

## Century 4-5
We just need to enter the directory name in quotes 
![cd](/images/ps_canyouopenme.png)

Hence the password for `century5` is `15768`

## Century 5-6
Following are the different ways of getting domain information - 

![domaininfo](/images/ps_domaininfo.png)

And the file on the desktop is - 

![3347file](/images/ps_3347file.png)

As the password is the shortname of the domain PLUS the filename, hence the password for `century6` will be`underthewire3347`

## Century 6-7
This time we need to find the number of directories, so we will just use the `-Directory` flag with `Get-ChildItem` command.<br>
Moreover as all the items in the `Desktop` directory are Folders, we can also proceed with `dir`

![getcount](/images/ps_getcountdir.png)

Hence the password for `century7` is `197`

## Century 7-8
We need to do a recursive search

![recurse](/images/ps_recursivesearch.png)

Hence, the password for `century8` is `7points`
