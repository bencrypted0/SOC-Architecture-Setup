### Recommended Commands (Run One by One)

**1. Command & Scripting Interpreter (T1059) – PowerShell & CMD execution**

```powershell
Invoke-AtomicTest T1059.001 -TestNumbers 1   # PowerShell execution
Invoke-AtomicTest T1059.003 -TestNumbers 1   # Windows Command Shell (cmd.exe)
```

**2. Ingress Tool Transfer (T1105) – File download (great for FIM + network events)**

```powershell
Invoke-AtomicTest T1105 -TestNumbers 1       # PowerShell download (very common)
```

**3. Persistence Techniques (T1547) – Registry Run keys + Startup folder (perfect with your realtime FIM)**

```powershell
Invoke-AtomicTest T1547.001 -TestNumbers 1   # Registry Run key
Invoke-AtomicTest T1547.001 -TestNumbers 2   # Registry RunOnce
Invoke-AtomicTest T1547.001 -TestNumbers 4   # Suspicious file in Startup folder
```

**4. Defense Evasion – Proxy Execution (T1218)**

```powershell
Invoke-AtomicTest T1218.010 -TestNumbers 1   # Regsvr32 (very noisy in Wazuh/Sysmon)
```

**5. Privilege Escalation – UAC Bypass (T1548)**

```powershell
Invoke-AtomicTest T1548.002 -TestNumbers 1   # Common UAC bypass
```

**6. Persistence – Scheduled Task (T1053)**

```powershell
Invoke-AtomicTest T1053.005 -TestNumbers 1   # Create scheduled task
```

**7. Discovery Techniques (good for command-line + process events)**

```powershell
Invoke-AtomicTest T1082 -TestNumbers 1       # System Information Discovery
Invoke-AtomicTest T1135 -TestNumbers 1       # Network Share Discovery
```

**8. Credential Access (T1003) – LSASS dumping (requires good Sysmon config)**

```powershell
Invoke-AtomicTest T1003.001 -TestNumbers 1   # Mimikatz-like (use carefully)
```

**9. File & Directory related (triggers your realtime FIM on user folders)**

```powershell
Invoke-AtomicTest T1083 -TestNumbers 1       # File and Directory Discovery
# Then manually create/modify sensitive.yml or drop files in Desktop/Downloads
```

### Bonus: Safe Testing Tips

- **Preview before running** (highly recommended):
    
    PowerShell
    
    ```
    Invoke-AtomicTest T1059.001 -ShowDetailsBrief
    Invoke-AtomicTest T1105 -ShowDetails
    ```
    
- **Cleanup** after each test (important!):
    
    PowerShell
    
    ```
    Invoke-AtomicTest T1105 -Cleanup
    Invoke-AtomicTest T1547.001 -Cleanup
    ```
    
- For **maximum visibility in Wazuh**, make sure you have added the **Sysmon event channel** to your Windows ossec.conf:
    
    XML
    
    ```
    <localfile>
      <location>Microsoft-Windows-Sysmon/Operational</location>
      <log_format>eventchannel</log_format>
    </localfile>
    ```
    

### Suggested Testing Order for Your Lab

1. Start with **T1105** (file download) → triggers network + file creation.
2. Then **T1547.001** (persistence) → triggers registry + FIM on Startup/Desktop.
3. Then **T1218.010** (Regsvr32) → classic defense evasion.
4. Finally **T1059** variants.

After running each, wait 30–60 seconds and check Wazuh dashboard under:

- **Security Events** (filter by agent name or recent time)
- Look for **rule.level** 7+ and MITRE fields populated