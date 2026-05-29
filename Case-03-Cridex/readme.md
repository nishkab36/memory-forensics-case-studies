# Cridex Memory Analysis

## Overview

This case study analyzes a Cridex memory sample using Volatility. The objective was to identify suspicious processes, detect evidence of code injection, and investigate potential command-and-control (C2) communication within the memory image.

## Memory Profile Identification

**Plugin Used:** `imageinfo`

The memory image was identified as a Windows XP Service Pack 2 system (WinXPSP2x86), which was used throughout the investigation.

## Process Enumeration

**Plugin Used:** `pslist`

The running processes were reviewed to identify activity occurring within the system at the time of memory acquisition.

Among the observed processes was **reader_sl.exe (PID 1640)**, running under the user's session. This process was selected for further investigation after additional analysis revealed indicators of injected code and suspicious activity associated with it.

## Process Hierarchy Analysis

**Plugin Used:** `pstree`

The process hierarchy was examined to understand the relationship between running processes.

The analysis showed that **reader_sl.exe (PID 1640)** was launched by **explorer.exe (PID 1484)**.

While this relationship alone does not indicate malicious activity, it provides useful context regarding the execution chain of the process and helps correlate findings from later stages of the investigation.

## Memory Injection Detection

**Plugin Used:** `malfind`

The `malfind` plugin was used to identify memory regions containing potentially injected code.

The analysis revealed memory pages with **PAGE_EXECUTE_READWRITE** permissions and an **MZ header** present within memory.

The MZ signature is commonly associated with Windows executable files and is frequently encountered when executable code has been injected into a running process.

The presence of executable and writable memory regions containing an MZ header is a strong indicator of code injection and suggests that malicious code was loaded directly into process memory.

## Hidden Module Analysis

**Plugin Used:** `ldrmodules`

The loaded modules associated with running processes were examined.

The analysis revealed a module that was present in memory (**InMem = True**) but absent from normal loader structures (**InLoad = False**, **InInit = False**).

This type of discrepancy is commonly investigated during memory forensic analysis because legitimate modules are normally visible across all loader lists.

The findings suggest that the module may have been manually loaded or hidden, which further supports the possibility of injected or malicious code within the system.

## Network Activity Analysis

**Plugin Used:** `connscan`

Network analysis revealed active connections associated with **explorer.exe (PID 1484)**.

The process was communicating with external IP addresses **41.168.5.140** and **125.19.103.198** over port **8080**.

Unexpected outbound connections to external systems can indicate communication with remote infrastructure used to control or manage malware infections.

These findings suggest possible command-and-control activity occurring on the compromised system.

## User and System Information

**Plugin Used:** `envars`

Environment variables were examined to gather additional context about the affected system.

The analysis revealed:

* Username: **Robert**
* Computer Name: **ACCOUNTING12**

This information helps identify the user account and host involved in the investigation and provides valuable context during incident response activities.

## Conclusion

The investigation identified multiple indicators of suspicious activity within the memory sample.

Key findings included:

* Evidence of executable code injection identified through `malfind`.
* Memory regions containing **PAGE_EXECUTE_READWRITE** permissions and an **MZ header**.
* Module loading inconsistencies observed through `ldrmodules`.
* External network communication over port **8080**.
* Identification of the affected user (**Robert**) and host (**ACCOUNTING12**).

Based on these findings, the memory sample demonstrated strong evidence of malware activity involving code injection and external network communication. The observed behavior included indicators commonly associated with user-mode rootkits and code injection techniques.
