# Route Windows VM Traffic Through pfSense

This guide explains how to route all outbound traffic from your Windows VM through a pfSense firewall in a VirtualBox lab environment. This enables network traffic inspection and filtering via Snort or Suricata.

---

##  Lab Setup

| Component      | Role                      | Network Adapters                   |
|----------------|---------------------------|------------------------------------|
| pfSense VM     | Firewall/Gateway          | `Adapter 1`: NAT<br>`Adapter 2`: Internal Network (`intnet`) |
| Windows VM     | Agent                     | `Adapter 1`: NAT or Bridged<br>`Adapter 2`: Internal Network (`intnet`) |
| Ubuntu VM      | Server (Wazuh, Splunk, Velociraptor) | `Adapter 1`: NAT or Bridged<br>`Adapter 2`: Internal Network (`intnet`) |

> **Internal Network Name**: `intnet`  
> Ensure all VMs use the *same* internal network for adapter 2.

---

##  Step-by-Step: Route Traffic from Windows via pfSense

### 1. Identify Network Interfaces

In the Windows VM, open Command Prompt and run:

```bash
ipconfig
```

Sample output:

```
Ethernet     --> 192.168.1.156 (NAT/Bridged)
Ethernet 3   --> 10.0.4.15     (Internal Network via pfSense)
```

---

### 2. Set Interface Priority

Open PowerShell as Administrator:

```powershell
Get-NetIPInterface
```

Lower the interface metric of `Ethernet 3` to give it higher priority:

```powershell
Set-NetIPInterface -InterfaceAlias "Ethernet 3" -InterfaceMetric 1
```

(Optional: Set higher metric for the other interface)

```powershell
Set-NetIPInterface -InterfaceAlias "Ethernet" -InterfaceMetric 50
```

---

### 3. Confirm Routing

Check the new default route:

```powershell
route print
```

Make sure the default gateway (`0.0.0.0`) now points to `10.0.4.2` (pfSense internal IP).

You can also run:

```bash
tracert 8.8.8.8
```

The **first hop** should be `10.0.4.2`.

---

###  Result

Your Windows VM now routes all internet traffic through pfSense. This enables:

- Network inspection via Snort or Suricata on pfSense
- MITM testing, DNS redirection, blocking rules, etc.
- No need to remove NAT/Bridged; they remain as backup paths

---

##  Next Steps

- [ ] Enable Snort on pfSense and inspect traffic
- [ ] Block domains or ports via firewall rules
- [ ] Simulate malware traffic and observe detections

---

##  Notes

- If `Ethernet 3` gets a 169.254.x.x IP, ensure DHCP is enabled in pfSense LAN or assign static IP manually.
- Do **not** remove NAT/Bridged unless you want complete isolation from host/internet.

---
