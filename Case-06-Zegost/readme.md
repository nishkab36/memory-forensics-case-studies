# Zegost Memory Analysis

## Overview

This case study analyzes a Zegost memory sample using Volatility. The objective was to identify malicious activity, investigate loaded modules within a running process, extract suspicious DLLs from memory, and verify the malware through threat intelligence analysis.

## Memory Profile Identification

**Plugin Used:** `imageinfo`

The memory image was identified as a Windows 7 Service Pack 0 system (Win7SP0x86), which was used throughout the investigation.

## Process Enumeration

**Plugin Used:** `pslist`

The list of active processes was examined to identify potential targets for further investigation.

Several instances of `svchost.exe` were present, which is normal on Windows systems because multiple services run under separate service host processes.

One instance, **svchost.exe (PID 880)**, was selected for further analysis.

## Process Command Line Analysis

**Plugin Used:** `cmdline`

The command line associated with **svchost.exe (PID 880)** was examined.

The process was running with the following parameters:

```text
C:\Windows\system32\svchost.exe -k netsvcs
```

The executable path appeared legitimate. However, additional investigation was required to determine whether malicious code had been loaded into the process.

## Loaded Module Analysis

**Plugin Used:** `dlllist`

The loaded modules associated with **svchost.exe (PID 880)** were examined.

Among the normal Windows DLLs, a suspicious module named:

```text
imageik.ddf
```

was identified.

The module was loaded from:

```text
C:\Users\Test\Application Data\ACD Systems\ACDSee\imageik.ddf
```

Unlike standard Windows DLLs that are normally loaded from system directories, this module was loaded from a user application data location, making it highly suspicious.

## DLL Extraction

**Plugin Used:** `dlldump`

The suspicious module was extracted directly from memory for further analysis.

The extracted file was saved successfully and prepared for hash generation and malware verification.

## Hash Generation

A hash of the extracted DLL was generated.

This hash was then used to search VirusTotal and determine whether the file was known to security vendors.

## Malware Verification

VirusTotal analysis showed that the extracted DLL was flagged by multiple security vendors.

The sample was identified as belonging to the **Zegost malware family**, confirming that the extracted module was malicious.

This validated the findings obtained during memory analysis.

## String Analysis

String analysis was performed on the extracted DLL.

Several suspicious indicators were identified, including references to external domains and URLs:

```text
www.163.com
www.baidu.com
http://
```

The presence of network-related strings suggests that the malware was capable of external communication.

## Network Activity Analysis

**Plugin Used:** `netscan`

Network analysis revealed activity associated with the investigated process.

A connection involving **svchost.exe (PID 880)** was observed communicating over port **8000**, indicating potential malware-related communication.

The presence of network activity further supported the findings obtained from the DLL analysis.

## Conclusion

The investigation identified a malicious DLL loaded inside **svchost.exe (PID 880)**.

Key findings included:

- Suspicious DLL loaded inside a legitimate Windows process.
- DLL loaded from a non-standard user application directory.
- Successful extraction of the DLL from memory.
- VirusTotal confirmation of Zegost malware.
- Network-related strings discovered within the DLL.
- Evidence of network communication associated with the compromised process.

Based on these findings, the memory sample demonstrated malicious activity involving a loaded malware DLL associated with the **Zegost malware family**.
