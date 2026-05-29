# Stuxnet Memory Analysis

## Overview

This case study analyzes a Stuxnet memory sample using Volatility. The objective was to identify suspicious processes and investigate their behavior through memory forensic analysis.

## Memory Profile Identification

**Plugin Used:** `imageinfo`

The `imageinfo` plugin was used to determine the operating system profile of the memory image.

The analysis identified the memory sample as a Windows XP Service Pack 2 system (WinXPSP2x86), which was used throughout the investigation.

## Process Enumeration

**Plugin Used:** `pslist`

The `pslist` plugin was used to view the running processes present in memory.

During the analysis, multiple instances of **lsass.exe** were observed. In a normal Windows XP system, only one legitimate LSASS process should be running. The additional instance with PID **1928** appeared suspicious and required further investigation.

## Process Hierarchy Analysis

**Plugin Used:** `pstree`

The `pstree` plugin was used to examine parent-child process relationships.

The legitimate LSASS process (PID 680) was spawned by **winlogon.exe**, which is the expected behavior in Windows systems.

However, a second instance of **lsass.exe (PID 1928)** was found under **services.exe**. Since LSASS is normally started by Winlogon during system initialization, this process hierarchy is unusual and indicates possible malicious activity.

## Process Handle Analysis

**Plugin Used:** `handles`

The `handles` plugin was used to inspect the resources accessed by the suspicious process.

The process maintained handles related to:

* Network communication (`\Device\Tcp`, `\Device\Ip`)
* Registry keys
* Multiple mutex objects

These artifacts show that the process was interacting with important system and network resources, which further increased the suspicion surrounding the process.

## Conclusion

The investigation identified **lsass.exe (PID 1928)** as the suspicious process within the memory sample.

This conclusion was based on:

* The presence of multiple LSASS processes.
* An unusual parent-child relationship.
* Access to network-related resources.
* Multiple mutex objects associated with the process.

Based on these findings, the process was determined to be associated with malicious activity within the Stuxnet memory sample.
