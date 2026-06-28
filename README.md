Initial Access

The first sign of compromise appears to be from the user downloading and running a fake game installer. I reviewed Chrome history, download artefacts, and UserAssist evidence. These showed that the user smithy had been searching for free games before the file Call-Of-Duty-Modern-Warfare.exe was downloaded into the Downloads folder.

The file path identified was:

C:\Users\smithy\Downloads\Call-Of-Duty-Modern-Warfare.exe

At 00:33:48 UTC on 07 Apr 2020, UserAssist showed that this file was executed by the smithy account. This is most likely where the malware first gained execution on the laptop. The file name looked like a normal Call of Duty installer, but the later service activity shows it was not legitimate. This maps to MITRE ATT&CK T1204.002 — User Execution: Malicious File. Evidence for this is shown in.

Privilege Escalation

Analysis of the SAM registry hive showed that the user account smithy was a member of the local Administrators group. This evidence was found in:
C:\Windows\System32\config\SAM
SAM\Domains\Account\Users
The user already had administrator rights, the malware most likely did not need to exploit the system to gain higher privileges. When the fake game installer was run by smithy, it likely had enough permission to create a Windows service.
The Windows System logs later showed the service cxlNatIRHFFcg / Call of Duty Online Checker was installed with the path:
"C:\Users\smithy\Downloads\Call of Duty Service.exe" abpVYmEe

The service was configured to run as LocalSystem, which is a highly privileged Windows account. My assessment is that the malware used the existing administrator rights of the smithy account to create the service, and then Windows Service Control Manager started it as LocalSystem. This refes to  MITRE mapping T1543.003 — Windows Service, because the malware used a Windows service to run with system-level privileges, but the main use of this technique in the report should remain under persistence.

Lateral Movement

Lateral movement was not confirmed. I reviewed the available Security logs, System logs, Volatility process output, and network-related artefacts, but I did not find clear evidence that the attacker moved from Joseph Smith’s laptop to another system.

There was no confirmed evidence of RDP logons, SMB movement, remote PowerShell, remote service creation, or another compromised host. Based on the evidence available, the compromise appears to be limited to Joseph Smith’s laptop. Further checking of firewall logs, proxy logs, and wider network monitoring would be needed to fully rule this out.

Persistence

Persistence was confirmed through a Windows service. The main service found was cxlNatIRHFFcg / Call of Duty Online Checker, which pointed to:

C:\Users\smithy\Downloads\Call of Duty Service.exe

At 00:46:39 UTC on 07 Apr 2020, the service was installed with this service file path:

"C:\Users\smithy\Downloads\Call of Duty Service.exe" abpVYmEe

The service was set to auto start and ran under LocalSystem. This explains the user’s complaint that the Call of Duty Service kept running after reboot. A normal service would usually run from C:\Program Files or C:\Windows\System32, not directly from the user’s Downloads folder. This maps to MITRE ATT&CK T1543.003 — Windows Service. The fake Call of Duty name also maps to T1036.004 — Masquerade Task or Service. Evidence is shown in Figure X.

Post Exploitation

After the malicious service was created, Call of Duty Service.exe continued running under services.exe, which is normal behaviour for a Windows service. Volatility evidence also showed repeated child process activity from Call of Duty Service.exe. This likely explains why Joseph Smith reported that the laptop was running slowly.

Execution

The main suspicious service found in the Windows System logs was:

Service Name: cxlNatIRHFFcg
Display Name: Call of Duty Online Checker
Service File Name: "C:\Users\smithy\Downloads\Call of Duty Service.exe" abpVYmEe
Start Type: auto start
Account: LocalSystem

This shows that the malware was actively running through Windows service execution, not just sitting as a file in the Downloads folder.

A second suspicious service named hmsfkw was also found. It attempted to run:

cmd.exe /c echo hmsfkw > \.\pipe\hmsfkw

This command tried to write the text hmsfkw into a Windows named pipe. The service failed after 30000 milliseconds, so I would not treat this as successful persistence. However, it is still suspicious because it used a random service name, cmd.exe, and named pipe activity around the same time as the confirmed Call of Duty service.

This activity maps to MITRE ATT&CK T1059.003 — Windows Command Shell and T1569.002 — Service Execution.

Defence Evasion

The malware used fake game-related names such as Call of Duty Service and Call of Duty Online Checker. This helped it look like a normal game service. The file running from C:\Users\smithy\Downloads is suspicious because legitimate services usually run from trusted locations like C:\Program Files or C:\Windows\System32. This maps to MITRE ATT&CK T1036.004 — Masquerade Task or Service.

Command and Control

Memory analysis found shellcode containing ws2_32, which is used for Windows networking, and a likely external destination of 1.6.12.12:11198. If this was confirmed in Volatility network output, it can be treated as command-and-control activity. If it was only found in shellcode, it should be written as a likely hardcoded C2 address. The closest MITRE mapping is T1095 — Non-Application Layer Protocol, unless later evidence confirms HTTP, DNS, or another application-layer protocol..
