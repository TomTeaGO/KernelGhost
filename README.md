# KernelGhost
Post Exploitation
Execution – T1059: Command and Script Interpreter

The Sysmon logs showed that the malware started extra files from the Windows 7 Temp folder and later ran commands on the Domain Controller. MITRE ATT&CK T1059 covers attackers using command-line tools or scripts to carry out actions on a compromised system.

Defence Evasion – T1055: Process Injection and T1070.004: File Deletion

The malware placed its code inside trusted Windows processes such as explorer.exe, rundll32.exe and spoolsv.exe. This matches T1055 because the attacker used normal Windows programs to hide malicious activity. On Ubuntu, .bash_history and data.zip were deleted, which matches T1070.004 because files were removed to hide evidence.

Discovery – T1087, T1018 and T1016

The attacker used whoami /groups to check account permissions, net view and ping to find other systems, and ifconfig to view Ubuntu network details. These actions match Account Discovery, Remote System Discovery and System Network Configuration Discovery because they helped the attacker understand the network and identify further targets.

Credential Access – T1003.003: NTDS

On the Domain Controller, the attacker used ntdsutil and esentutl to copy and access the ntds.dit database. This matches T1003.003 because ntds.dit contains domain account information and password hashes that may be used to recover passwords.

Collection – T1560.001: Archive Collected Data

On Ubuntu, files named Satellite-Feed-Data-2* were placed into data.zip. This matches T1560.001 because the attacker combined several files into one archive, making them easier to store or transfer.

Command and Control – T1071 and T1021.004

The Windows 7 workstation and Domain Controller contacted 8.3.172.84 through windows-llive.com. This matches T1071 because the malware used network communication to contact the attacker. The same IP later logged into Ubuntu through SSH using sat-admin, which matches T1021.004 because SSH was used for remote access.

Exfiltration

The evidence confirms that satellite files and Active Directory data were collected.

ISM-1501

The Windows 7 computer, Domain Controller and Ubuntu system should be rebuilt using clean and trusted copies. This is important because the attacker gained high-level access and may have left hidden files or settings behind. Rebuilding the systems is safer than only deleting the files that were found.

12. Highly Recommended — Reset Passwords

ISM-1173

The sat-admin password and all administrator and service-account passwords should be changed. This is needed because the attacker successfully logged into Ubuntu and also copied account information from the Domain Controller. Multi-factor authentication should also be used for administrator and remote access.

13. Recommended — Improve Monitoring

ISM-1405

Logs from Windows, Ubuntu, SSH and the firewall should be kept in one central location. Alerts should be created for unusual logins, new services, unknown USB devices and suspicious network connections. This will help the organisation detect similar activity earlier.
