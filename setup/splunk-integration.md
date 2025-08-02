
# Splunk Integration with pfSense

This guide explains how to integrate pfSense with Splunk to monitor firewall logs, system events, and Snort alerts.

---

## Prerequisites

- pfSense is installed and accessible via Web UI.
- Splunk Enterprise or Splunk Free installed and accessible (e.g., https://192.168.1.157:8000).
- Network connectivity between pfSense and Splunk server.
- Port 5516 open on Splunk server.

---

## Step 1: Create UDP Syslog Input on Splunk

1. Log in to the Splunk Web UI.
2. Go to: **Settings > Data Inputs > UDP > Add New**
3. Enter the following:
   - **UDP Port:** `5516`
   - **Source type:** `pfsense_syslog` (or create a new one)
   - **App Context:** `launcher` (default)
   - **Host:** select "IP" and enter pfSense's IP address (or leave blank for automatic detection)
   - **Index:** `main` (or create a new index like `pfsense`)
4. Click **Save**.

---

## Step 2: Configure pfSense to Forward Logs to Splunk

1. Log in to pfSense Web UI.
2. Navigate to: **Status > System Logs > Settings**
3. Scroll to **Remote Logging Options** and check:
   - Send log messages to remote syslog server
4. In **Remote Syslog Servers**, enter:
   ```
   192.168.1.157:5516
   ```
   *(Replace with your Splunk server IP)*
5. Under **Remote Syslog Contents**, check options like:
   - Firewall Events
   - System Events
   - DHCP
   - Snort Alerts
6. Click **Save**.

---

## Step 3: Verify Logs in Splunk

1. Go to **Search & Reporting** app in Splunk.
2. Run the search query:
   ```spl
   index=main sourcetype=pfsense_syslog
   ```
3. You should see logs from pfSense, including Snort alerts.

---

## Optional: Create Dashboards

- Build visualizations using firewall logs, alert types, and traffic patterns.
- Use fields like `host`, `src_ip`, `dest_ip`, and `signature` for filtering and charts.
