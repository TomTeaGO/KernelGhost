On 18 Jun 20, adversary IP 5.112.3.2 targeted the public WordPress website. The adversary carried out automated scanning and repeated login attempts before gaining access to the WordPress administration area. Network evidence showed the admin account being used with the password Consumer10. After gaining access, the adversary modified a WordPress theme file to maintain remote access to the web server.

The compromise later affected internal systems. Malicious files were identified on both the Domain Controller and Windows 7 workstation. The Windows 7 malware was installed as an automatic service running with high privileges, while the Domain Controller showed suspicious script and command activity. Evidence also showed internal network logons between the compromised systems, including use of the KOALANET\Administrator account from the web server.

On 21 Jun 20, suspicious command activity on the Domain Controller was followed by the creation of LOCKED.rar and a ransom note named Your Files Have Been Encrypted. Shortly afterwards, the Windows Security audit log was cleared. The incident is therefore assessed as a financially motivated cybercriminal attack that began with compromise of the public website, spread into the internal environment and ended with ransomware-style impact. The available evidence does not support confident attribution to a specific named threat group or nation-state actor.


5. Initial Access
At 00:45:19Z on 18 Jun 20, the adversary at 5.112.3.2 began targeting KOALANET’s public WordPress website. After repeated login attempts, the adversary gained access to the website administration area and later changed a website file to maintain remote access to the web server.
6. Privilege Escalation
At 23:02:24Z on 21 Jun 20, the malicious file C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe was installed on the Windows 7 workstation as an automatic service running with high-level system privileges. The investigation confirmed privileged access, but did not identify the exact method used to gain those privileges.
7. Lateral Movement
At 22:55:17Z on 21 Jun 20, malicious software was confirmed running on the Windows 7 workstation 10.0.0.12 and communicating with the same adversary IP. The exact method used to place the malware on the workstation was not confirmed. Later, at 00:52:14Z on 22 Jun 20, the Domain Controller recorded a successful Administrator logon from the compromised web server 10.0.0.3, showing that the adversary had moved from the public-facing system into the internal network.
8. Persistence
At 01:36:14Z on 18 Jun 20, the adversary modified the WordPress page.php file so the web server would continue providing remote access. Later, at 23:02:24Z on 21 Jun 20, the Windows 7 malware was installed as an automatic service, allowing it to continue running after a restart.
9. Post Exploitation
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




TIMELINE OF EVENTS
DTG (Z)	Target Host	Description
UNK – pre-incident	Web Server 10.0.0.3	Credential exposure. Cleartext internal Administrator credentials were stored on the web server for access to //10.0.0.2/CorporateShare. The password matched the WordPress credential later observed in attacker traffic.
Source: .bash_history(1) / mount configuration
Path: //10.0.0.2/CorporateShare → /mnt/CorporateFiles/
00:45:19 18 Jun 20	Web Server 10.0.0.3	Reconnaissance. Adversary IP 5.112.3.2 first accessed KOALANET’s public WordPress website.
Source: access.log.4
Path: /var/log/apache2/access.log.4
00:48:04 18 Jun 20	Web Server 10.0.0.3	Reconnaissance. The adversary began automated WordPress scanning using WPScan v3.8.1, followed by user enumeration activity.
Source: access.log.4
Path: /var/log/apache2/access.log.4
00:49:52 18 Jun 20	Web Server 10.0.0.3	Initial access attempt. Repeated authentication attempts began against the WordPress login endpoint /wp-login.php.
Source: access.log.4
Path: /var/log/apache2/access.log.4
00:59:21 18 Jun 20	Web Server 10.0.0.3	Initial access likely achieved. A POST /wp-login.php request returned HTTP 302, providing the first strong indication of successful authentication.
Source: access.log.4
Path: /var/log/apache2/access.log.4
01:12:30 18 Jun 20	Web Server 10.0.0.3	Initial access confirmed. The adversary successfully accessed the restricted WordPress administration area /wp-admin/. Separate packet evidence showed attacker traffic submitting admin / Consumer10 credentials.
Source: access.log.4, Wireshark
Location: /var/log/apache2/access.log.4; Wireshark frame 726938
01:19:51 18 Jun 20	Web Server 10.0.0.3	Post exploitation / staging. A file was uploaded through the WordPress asynchronous upload handler.
Source: access.log.4
Path: POST /wp-admin/async-upload.php
01:24:36 18 Jun 20	Web Server 10.0.0.3	Persistence preparation. The adversary accessed the WordPress theme editor after gaining administrator access.
Source: access.log.4
Path: /wp-admin/theme-editor.php
01:36:14 18 Jun 20	Web Server 10.0.0.3	Persistence. The adversary modified page.php with obfuscated PHP code that provided continued remote access.
Source: access.log.4, 11.txt
Path: /var/www/html/wp-content/themes/twentyfourteen/page.php
01:36:57 18 Jun 20	Web Server 10.0.0.3	Command and control. The compromised web server initiated a reverse TCP connection to adversary IP 5.112.3.2 on port 5001.
Connection: 10.0.0.3:60268 → 5.112.3.2:5001
23:13:24 18 Jun 20	Web Server 10.0.0.3	Command and control. A second long-running TCP 5001 session was established with the same adversary IP.

