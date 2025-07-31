# pfSense Installation Guide

This guide documents the complete installation of **pfSense** on VirtualBox, including network interface setup, Web UI access using an Ubuntu VM, and initial configuration steps. This forms the network security foundation for the lab environment involving Snort, Splunk, Wazuh, and simulated attacks.

---

## Prerequisites

- VirtualBox installed
- pfSense ISO (download from: https://www.pfsense.org/download/)
- Ubuntu VM installed (used to access pfSense Web UI)
- Kali Linux or attacker VM (for simulations, optional for now)

---

## Step 1: Create pfSense VM in VirtualBox

### VM Settings

- **Name**: `pfSense`
- **OS Type**: Other → BSD → FreeBSD (64-bit)
- **Memory**: 2048 MB
- **Processors**: 2
- **Hard Disk**: 10–16 GB (VDI, dynamically allocated)
- **ISO**: Attach the pfSense ISO under Storage → Optical Drive

### Network Adapters (Very Important)

- **Adapter 1** (WAN): `NAT`
- **Adapter 2** (LAN): `Host-only Adapter` or `Internal Network`

Ensure that your Ubuntu VM is also connected to the **same Host-only Adapter** as pfSense Adapter 2 (LAN), so it can access the pfSense LAN interface.

---

## Step 2: Boot and Install pfSense

1. Start the pfSense VM (ensure the ISO is attached).
2. Select `Install pfSense` at the prompt.
3. Accept license agreement.
4. Choose `Auto (UFS)` for guided disk partitioning.
5. Select the virtual disk when prompted.
6. Wait for installation to complete.
7. Select `No` when asked to open a shell.
8. Reboot the VM.
9. After reboot, **eject the ISO** from the virtual CD drive.

---

## Step 3: Console Setup (Assign Interfaces)

Once pfSense boots, you’ll be prompted to assign interfaces.

- **WAN**: Usually `vtnet0` or `em0` (attached to NAT)
- **LAN**: Usually `vtnet1` or `em1` (attached to Host-only)

Assign interfaces like this:

```
Do you want to set up VLANs now [y|n]? n
Available interfaces: vtnet0 vtnet1
Enter WAN interface name: vtnet0
Enter LAN interface name: vtnet1
```

pfSense will assign:

- WAN IP: via DHCP (e.g., 10.x.x.x from NAT)
- LAN IP: default 192.168.1.1/24

---

## Step 4: Access pfSense Web UI from Ubuntu VM

### Ubuntu Network Setup

On your Ubuntu VM:

1. Go to **Settings → Network**
2. Add a **Host-only Adapter** (same as pfSense LAN)
3. Boot Ubuntu

### Assign Static IP (if needed)

If Ubuntu doesn't receive an IP from pfSense:

```bash
sudo ip addr add 192.168.1.10/24 dev enp0s8
sudo ip route add default via 192.168.1.1
```

- `enp0s8` is typically the host-only interface (check using `ip a`)
- Ping pfSense to confirm connectivity:
  ```bash
  ping 192.168.1.1
  ```

### Access Web Interface

- Open Firefox or Chromium on Ubuntu
- Navigate to: `https://192.168.1.1`
- Accept the SSL warning
- Login credentials:
  - Username: `admin`
  - Password: `pfsense`

---

## Step 5: pfSense Web Setup Wizard

1. Go through the wizard steps:
   - Set hostname (e.g., `pfsense-lab`)
   - Set domain (optional)
   - Confirm WAN is set to DHCP
   - Set LAN IP (optional; default is fine)
   - Set admin password
2. Finish and apply changes

---

## Step 6: Optional System Settings

- **Enable SSH**:
  - System → Advanced → Admin Access
  - Enable Secure Shell
- **Set DNS Servers**:
  - System → General Setup
- **Disable IPv6** if not used
- **Update**:
  - Dashboard → System Update (recommended after first login)

---

## Troubleshooting

- **No IP on LAN interface?** Assign one manually via console:
  ```bash
  Option 2: Set interface IP address
  Interface: LAN
  IP: 192.168.1.1
  Subnet: 24
  ```

- **Can't access Web UI?**
  - Check Ubuntu LAN IP/subnet
  - Verify firewall settings in pfSense (LAN should allow web config)

---

## Next Steps

- [→ Install and configure Snort](snort-configuration.md)
- [→ Forward logs to Wazuh and Splunk](log-forwarding.md)
- [→ Create firewall and IDS/IPS rules](../firewall-and-snort-rules.md)
- [→ Simulate network attacks using Kali](../simulations/nmap-scan.md)

---

## References

- https://docs.netgate.com
- https://www.pfsense.org/download/
- https://forum.netgate.com/
