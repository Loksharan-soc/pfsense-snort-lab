\# Snort Configuration on pfSense



This guide covers the full setup and configuration of \*\*Snort\*\* as an IDS/IPS on pfSense. We’ll walk through installing the Snort package, configuring interfaces, applying rule sets, and testing detection using simulated attacks.



---



\## Prerequisites



\- pfSense is fully installed and accessible via Web UI (`https://192.168.1.1`)

\- Ubuntu or Kali VM is connected to the same virtual network for generating traffic

\- Internet access on pfSense WAN interface (for downloading rules)



---



\## Step 1: Install Snort Package



1\. Log in to the pfSense Web UI.

2\. Go to: \*\*System → Package Manager → Available Packages\*\*

3\. Search for `snort`

4\. Click \*\*Install\*\*, then \*\*Confirm\*\*

5\. Wait for installation to complete



---



\## Step 2: Configure Snort Interface



1\. Go to: \*\*Services → Snort\*\*

2\. Click \*\*+ Add\*\* to create a new interface

3\. Set the following:

&nbsp;  - \*\*Interface\*\*: LAN (or WAN, depending on detection target)

&nbsp;  - \*\*Enable\*\*: checked

&nbsp;  - \*\*Description\*\*: `Snort\_LAN`

&nbsp;  - \*\*Block Offenders\*\*:  enabled (optional)

&nbsp;  - \*\*IPS Mode\*\*: Legacy Mode (default)

4\. Click \*\*Save\*\*



Then click \*\*Apply Changes\*\*



---



\## Step 3: Download and Enable Rule Sets



1\. Go to the \*\*Global Settings\*\* tab

2\. Choose the rule sources:

&nbsp;  - \*\*Snort VRT Rules\*\*: requires registration

&nbsp;  - \*\*ET Open Rules\*\*:  check this (free)

3\. Click \*\*Save\*\*



4\. Go to \*\*Updates\*\* tab

5\. Click \*\*Update Rules\*\*

6\. Wait for all rule packages to download



---



\## Step 4: Enable Categories for Detection



1\. Go to \*\*Snort Interfaces → LAN → Categories\*\*

2\. Enable relevant rule categories:

&nbsp;  - attack-responses

&nbsp;  - policy-violation

&nbsp;  - malware-cnc

&nbsp;  - trojan-activity

&nbsp;  - scan

&nbsp;  - exploit

&nbsp;  - dns



You can start by enabling everything under `ET open` to see broader detection.



3\. Click \*\*Save\*\*



---



\## Step 5: Snort Logging and Blocking



\- Go to \*\*Snort Interfaces → LAN → General Settings\*\*

\- Make sure logging is enabled:

&nbsp; - \*\*Log to System Log\*\*: checked

&nbsp; - \*\*Block Offenders\*\*: check (optional, for IPS behavior)

\- Click \*\*Save\*\* and \*\*Apply\*\*



Logs are viewable under:

\- \*\*Status → System Logs → Snort\*\*



---



\## Step 6: Test Snort with Simulated Attack (Ping or Nmap)



\### Option 1: Simple ICMP Traffic (ping)



From Ubuntu or Kali:



```bash

ping 192.168.1.1

```



\- Go to \*\*Snort Logs\*\* and verify alert detection (under `ICMP info` category)



\### Option 2: Nmap Port Scan



From Kali/Ubuntu:



```bash

nmap -sS 192.168.1.156

```



\- Should trigger alerts under categories like `scan` or `policy-violation`



---



\## Step 7: Tune Rules and Suppress False Positives



\- Navigate to \*\*Suppress List\*\* under Snort

\- Add entries for alerts you don’t want to be triggered again

\- Use rule SIDs from logs to suppress specific alerts



---



\## Step 8: Enable IPS Blocking Mode (Optional)



If you want Snort to actively block traffic:



1\. Go to \*\*Snort Interfaces → LAN → General Settings\*\*

2\. Enable:

&nbsp;  - \*\*Block Offenders\*\*

&nbsp;  - \*\*Kill States\*\*

&nbsp;  - \*\*IPS Mode: Legacy Mode\*\*

3\. Save and Apply



Note: This can interfere with testing if not tuned properly.



---



\## Next Steps



\- \[→ Create custom firewall rules](../firewall-and-snort-rules.md)

\- \[→ Simulate malware download, port scans, brute force](../simulations/)

\- \[→ Forward Snort alerts to Splunk or Wazuh](../log-forwarding.md)



---



\## References



\- https://docs.netgate.com/pfsense/en/latest/packages/snort/index.html

\- https://www.snort.org/downloads

\- https://rules.emergingthreats.net/