Connection: 10.0.0.3:60802 → 5.112.3.2:5001
02:42:08 19 Jun 20	Domain Controller 10.0.0.2	Suspicious script YFzTPkztw.vbs was created in the Windows Temp directory.
Path: C:\Windows\Temp\YFzTPkztw.vbs
04:10:22 19 Jun 20	Domain Controller 10.0.0.2	Execution. wscript.exe executed YFzTPkztw.vbs, which launched suspicious executable fbjWOkz.exe from the Administrator Temp directory.
Path: C:\Users\Administrator\AppData\Local\Temp\rad872D2.tmp\fbjWOkz.exe
22:55:15 21 Jun 20	Windows 7 10.0.0.12	Discovery. whoami.exe executed, indicating the current user or security context was being checked.
Source: Windows 7 Volatility process output
Path: C:\Windows\System32\whoami.exe
22:55:16–22:55:17 21 Jun 20	Windows 7 10.0.0.12	execution. Malicious ASjScLYWro.exe was created and executed from Abbey Soto’s Temp directory.
Path: C:\Users\abbey.soto\AppData\Local\Temp\ASjScLYWro.exe
22:56:02 21 Jun 20	Windows 7 10.0.0.12	Persistence attempt. Suspicious service sunmeg was installed using cmd.exe and a named pipe under LocalSystem. The service later timed out.
Location: Event ID 7045; cmd.exe /c echo sunmeg > \\.\pipe\sunmeg
23:02:24 21 Jun 20	Windows 7 10.0.0.12	Post exploitation / execution. ASjScLYWro.exe spawned cmd.exe, which launched malicious PtKZjedI.exe.
Path: C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe
23:02:24 21 Jun 20	Windows 7 10.0.0.12	Persistence / privileged execution. PtKZjedI.exe was installed as the auto-start service Routine updater for Windows applications and configured to run as LocalSystem.
Location: Event IDs 7045, 7036
23:13:11 21 Jun 20	Domain Controller 10.0.0.2	Post exploitation. fbjWOkz.exe spawned cmd.exe, which started conhost.exe, confirming command activity on the Domain Controller.
Process chain: fbjWOkz.exe → cmd.exe → conhost.exe
23:14:26 21 Jun 20	Domain Controller 10.0.0.2	Impact / staging. Large archive LOCKED.rar was created shortly after the suspicious command activity.
Path: C:\LOCKED.rar
23:21:30 21 Jun 20	Domain Controller 10.0.0.2	Ransom note Your Files Have Been Encrypted was created, stating that corporate files had been encrypted and demanding payment.
Path: C:\Your Files Have Been Encrypted
23:23:19 21 Jun 20	Domain Controller 10.0.0.2	Defence evasion. The Windows Security audit log was cleared shortly after the ransomware-style impact.
Location: Security Event ID 1102
23:53:51 21 Jun 20	Domain Controller 10.0.0.2	Lateral movement / internal access. A successful network logon to the Domain Controller originated from Windows 7 workstation 10.0.0.12 using KOALANET\abbey.soto.
Location: Event ID 4624, Logon Type 3
00:52:14 22 Jun 20	Domain Controller 10.0.0.2	Lateral movement / valid account use. A successful network logon originated from compromised web server 10.0.0.3 using KOALANET\Administrator over NTLM V2.
Location: Event ID 4624, Logon Type 3, source 10.0.0.3:41026
00:52:14 22 Jun 20	Domain Controller 10.0.0.2	Privileged access. The Administrator network session received special privileges.
Location: Event ID 4672, account KOALANET\Administrator
UNK 21/22 Jun 20	Windows 7 10.0.0.12	Command and control. Netscan shows malicious ASjScLYWro.exe communicated with adversary IP 5.112.3.2 on TCP port 16001.
Connection: 10.0.0.12:58748 → 5.112.3.2:16001
UNK 21/22 Jun 20	Windows 7 10.0.0.12	Command and control. Malicious PtKZjedI.exe communicated with adversary IP 5.112.3.2 on TCP port 16002.
Connection: 10.0.0.12:58749 → 5.112.3.2:16002
UNK 21/22 Jun 20	Windows 7 10.0.0.12	Internal network access. The workstation had an established SMB connection to the Domain Controller. This confirms connectivity but does not prove the malware delivery route.
Connection: 10.0.0.12:57316 → 10.0.0.2:445


