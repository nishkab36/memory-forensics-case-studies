# Taidoor Memory Analysis

## Overview

This case study analyzes a Taidoor memory sample using Volatility. The objective was to investigate suspicious process activity, identify evidence of process hollowing, and examine potential network communication associated with the compromised process.

## Memory Profile Identification

**Plugin Used:** `imageinfo`

The memory image was identified as a Windows 7 Service Pack 0 system (Win7SP0x86), which was used throughout the investigation.

## Process Hierarchy Analysis

**Plugin Used:** `pstree`

The process hierarchy was examined to understand relationships between running processes.

During the analysis, an instance of **svchost.exe (PID 1412)** was identified. Unlike other service host processes visible in the process tree, this instance did not show a normal parent-child relationship.

The absence of a visible parent process suggested that additional investigation was required to determine whether the process had been manipulated or was no longer executing normally.

## Process Inspection

**Plugin Used:** `dllist`

The loaded modules associated with **svchost.exe (PID 1412)** were examined.

The process was running from the expected Windows system path:

```text
C:\Windows\System32\svchost.exe
```

However, further analysis was required because a legitimate process path alone does not guarantee that the process has not been compromised.

## Process Hollowing Detection

**Plugin Used:** `hollowfind`

The `hollowfind` plugin was used to detect signs of process hollowing.

The analysis reported a **Process Base Address and Memory Protection Discrepancy**, indicating differences between the process information stored in memory structures.

Additionally, the plugin identified suspicious executable memory regions associated with the process.

These findings are commonly associated with **process hollowing**, a technique in which malicious code replaces the contents of a legitimate process while retaining its original process identity.

## Memory Injection Analysis

**Plugin Used:** `malfind`

Further investigation using `malfind` revealed executable memory regions with **PAGE_EXECUTE_READWRITE** permissions.

The memory region also contained an **MZ header**, which is commonly found at the beginning of Windows executable files.

The presence of an executable PE header within writable process memory is a strong indicator that code was injected into the process.

These findings supported the results obtained from the `hollowfind` analysis.

## Network Activity Analysis

**Plugin Used:** `netscan`

Network analysis revealed a connection associated with **svchost.exe (PID 1412)**.

The process was observed communicating with the external IP address:

```text
200.2.126.61:443
```

Unexpected external communication originating from a process exhibiting signs of hollowing and code injection increases the likelihood that the process was being used for malicious activity.

## Conclusion

The investigation identified suspicious activity associated with **svchost.exe (PID 1412)**.

Key findings included:

- An unusual process hierarchy.
- Evidence of process hollowing identified through `hollowfind`.
- Executable memory regions containing an MZ header.
- Memory pages with **PAGE_EXECUTE_READWRITE** permissions.
- External network communication over port **443**.

Based on these findings, the memory sample demonstrated strong evidence of malware activity involving **process hollowing and code injection techniques**. The compromised process appeared to be communicating with an external system while masquerading as a legitimate Windows process.
