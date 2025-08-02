# Wazuh Integration with pfSense Logs

This guide explains how to forward logs from pfSense to a Wazuh server for centralized log monitoring and analysis.

---

## Prerequisites

- A running Wazuh Manager (e.g., on Ubuntu)
- pfSense firewall with Web UI access
- Network access from pfSense to Wazuh server (default port 514/UDP)

---

## Step 1: Enable and Configure Syslog on pfSense

1. Log in to pfSense Web UI.
2. Navigate to **Status > System Logs > Settings**
3. Under **Remote Logging Options**, check:
   - "Send log messages to remote syslog server"
4. In **Remote Syslog Servers**, add:
```
192.168.1.157:514
```
*(Replace with your Wazuh server IP if different)*

5. Select relevant **Remote Syslog Contents**:
   - Firewall Events
   - System Events
   - DHCP
   - Snort Alerts (if Snort is installed)
6. Click **Save**.

---

## Step 2: Configure Wazuh to Receive Syslog

On the Wazuh server (Ubuntu):

### 1. Open syslog UDP port on firewall

```bash
sudo ufw allow 514/udp
sudo ufw reload
```

### 2. Edit Wazuh configuration

Edit the Wazuh manager configuration:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add inside the `<ossec_config>` tag:

```xml
<remote>
  <connection>syslog</connection>
  <port>514</port>
  <protocol>udp</protocol>
</remote>
```

Save and exit (`Ctrl + O`, `Enter`, `Ctrl + X`).

### 3. Restart Wazuh manager

```bash
sudo systemctl restart wazuh-manager
```

---

## Step 3: Verify Logs in Wazuh

- Check Wazuh dashboard or run:
```bash
tail -f /var/ossec/logs/ossec.log
```
- Look for incoming logs from pfSense IP.

If you don’t see logs, double-check your pfSense configuration and network/firewall rules.

---

## Notes

- Wazuh may require custom decoders for pfSense logs. Check documentation for rule tuning.
- Syslog logs are unencrypted by default unless you configure TLS (advanced setup).