5. Initial Access
3. On 18 Jun 20, adversary IP 5.112.3.2 targeted KOALANET’s public WordPress website hosted on web server 10.0.0.3. At 00:48:04Z, the adversary began automated WordPress reconnaissance using WPScan v3.8.1, followed by activity consistent with user enumeration. At 00:49:52Z, repeated authentication attempts began against /wp-login.php. At 00:59:21Z, a POST /wp-login.php request returned HTTP 302, indicating a likely successful authentication. By 01:12:30Z, the adversary had successfully accessed /wp-admin/, confirming unauthorised access to the WordPress administration area. Separate packet evidence also showed traffic from 5.112.3.2 to 10.0.0.3 submitting the credentials admin and Consumer10. This activity mapped to MITRE ATT&CK T1110 – Brute Force and T1078 – Valid Accounts.
4. The initial access event was the compromise of the WordPress administrator account on 10.0.0.3. The first likely successful authentication occurred at 00:59:21Z, with confirmed access to the restricted /wp-admin/ area by 01:12:30Z. This was the first confirmed unauthorised entry into the KOALANET environment. Later compromise of the Domain Controller and Windows 7 workstation is covered separately under Lateral Movement and Post Exploitation.

5.  Evidence analysed
Finding: Repeated WordPress authentication attempts were followed by successful administrator access.
/var/log/apache2/access.log.4
Search for:
5.112.3.2
POST /wp-login.php
/wp-admin/
WPScan v3.8.1
The sequence shows reconnaissance, repeated login attempts, a likely successful login and later confirmed access to the restricted WordPress administration area.
________________________________________
5b. Malicious artefact found
Network packet analysis showed the adversary submitting:
log=admin
pwd=Consumer10
Wireshark frame 726938
Traffic direction:
5.112.3.2 → 10.0.0.3
Useful filter:
http.request.method == "POST" &&
http.request.uri contains "wp-login.php"
The adversary used WordPress administrator credentials during the attack. Combined with the later /wp-admin/ access, this supports successful use of a valid account.
________________________________________
5c. Why the finding was significant
Finding: After gaining WordPress administrator access, the adversary later accessed the theme editor and modified:
/var/www/html/wp-content/themes/twentyfourteen/page.php
Where to look:
/var/log/apache2/access.log.4
Search for:
/wp-admin/theme-editor.php
page.php
The initial compromise gave the adversary enough control to alter server-side WordPress code. This later enabled continued remote access to the web server and is covered in more detail under Persistence.

