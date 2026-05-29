# Mader Kernel-Mode Rootkit Analysis

## Overview

This case study analyzes a Mader memory sample using Volatility. The objective was to identify malicious kernel-level activity, investigate suspicious driver behavior and determine whether the system contained evidence of a kernel-mode rootkit.

## Memory Profile Identification

**Plugin Used:** `imageinfo`

The memory image was identified as a Windows XP Service Pack 2 system (WinXPSP2x86), which was used throughout the investigation.

## Driver Enumeration

**Plugin Used:** `modules`

The loaded kernel modules were examined to identify drivers present in memory.

During the analysis, a driver named **core.sys** was identified. The driver was loaded from:

`\SystemRoot\system32\drivers\core.sys`

Because kernel drivers operate with high privileges and can directly interact with operating system components, the driver was selected for further investigation.

## SSDT Analysis

**Plugin Used:** `ssdt`

The System Service Descriptor Table (SSDT) was examined to identify potential system call modifications.

The SSDT contains pointers to core Windows system functions. Malware operating in kernel mode often modifies SSDT entries to intercept system calls and manipulate operating system behavior.

The analysis confirmed the presence of SSDT entries within the memory image, allowing further inspection of ownership information.

## SSDT Hook Detection

**Plugin Used:** `ssdt`

Further analysis revealed that multiple SSDT entries were owned by **core.sys**.

Examples included:

- NtClose
- NtCreateKey
- NtDeleteKey
- NtDeleteValueKey
- NtLoadKey
- NtOpenKey
- NtRestoreKey
- NtSetValueKey

These functions are responsible for handling file and registry operations within Windows.

The ownership of numerous SSDT entries by a third-party driver is a strong indicator of SSDT hooking, a technique commonly used by kernel-mode rootkits to intercept and manipulate operating system activity.

## Driver Extraction

**Plugin Used:** `moddump`

The suspicious driver **core.sys** was extracted from memory for further analysis.

The extraction was successful and produced a copy of the driver file, enabling additional examination outside of the memory image.

## Hash Generation

A SHA-256 hash was generated for the extracted driver.

Hashes provide a unique fingerprint of a file and are commonly used to compare samples against threat intelligence sources and malware databases.

The generated hash was subsequently searched on VirusTotal.

## Malware Verification

**Tool Used:** VirusTotal

The generated hash was searched on VirusTotal.

The driver was flagged as malicious by multiple security vendors and was associated with rootkit-related detections. Several vendors specifically identified the sample as belonging to the **Mader** malware family.

This independent verification confirmed that the extracted driver was malicious.

## Conclusion

The investigation identified **core.sys** as a malicious kernel driver within the memory sample.

Key findings included:

- Presence of the suspicious driver **core.sys**.
- Multiple SSDT entries owned by the driver.
- Evidence of SSDT hooking affecting system and registry functions.
- Successful extraction of the driver from memory.
- Malware detection by multiple security vendors through VirusTotal.

Based on these findings, the memory sample demonstrated strong evidence of **kernel-mode rootkit activity**. The observed behavior is consistent with techniques commonly used by rootkits to intercept operating system functions and conceal malicious activity.
