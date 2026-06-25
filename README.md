The affected system was a personal laptop owned by Australian contractor Joseph Smith while on overseas deployment. The laptop was brought to the IT help desk because it was running slowly, and the user noticed an unusual Call of Duty Service that continued running after reboot.
Forensic analysis indicates the infection likely started when the user searched for free video games and downloaded a suspicious file named Call-Of-Duty-Modern-Warfare.exe. Evidence shows the file was saved in the user’s Downloads folder and later executed by the user.

After execution, the malware created a Windows service linked to Call of Duty Service.exe. The service was configured to start automatically, explaining why it kept running after reboot. Memory evidence also showed the process repeatedly spawning child processes, likely causing the system performance issues.

The threat actor is assessed as a likely Tier 5–6 opportunistic cybercriminal or commodity malware actor. The activity does not currently support attribution to a named MITRE ATT&CK APT group. The behaviour is more consistent with low-sophistication malware using a fake game download, misleading service name, and basic Windows service persistence.

Essential — Rebuild the laptop, do not just delete the file.

The laptop should be rebuilt from a trusted image because the malware installed itself as a Windows service and ran with high privileges. Only clean user files should be restored from backup. This removes hidden changes and gives confidence the device is safe again. ISM-1511 / ISM-1515

Essential — Block the confirmed malware indicators immediately.
Block Call-Of-Duty-Modern-Warfare.exe, Call of Duty Service.exe, the service name cxlNatIRHFFcg, and confirmed network traffic to 1.6.12.12:11198. This stops the same malware from running again or calling back out after recovery. ISM-1416 / ISM-1341

Highly Recommended — Update the rebuild to supported versions before returning it to the user.
Rebuild onto Windows 11 version 26H1 where supported, update Chrome to 149.0.7827.102/.103 or later, and update Microsoft Defender to security intelligence 1.453.274.0 or later. This directly reduces risk from unsafe websites, fake downloads, and known malware. ISM-1699 / ISM-1700

Highly Recommended — Stop users running unknown files from Downloads.
Apply application control so .exe files cannot run from Downloads, Temp, browser cache, or user profile folders unless approved. This would have helped stop the fake Call of Duty installer from executing in the first place. ISM-0843 / ISM-1870

Recommended — Remove normal local admin rights and alert on new services.
The user should not have normal local administrator access unless required. Alerts should be created for new Windows services, especially Event ID 7045, and services running from user folders. This helps stop malware from installing persistence and gives IT early warning if it happens again. ISM-1508 / ISM-0445 / ISM-1685 / ISM-1889

Initial Access Analysis of Chrome browsing history, download artefacts, and UserAssist registry evidence found that the adversary gained initial access to Joseph Smith’s laptop on 07 Apr 2020 after the user downloaded and executed a fake game installer named Call-Of-Duty-Modern-Warfare.exe, located at C:\Users\smithy\Downloads\Call-Of-Duty-Modern-Warfare.exe. This refers to MITRE ATT&CK T1204.002 — User Execution: Malicious File, where an adversary relies on a user opening a malicious file to gain execution. At 00:33:48 UTC, the user executed Call-Of-Duty-Modern-Warfare.exe, which likely gave the adversary initial access to the laptop. Additional Information a. Chrome history showed the user searched for free games and visited several gaming websites before the suspicious download occurred. b. Download evidence showed the suspicious file path as C:\Users\smithy\Downloads\Call-Of-Duty-Modern-Warfare.exe. c. UserAssist evidence confirmed the file was executed by the user account smithy

Persistence

The evidence shows that the malware created persistence by installing itself as a Windows service. Windows System logs, registry artefacts, and Volatility output all pointed to a suspicious service linked to Call of Duty Service.exe, located at C:\Users\smithy\Downloads\Call of Duty Service.exe. At 00:46:39 UTC, the service cxlNatIRHFFcg / Call of Duty Online Checker was created and set to start automatically.

This is important because the service was not running from a normal program location. It was running from the user’s Downloads folder, which is suspicious for a Windows service. The registry evidence showed the service ImagePath pointing to Call of Duty Service.exe, and the service had Start = 2, meaning it would automatically run when Windows started. Volatility also showed Call of Duty Service.exe running under services.exe, which matches normal Windows service behaviour.

This explains why the user kept seeing the Call of Duty Service after reboot. The malware was designed to restart each time the laptop turned on. This maps to MITRE ATT&CK T1543.003 — Windows Service, because the adversary used a service for persistence. It also maps to T1036.004 — Masquerade Task or Service, because the fake Call of Duty name made the service look like a normal game-related background process.

After the malicious service was created, the malware continued running on the laptop as Call of Duty Service.exe. Volatility process evidence showed Call of Duty Service.exe running under services.exe, which means it was operating like a Windows service. The process also repeatedly spawned child processes, which likely explains why Joseph Smith reported that the laptop was running very slowly.

Memory analysis also identified shellcode-style data linked to network activity. The shellcode contained references to ws2_32, which is used by Windows for network communication, and it showed a likely external destination of 1.6.12.12:11198. This indicates the malware may have had command-and-control or reverse connection capability after it was running on the system.

This activity is important because it shows the malware did more than just install itself. It stayed active, created repeated process activity, and had signs of network communication. This maps to MITRE ATT&CK T1055 — Process Injection if the shellcode came from injected memory, and T1071 — Application Layer Protocol if the network traffic to 1.6.12.12:11198 is confirmed by netscan or network log