Privilege Escalation
7. The investigation did not identify the exact method used by the adversary to gain higher privileges after the initial WordPress compromise. However, later evidence showed that the adversary was operating with high-level access on internal systems. On 21 Jun 20 at 23:02:24Z, the malicious file PtKZjedI.exe was installed on Windows 7 workstation 10.0.0.12 as an automatic Windows service running under LocalSystem. On 22 Jun 20 at 00:52:14Z, the Domain Controller 10.0.0.2 also recorded a successful network logon from the compromised web server 10.0.0.3 using the KOALANET\Administrator account. This confirmed that privileged access had been achieved, although the exact escalation technique could not be proven from the available evidence.
This activity was associated with MITRE ATT&CK T1543.003 – Windows Service and T1078 – Valid Accounts.
8. The strongest evidence was found on the Windows 7 workstation. At 23:02:24Z on 21 Jun 20, PtKZjedI.exe was installed as the service Routine updater for Windows applications, set to start automatically and run as LocalSystem. The Domain Controller also showed privileged account use when KOALANET\Administrator logged in from the compromised web server. This shows the adversary reached high-level access on more than one system, but the investigation could not confirm exactly how those privileges were first obtained.
9. Evidence analysed
Finding: PtKZjedI.exe was installed as an automatic Windows service running as LocalSystem.
Where to look:
Source file: system77.txt
Search for:
Event ID 7045
Routine updater for Windows applications
PtKZjedI.exe
LocalSystem
File path:
C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe
What it means: The malware was running with SYSTEM-level privileges, giving it extensive control over the workstation. The event proves privileged execution, but not the exact method used to gain those privileges.
10. Supporting evidence
Finding: The malicious service later entered the running state.
Where to look:
Source file: system77.txt
Search for:
Event ID 7036
Routine updater for Windows applications
What it means: This supports that the malicious service was successfully started and continued running with LocalSystem privileges.
10a. Privileged access on the Domain Controller
Finding: A successful Administrator network logon came from the compromised web server.
Where to look:
Source files:
security.csv
test.txt
Search for:
Event ID 4624
Logon Type 3
KOALANET\Administrator
10.0.0.3
Relevant event:
00:52:14Z 22 Jun 20
Source: 10.0.0.3
Destination: 10.0.0.2
Account: KOALANET\Administrator
Authentication: NTLM V2
What it means: The compromised web server successfully authenticated to the Domain Controller using a privileged account.
10b. Special privileges assigned
Finding: The Administrator session received special privileges.
Where to look:
Source files:
security.csv
test.txt
Search for:
Event ID 4672
KOALANET\Administrator
What it means: This confirmed that the logged-on Administrator session had high-level privileges on the Domain Controller.
10c. Evidence limitation
The same password, Consumer10, was also seen in the WordPress login traffic and was associated with internal Administrator access to the corporate share. This password reuse may explain how the adversary reached privileged internal accounts. However, the available evidence does not prove the exact step used to gain LocalSystem or Administrator privileges. The safest conclusion is that privileged access was confirmed, but the exact privilege escalation method was not identified.

7. Lateral Movement
How the adversary moved through the network
After the WordPress server 10.0.0.3 was compromised, the adversary gained access to systems inside the KOALANET network. The clearest evidence was found on the Domain Controller. On 22 Jun 20 at 00:52:14Z, Windows Security Event ID 4624 recorded a successful Logon Type 3 from the compromised web server 10.0.0.3 using the account KOALANET\Administrator. Domain Controller memory also showed an SMB connection on port 445 using the same source port. The web server had also been configured to access //10.0.0.2/CorporateShare, using Administrator credentials. This strongly supports movement from the compromised web server into the internal network using valid credentials and SMB access.
The Windows 7 workstation 10.0.0.12 was also involved in the compromise. On 21 Jun 20 at 23:53:51Z, the Domain Controller recorded a successful network logon from the workstation using KOALANET\abbey.soto. Memory analysis also showed an established SMB connection from Windows 7 to the Domain Controller on port 445. This confirms internal access between the two systems. However, the exact method used to place the malware on Windows 7 was not identified, so the evidence does not prove that the malware was delivered over SMB.
This activity mapped to MITRE ATT&CK T1078 – Valid Accounts and T1021.002 – SMB/Windows Admin Shares.
Evidence analysed – Web Server to Domain Controller

Finding: A successful Administrator network logon came from the compromised web server.

Where to look:

Source files:
security.csv
test.txt

Search for:

Event ID 4624
Logon Type 3
KOALANET\Administrator
10.0.0.3

Relevant event:

00:52:14Z 22 Jun 20
Source IP: 10.0.0.3
Destination: 10.0.0.2
Account: KOALANET\Administrator
Authentication: NTLM V2
Source Port: 41026

