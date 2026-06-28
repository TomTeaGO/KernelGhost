Privilege Escalation

Privilege escalation was not confirmed from the evidence reviewed. Windows System logs showed that the suspicious service cxlNatIRHFFcg / Call of Duty Online Checker was installed and configured to run under the LocalSystem account, which means the malware achieved high-privilege service execution.

However, the evidence does not show how the malware gained permission to create a LocalSystem service. No confirmed UAC bypass, exploit, token abuse, credential theft, or privilege escalation method was identified. It is possible the user already had local administrator rights on the personal laptop or approved the fake installer when it was executed.

For this reason, this activity should be described as privileged execution through Windows service creation, not confirmed privilege escalation.

Lateral Movement

Lateral movement was not confirmed from the available evidence. Review of the uploaded Security logs, System logs, Volatility process evidence, and available artefacts did not identify confirmed movement from Joseph Smith’s laptop to another system.

No clear evidence was found for RDP logons, SMB movement, remote service creation on another host, remote PowerShell, or another affected system. Based on the evidence reviewed, the compromise appears limited to Joseph Smith’s laptop.

Persistence

The evidence shows that the malware created persistence by installing itself as a Windows service. Windows System logs, registry artefacts, and Volatility output all pointed to a suspicious service linked to Call of Duty Service.exe, located at C:\Users\smithy\Downloads\Call of Duty Service.exe.

At 00:46:39 UTC on 07 Apr 2020, the service cxlNatIRHFFcg / Call of Duty Online Checker was created with the file path "C:\Users\smithy\Downloads\Call of Duty Service.exe" abpVYmEe. The service was configured as auto start and ran under the LocalSystem account. This explains why the user kept seeing the Call of Duty Service running after reboot.

This maps to MITRE ATT&CK T1543.003 — Windows Service, because the malware used a Windows service to continue running after restart. It also maps to MITRE ATT&CK T1036.004 — Masquerade Task or Service, because the fake Call of Duty name made the service look like a normal game-related background service.

Post Exploitation

After the malware gained execution, it continued running through the suspicious Call of Duty Service.exe process. The main evidence came from Windows System logs, Volatility process output, command-line artefacts, and memory/shellcode evidence. The key file path linked to this activity was:

C:\Users\smithy\Downloads\Call of Duty Service.exe

Execution

Windows System logs showed that a service named cxlNatIRHFFcg / Call of Duty Online Checker was installed with the service file path:

"C:\Users\smithy\Downloads\Call of Duty Service.exe" abpVYmEe

The service was configured to run as LocalSystem and was set to auto start. Volatility process evidence also showed Call of Duty Service.exe running under services.exe, which is expected behaviour for a Windows service. This means the malware was not just sitting on the disk; it was actively running as a service on the laptop.

A second suspicious service named hmsfkw was also found in the System logs. This service attempted to run:

cmd.exe /c echo hmsfkw > \.\pipe\hmsfkw

This command tried to write the text hmsfkw into a Windows named pipe. The service later failed after 30000 milliseconds, so it should not be treated as successful persistence. However, it is still suspicious because it used a random service name, cmd.exe, and named pipe activity shortly before the main Call of Duty Service.exe service was created. This activity maps to MITRE ATT&CK T1059.003 — Windows Command Shell and T1569.002 — Service Execution.

Defence Evasion

The malware used simple masquerading to avoid suspicion. It used names such as Call of Duty Service and Call of Duty Online Checker, which made the service look like it belonged to a normal game or game update process.

This was suspicious because the service was running from the user’s Downloads folder:

C:\Users\smithy\Downloads\Call of Duty Service.exe

A legitimate Windows service would normally run from a trusted program location such as C:\Program Files or C:\Windows\System32, not directly from a user Downloads folder. This maps to MITRE ATT&CK T1036.004 — Masquerade Task or Service, because the malware used a believable service name to make the activity look normal.

Command and Control

Memory evidence showed shellcode-style data containing ws2_32, which is linked to Windows networking functions. The shellcode also contained a likely external destination:

1.6.12.12:11198

This suggests the malware may have had command-and-control or reverse connection capability. If 1.6.12.12:11198 is confirmed in Volatility netscan, netstat, firewall logs, proxy logs, or packet capture, then it can be reported as confirmed network communication. If it was only found inside shellcode, it should be written as a likely hardcoded C2 destination, not confirmed active traffic.
