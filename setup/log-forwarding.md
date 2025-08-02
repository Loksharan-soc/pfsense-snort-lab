# log-forwarding.md

# Log Forwarding from pfSense to SIEM

This guide explains how to forward pfSense logs (firewall events, system logs, Snort alerts) to external log collectors like Splunk or Wazuh.

---

## Prerequisites

- pfSense installed and accessible via Web UI
- A log collector (Splunk or Wazuh) set up and reachable from pfSense
- Network connectivity between pfSense and log collector

---

## Step 1: Configure Remote Logging in pfSense

1. Log in to the pfSense Web UI.
2. Navigate to: **Status > System Logs > Settings**
3. Scroll to **Remote Logging Options** and check:
   - Send log messages to remote syslog server
4. In **Remote Syslog Servers**, enter the IP and port(s) of your log servers, separated by commas. For example:
   ```
   192.168.1.157:5516, 192.168.1.157:514
   ```
5. Under **Remote Syslog Contents**, select the types of logs you want to forward:
   - Firewall Events
   - System Events
   - DHCP
   - Snort Alerts (if installed)
6. Scroll down and click **Save**.

Logs are now forwarded to your configured syslog-compatible SIEM tools.

---

## Notes

- Make sure your SIEM tools (Splunk, Wazuh) are configured to listen on the specified ports.
- pfSense sends logs using UDP by default.