What it means: The compromised web server successfully logged onto the Domain Controller using a privileged account.

Supporting network evidence

Finding: Domain Controller memory showed an SMB connection matching the successful logon.

Where to look:

Source file:
VolatilityWorkbenchLognetscan.txt

Search for:

10.0.0.2:445
10.0.0.3:41026

Connection:

10.0.0.2:445 ↔ 10.0.0.3:41026

What it means: This supports SMB communication between the compromised web server and the Domain Controller.

Evidence analysed – Corporate share access

Finding: The web server had stored credentials for the Domain Controller share.

Where to look:

Source file:
.bash_history(1)

and:

Source file:
11.txt

Relevant locations:

//10.0.0.2/CorporateShare
/mnt/CorporateFiles/

Credential evidence:

username=administrator
password=Consumer10

What it means: The web server had direct access to an internal share using Administrator credentials. The same password was also seen in the WordPress login traffic, showing password reuse across the environment.

Evidence analysed – Windows 7 to Domain Controller

Finding: The Domain Controller recorded a successful network logon from Windows 7 workstation 10.0.0.12.

Where to look:

Source files:
test.txt
security.csv

Search for:

Event ID 4624
Logon Type 3
10.0.0.12
KOALANET\abbey.soto

Relevant event:

23:53:51Z 21 Jun 20
Source IP: 10.0.0.12
Destination: 10.0.0.2
Account: KOALANET\abbey.soto

What it means: This confirms that the compromised Windows 7 workstation was communicating with the Domain Controller using a valid domain account.

Supporting Windows 7 evidence

Finding: Memory analysis showed an established SMB connection from Windows 7 to the Domain Controller.

Where to look:

Source file:
VolatilityWorkbenchnetscan7.txt

Search for:

10.0.0.12:57316
10.0.0.2:445

Connection:

10.0.0.12:57316 → 10.0.0.2:445

What it means: This confirms SMB connectivity between the two systems. It does not prove that malware was copied to Windows 7 using SMB.

