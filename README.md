On 18 Jun 20, adversary IP 5.112.3.2 targeted the public WordPress website. The adversary carried out automated scanning and repeated login attempts before gaining access to the WordPress administration area. Network evidence showed the admin account being used with the password Consumer10. After gaining access, the adversary modified a WordPress theme file to maintain remote access to the web server.

The compromise later affected internal systems. Malicious files were identified on both the Domain Controller and Windows 7 workstation. The Windows 7 malware was installed as an automatic service running with high privileges, while the Domain Controller showed suspicious script and command activity. Evidence also showed internal network logons between the compromised systems, including use of the KOALANET\Administrator account from the web server.

On 21 Jun 20, suspicious command activity on the Domain Controller was followed by the creation of LOCKED.rar and a ransom note named Your Files Have Been Encrypted. Shortly afterwards, the Windows Security audit log was cleared. The incident is therefore assessed as a financially motivated cybercriminal attack that began with compromise of the public website, spread into the internal environment and ended with ransomware-style impact. The available evidence does not support confident attribution to a specific named threat group or nation-state actor.

Initial Access

At 00:45:19Z on 18 Jun 20, the adversary at 5.112.3.2 began targeting KOALANET’s public WordPress website. After many login attempts, the adversary gained access to the website administration area by 01:12:30Z using administrator credentials.

Privilege Escalation

At 23:02:24Z on 21 Jun 20, the malicious file C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe was installed on the Windows 7 workstation as an automatic service running with high-level system access. The investigation confirmed that higher privileges were being used, but the exact method used to gain them was not identified.

Lateral Movement

The compromise later spread into KOALANET’s internal network. At 23:53:51Z on 21 Jun 20, the Domain Controller recorded a successful network logon from Windows 7 workstation 10.0.0.12. Later, at 00:52:14Z on 22 Jun 20, another successful logon came from the compromised web server 10.0.0.3 using the KOALANET\Administrator account. The exact way the Windows 7 workstation was first compromised was not confirmed.

Persistence

At 01:36:14Z on 18 Jun 20, the adversary changed the WordPress page.php file so remote access to the web server could be maintained. Later, at 23:02:24Z on 21 Jun 20, the Windows 7 malware was installed as an automatic service, allowing it to keep running after a restart.

Post Exploitation

At 04:10:22Z on 19 Jun 20, malicious activity was confirmed on the Domain Controller when a script launched a suspicious program. On 21 Jun 20 at 23:13:11Z, further command activity followed. Soon after, LOCKED.rar was created at 23:14:26Z, followed by the ransom note Your Files Have Been Encrypted at 23:21:30Z. The Security audit log was then cleared at 23:23:19Z, showing ransomware-style damage followed by an apparent attempt to remove evidence.

Remediation Advice
Essential 1 – Isolate Affected Systems and Remove Malware

ISM-1501

Immediately isolate the compromised WordPress server, Windows 7 workstation and Domain Controller. Remove the confirmed malicious files, services and altered web files. Rebuild any system that cannot be trusted from a clean source.

Why useful: This stops the known malware from continuing to run and reduces the chance of hidden access remaining.

Essential 2 – Reset Passwords and Enable MFA

ISM-1173

Reset the WordPress admin account, KOALANET\Administrator account and any account that used Consumer10. Remove cleartext passwords and enable MFA for privileged accounts.

Why useful: The same password was reused across the environment. Changing it and adding MFA makes it much harder for the adversary to regain access.

Highly Recommended 1 – Rebuild and Secure the WordPress Website

ISM-1876

Restore the WordPress website from a clean copy. Remove the malicious code from page.php, update WordPress, plugins and themes, and remove anything no longer needed.

Why useful: The adversary first gained access through WordPress and later changed a website file to maintain remote access.

Highly Recommended 2 – Separate the Web Server from the Internal Network

ISM-1181

Place the public WordPress server in a separate network area and block unnecessary access from it to the Domain Controller and corporate share.

Why useful: This would make it much harder for an adversary who compromises the public website to reach important internal systems.

Recommended – Keep Protected and Tested Backups

ISM-1511, ISM-1811, ISM-1515

Keep secure backups of corporate files, Domain Controller data and website files. Regularly test that the backups can be restored.

Why useful: The incident caused ransomware-style damage. Clean backups would help KOALANET recover important files without relying on a ransom payment.
