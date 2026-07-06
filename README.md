On 18 Jun 20, adversary IP 5.112.3.2 targeted the public WordPress website. The adversary carried out automated scanning and repeated login attempts before gaining access to the WordPress administration area. Network evidence showed the admin account being used with the password Consumer10. After gaining access, the adversary modified a WordPress theme file to maintain remote access to the web server.

The compromise later affected internal systems. Malicious files were identified on both the Domain Controller and Windows 7 workstation. The Windows 7 malware was installed as an automatic service running with high privileges, while the Domain Controller showed suspicious script and command activity. Evidence also showed internal network logons between the compromised systems, including use of the KOALANET\Administrator account from the web server.

On 21 Jun 20, suspicious command activity on the Domain Controller was followed by the creation of LOCKED.rar and a ransom note named Your Files Have Been Encrypted. Shortly afterwards, the Windows Security audit log was cleared. The incident is therefore assessed as a financially motivated cybercriminal attack that began with compromise of the public website, spread into the internal environment and ended with ransomware-style impact. The available evidence does not support confident attribution to a specific named threat group or nation-state actor.

Initial Access
At 00:45:19Z on 18 Jun 20, the adversary at 5.112.3.2 began targeting KOALANET’s public WordPress website. After repeated login attempts, the adversary gained access to the website administration area and later changed a website file to maintain remote access to the web server.

Privilege Escalation
At 23:02:24Z on 21 Jun 20, the malicious file C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe was installed on the Windows 7 workstation as an automatic service running with high-level system privileges. The investigation confirmed privileged access, but did not identify the exact method used to gain those privileges.

Lateral Movement
At 22:55:17Z on 21 Jun 20, malicious software was confirmed running on the Windows 7 workstation 10.0.0.12 and communicating with the same adversary IP. The exact method used to place the malware on the workstation was not confirmed. Later, at 00:52:14Z on 22 Jun 20, the Domain Controller recorded a successful Administrator logon from the compromised web server 10.0.0.3, showing that the adversary had moved from the public-facing system into the internal network.
Persistence
At 01:36:14Z on 18 Jun 20, the adversary modified the WordPress page.php file so the web server would continue providing remote access. Later, at 23:02:24Z on 21 Jun 20, the Windows 7 malware was installed as an automatic service, allowing it to continue running after a restart.

Post Exploitation
At 04:10:22Z on 19 Jun 20, malicious activity was confirmed on the Domain Controller when a script launched a suspicious program. On 21 Jun 20 at 23:13:11Z, further command activity occurred. Shortly afterwards, LOCKED.rar was created at 23:14:26Z, followed by the ransom note Your Files Have Been Encrypted at 23:21:30Z. The Security audit log was then cleared at 23:23:19Z, showing ransomware-style impact followed by an apparent attempt to remove evidence.


Essential 1 – Reset Compromised Passwords and Enable MFA
ISM-1173
Reset the WordPress admin account, KOALANET\Administrator, and any account using Consumer10. Remove the cleartext password from the web server and enable MFA for privileged accounts.
Why useful: The same password was reused across the environment, which may have helped the adversary move from the website into internal systems.
Essential 2 – Rebuild Unsupported Compromised Systems
ISM-1501
Rebuild the compromised Windows 7 workstation and Windows Server 2012 Domain Controller from known-good sources, then move them to supported operating systems.
Why useful: Malware and ransomware activity was confirmed on these systems. Rebuilding removes hidden persistence and reduces risk from unsupported software.
Highly Recommended 1 – Rebuild and Secure the WordPress Server
ISM-1876
Rebuild the WordPress website from a clean copy, update WordPress, plugins and themes to supported versions, and remove anything unused.
Why useful: The adversary first gained access through WordPress and modified page.php to maintain remote access.
Highly Recommended 2 – Separate the Web Server from the Internal Network
ISM-1181
Place the public web server in a separate network zone and block unnecessary direct access to the Domain Controller, especially SMB port 445.
Why useful: This would make it much harder for an adversary who compromises the public website to reach internal systems.
Recommended – Maintain Protected and Tested Backups
ISM-1511, ISM-1811, ISM-1515
Keep protected backups of corporate files, Domain Controller data and website configurations. Regularly test that the backups can be restored.
Why useful: The incident caused ransomware-style impact. Clean backups would allow KOALANET to recover important files without relying on a ransom payment.