Evidence limitation
The evidence clearly shows that the compromise spread into the internal network and involved the Domain Controller and Windows 7 workstation. However, the exact route used to first compromise Windows 7 was not confirmed. Also, the successful Administrator logon from the web server happened after the ransomware impact, so it proves continued internal access rather than the original route that caused the earlier Domain Controller compromise
8. Persistence
How the adversary maintained access
The adversary set up more than one way to keep access after the initial compromise. On the WordPress server 10.0.0.3, the adversary used the WordPress theme editor to modify page.php. At 01:36:14Z on 18 Jun 20, the file was changed to include obfuscated PHP code that connected back to 5.112.3.2:5001. This gave the adversary a way to regain remote access through the compromised website.
Persistence was also found on the Windows 7 workstation 10.0.0.12. On 21 Jun 20 at 23:02:24Z, the malicious file PtKZjedI.exe was installed as an automatic Windows service called Routine updater for Windows applications. The service ran as LocalSystem and later entered the running state. This meant the malware could continue running with high privileges and start again after a reboot.
This activity mapped to MITRE ATT&CK T1505.003 – Web Shell and T1543.003 – Windows Service.
Evidence analysed – WordPress persistence
Finding: The adversary modified the WordPress theme file page.php.
Where to look:
Source files:
access.log.4
11.txt
Relevant path:
/var/www/html/wp-content/themes/twentyfourteen/page.php
Search the Apache log for:
/wp-admin/theme-editor.php
page.php
Relevant time:
01:36:14Z 18 Jun 20
What it means: The file contained obfuscated PHP code that connected back to the adversary on TCP port 5001. This gave the adversary continued remote access through the web server.
________________________________________
Supporting network evidence
Finding: Shortly after page.php was modified, the web server connected to the adversary IP.
Where to look:
Source file:
webserver_dc_smb_5001_clean.csv
Connection:
10.0.0.3:60268 → 5.112.3.2:5001
Relevant time:
01:36:57Z 18 Jun 20
What it means: The timing supports that the modified page.php was linked to the reverse connection back to the adversary.
________________________________________
Evidence analysed – Windows 7 persistence
Finding: PtKZjedI.exe was installed as an automatic Windows service.
Where to look:
Source file:
system77.txt
Search for:
Event ID 7045
Routine updater for Windows applications
PtKZjedI.exe
LocalSystem
Malicious file path:
C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe
Relevant time:
23:02:24Z 21 Jun 20
What it means: The malware was configured to start automatically and run as LocalSystem, allowing it to remain active after restart.
________________________________________
Supporting service evidence
Finding: The malicious service later entered the running state.
Where to look:
Source file:
system77.txt
Search for:
Event ID 7036
Routine updater for Windows applications
What it means: This shows the service was not only created but successfully started.
________________________________________
Additional persistence attempt
Finding: A second suspicious service named sunmeg was installed earlier using cmd.exe and a named pipe.
Where to look:
Source file:
system77.txt
Search for:
Event ID 7045
sunmeg
Command:
cmd.exe /c echo sunmeg > \\.\pipe\sunmeg
What it means: This was a suspicious persistence or execution attempt. However, the service later timed out, so successful persistence through sunmeg was not confirmed.
post exploitation but i need sub heading like defence evation, credential access, discovery, collection, c2 and exfiltration no need to make this one too big like other but give me each para with where and how and what happen make sure it human written
Thought for 13s
9. Post Exploitation
Defence Evasion
On 21 Jun 20 at 23:23:19Z, the Windows Security audit log on Domain Controller 10.0.0.2 was cleared shortly after the ransomware activity. This was identified in security.csv and test.txt through Event ID 1102.
Where to look:
security.csv / test.txt
Search: Event ID 1102
What it means: Clearing the Security log removes useful records of logons and system activity, making the investigation harder. This activity maps to MITRE ATT&CK T1070.001 – Clear Windows Event Logs.
________________________________________
Credential Access
The compromised web server contained cleartext credentials for access to the Domain Controller share //10.0.0.2/CorporateShare. The account used was administrator, and the password matched Consumer10, which was also seen in the WordPress login traffic from the adversary.
Where to look:
Source: .bash_history(1) and web-server mount evidence in 11.txt
Paths:
//10.0.0.2/CorporateShare
/mnt/CorporateFiles/
What it means: Password reuse exposed internal Administrator access after the public website was compromised. The exact method used by the adversary to obtain the internal credential was not confirmed. This activity is associated with T1552.001 – Credentials In Files and T1078 – Valid Accounts.
________________________________________
Discovery
On 21 Jun 20 at 22:55:15Z, whoami.exe ran on Windows 7 workstation 10.0.0.12.
Where to look:
Source: Windows 7 Volatility process output
Path:
C:\Windows\System32\whoami.exe
What it means: whoami.exe shows the current logged-on user and security context. This suggests the adversary was checking what account or privilege level was available on the workstation. This maps to T1033 – System Owner/User Discovery.
________________________________________
Collection
On 21 Jun 20 at 23:14:26Z, a large archive named LOCKED.rar was created on the Domain Controller shortly after suspicious command activity.
Where to look:
Source: image(10).png
Path:
C:\LOCKED.rar
What it means: The timing and size of the archive suggest it may have been used to collect or stage corporate data. However, the archive contents were not independently verified, so collection should be reported as likely, not confirmed.
________________________________________
Command and Control
On 18 Jun 20 at 01:36:57Z, the compromised WordPress server 10.0.0.3 connected back to adversary IP 5.112.3.2 on TCP port 5001. This happened shortly after the adversary modified the WordPress file page.php.
Where to look:
Source: webserver_dc_smb_5001_clean.csv
Connection:
10.0.0.3:60268 → 5.112.3.2:5001
Malicious file:
/var/www/html/wp-content/themes/twentyfourteen/page.php
The Windows 7 workstation also communicated with the same adversary IP on ports 16001 and 16002.
Where to look:
Source: VolatilityWorkbenchnetscan7.txt
10.0.0.12:58748 → 5.112.3.2:16001
10.0.0.12:58749 → 5.112.3.2:16002
What it means: These connections show that compromised systems were communicating back to adversary-controlled infrastructure. This activity is associated with T1095 – Non-Application Layer Protocol and T1571 – Non-Standard Port.
________________________________________
Exfiltration
The investigation did not confirm data exfiltration from the KOALANET network. Although LOCKED.rar may have been used to stage collected data and long-running connections were observed between compromised systems and 5.112.3.2, the available evidence did not show confirmed transfer of corporate files outside the network.
Where to look:
webserver_dc_smb_5001_clean.csv
VolatilityWorkbenchnetscan7.txt
image(10).png
What it means: External communication and possible staging were present, but there was not enough evidence to state that corporate data was successfully exfiltrated.
INDICATORS OF COMPROMISE Indicators of Compromise 24. Below are all the indicators of compromise observed during this incident. Host/Network IOC Location Network IP - PCAP, Apache logs, Memory Network Port - PCAP Host File – MD5 - Path: … Network User Agent - PCAP, WEB1 – Access.log Host Reg Key – a.exe HKLM/…/…/run 
Thought for 11s
INDICATORS OF COMPROMISE
24. Below are the indicators of compromise observed during this incident.
Host/Network	IOC	Location
Network	IP – 5.112.3.2	PCAP, Apache logs, memory analysis. Observed communicating with Web Server 10.0.0.3 and Windows 7 10.0.0.12.
Network	Port – TCP 5001	webserver_dc_smb_5001_clean.csv. Reverse connection: 10.0.0.3 → 5.112.3.2:5001.
Network	Port – TCP 16001	VolatilityWorkbenchnetscan7.txt. Connection: 10.0.0.12 → 5.112.3.2:16001, associated with ASjScLYWro.exe.
Network	Port – TCP 16002	VolatilityWorkbenchnetscan7.txt. Connection: 10.0.0.12 → 5.112.3.2:16002, associated with PtKZjedI.exe.
Network	User Agent – WPScan v3.8.1	Web Server Apache log: /var/log/apache2/access.log.4.
Host – Web Server	File – page.php	/var/www/html/wp-content/themes/twentyfourteen/page.php
Host – Web Server	File – 1.php_.txt	/var/www/html/wp-content/uploads/1.php_.txt
Host – Corporate Share	File – 1.php_.txt	/mnt/CorporateFiles/SurveyData/1.php_.txt / CorporateShare\SurveyData\1.php_.txt
Host – Domain Controller	File – YFzTPkztw.vbs	C:\Windows\Temp\YFzTPkztw.vbs
Host – Domain Controller	File – fbjWOkz.exe	C:\Users\Administrator\AppData\Local\Temp\rad872D2.tmp\fbjWOkz.exe
Host – Windows 7	File – ASjScLYWro.exe	C:\Users\abbey.soto\AppData\Local\Temp\ASjScLYWro.exe
Host – Windows 7	File – PtKZjedI.exe	C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe
Host – Domain Controller	File – LOCKED.rar	C:\LOCKED.rar
Host – Domain Controller	Ransom Note – Your Files Have Been Encrypted	C:\Your Files Have Been Encrypted
Host – Windows 7	Service – Routine updater for Windows applications	system77.txt, Event ID 7045. Binary: C:\Users\abbey.soto\AppData\Local\Temp\PtKZjedI.exe
Host – Windows 7	Service – sunmeg	system77.txt, Event ID 7045. Command: cmd.exe /c echo sunmeg > \\.\pipe\sunmeg
Host – Web Server	Credential – admin account used with Consumer10	Wireshark frame 726938; HTTP POST to /wp-login.php.
Host – Web Server	Credential exposure – internal Administrator credential	.bash_history(1) / mount configuration. Used for //10.0.0.2/CorporateShare mounted at /mnt/CorporateFiles/.
Host – Domain Controller	Account – KOALANET\Administrator	security.csv, test.txt; Event ID 4624, Logon Type 3, source 10.0.0.3.
Host – Domain Controller	Security Event – Event ID 1102	security.csv, test.txt. Security audit log cleared at 23:23:19Z 21 Jun 20.
Host – Web Server	Obfuscated code – eval(base64_decode(...))	/var/www/html/wp-content/themes/twentyfourteen/page.php and suspicious 1.php_.txt.
Host – Windows 7	Named Pipe – \\.\pipe\sunmeg	system77.txt, Event ID 7045.



