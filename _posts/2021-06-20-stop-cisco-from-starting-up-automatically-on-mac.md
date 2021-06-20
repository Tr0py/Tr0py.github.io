---
title: 'Stop Cisco from Starting Up Automatically on Mac'
date: 2021-06-20
tags:
  - notes
  - English
---

**Problem:** After installing Cisco VPN, it always automatically startup when I login. It’s annoying.

[TOC]

## TL;DR

**Disable Cisco from autostartup from terminal** 

```shell
launchctl unload -w /Library/LaunchAgents/com.cisco.anyconnect.gui.plist
```



---



## Comparing different solutions

### 1. Official Solution by Apple *which doesn’t work*

`System Preferences > Users & Groups > Login Items`

Can’t find cisco there.

**Reference:** [Set your Mac to automatically log in during startup](https://support.apple.com/en-us/HT201476)

### 2. Brute force (don’t recommend)

Removing `/Library/LaunchDaemons/com.cisco.anyconnect.gui.plist`

**Reference:** [This answer](https://apple.stackexchange.com/a/269517/422169)

### 3. Best way: Use `launchctl`

**Load/unload a auto startup item**

```shell
 launchctl load -w /Library/LaunchAgents/com.cisco.anyconnect.gui.plist # enable
 launchctl unload -w /Library/LaunchAgents/com.cisco.anyconnect.gui.plist # disable
```

**Check if it auto starts**

```
launchctl list | grep -i cisco
```

**Reference:** [This answer](https://apple.stackexchange.com/a/394292/422169) (recommend to read)



## Explanation

1. The Cisco application did not register in the Mac login startup items but in `/Library` as a service. So we can’t find it from Apple’s control panel user interface.

2. The `Library/LaunchDaemons/` folders are sililar as `init.d` on Linux, and `launchctl` is like `systemd` on Linux. Always use interface and don’t mess up with the actual files.

