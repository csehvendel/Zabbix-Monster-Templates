# Zabbix Cisco Catalyst 9300 "Monster" Template

![Zabbix Version](https://img.shields.io/badge/Zabbix-7.4%2B-red) ![Cisco IOS-XE](https://img.shields.io/badge/Cisco-IOS--XE-blue) ![Method](https://img.shields.io/badge/Method-Hybrid%20(SNMP%20%2B%20SSH)-green) ![License](https://img.shields.io/badge/License-MIT-orange)

**The ultimate monitoring solution for Cisco Catalyst 9300 (IOS-XE) switches.**

This template transcends standard SNMP monitoring. It implements a **Hybrid Approach** (SNMP + SSH) to overcome the limitations of modern IOS-XE versions, specifically regarding the deprecated or incomplete *Device Tracking* MIBs.

By securely logging into the switch via SSH and parsing CLI outputs, this template provides **100% visibility** into connected endpoints, IP conflicts, and port usage that standard templates miss.

---

## 🌟 Complete Feature List

### 1. 🕵️‍♂️ Advanced Device Tracking (SSH-Based)
Standard SNMP (`IP-MIB` or `CISCO-IP-DEVICE-TRACKING-MIB`) often fails to report all connected devices on newer IOS-XE versions (reporting ~10 entries instead of hundreds).
* **Method:** Executes `show device-tracking database` via SSH.
* **Capability:** Retrieves **ALL** connected endpoints (DHCP Snooping, ARP, ND, Static).
* **Performance:** Tested with 160+ active endpoints with zero data loss.
* **Output:** raw JSON data available for debugging (`Application: Zabbix Raw Data`).

### 2. 🚨 Intelligent IP Conflict Monitor
A built-in "Police" logic that scans the retrieved database for network inconsistencies.
* **Logic:** Detects if a single IP address is associated with multiple *different* MAC addresses.
* **IPv6 Ready:** Fully supports IPv6 conflict detection.
* **Smart Filtering:** Automatically ignores IPv6 Link-Local (`fe80::`) addresses to prevent false positives (as these are unique only per link).
* **Alerting:** Fires a **HIGH** severity trigger immediately: `CRITICAL: IP Conflict detected! 192.168.x.x [MAC1 vs MAC2]`.

### 3. 🗺️ Port-to-IP Neighbor Discovery
Automatically maps connected IP addresses to physical interfaces. No need to manually cross-reference MAC tables or ARP caches.
* **Integration:** Injects IP information directly into Interface statistics.
* **Naming Convention:** `Interface Gi1/0/48: Neighbor IP Address(es)`
* **Format:** `IPv4: 10.1.5.2 | IPv6: 2001:db8::2`
* **Grouping:** Automatically tagged with `Monitoring: IP Monitor` and the specific interface name.

### 4. 🥞 Stack & Hardware Health (SNMP)
Robust standard monitoring via optimized SNMP OIDs.
* **StackWise Monitoring:**
    * Ring Redundancy status.
    * Stack Member count & status.
    * Stack Port health.
* **Environment:**
    * Temperature sensors.
    * Fan status (RPM/Status).
    * Power Supply status.
* **Performance:**
    * CPU Usage (Control Plane & Data Plane).
    * Memory Usage (Processor & IO).

### 5. 🔌 Interface Statistics
* Traffic (Bits/sec).
* Packet Errors/Discards.
* Link Status & Speed.
* Description & Alias.

---

## 🛠️ Prerequisites

1.  **Zabbix Server:** Version **7.4** or higher (Required for new import format and UUIDs).
2.  **Timeout Configuration:**
    * Edit `zabbix_server.conf` or `zabbix_proxy.conf`.
    * Set `Timeout=30` (SSH commands and large JSON parsing need time).
    * *Restart Zabbix server/proxy after changing this.*
3.  **Switch Configuration:**
    * SNMP v2c/v3 enabled (Read-Only).
    * SSH enabled.
    * Device Tracking (SISF) enabled on ports (default on many 9300 configs).

---

## ⚙️ Installation Guide

### 1. Import
Download the `.json` file from this repository and import it into Zabbix:
* `Data collection` -> `Templates` -> `Import`.
* Check "Create new" and "Update existing".

### 2. Configure Host Macros
Link the template to your Cisco 9300 Host. Go to the **Macros** tab (Inherited and host macros) and set the following:

| Macro | Value Example | Type | Description |
| :--- | :--- | :--- | :--- |
| `{$SNMP_COMMUNITY}` | `public` | Text | SNMP Read Community string. |
| `{$SSH_USER}` | `zabbix_mon` | Text | Username for SSH login. |
| `{$SSH_PASSWORD}` | `Secret123!` | **Secret Text** | Password for SSH login. |

### 3. Verify Operation
1.  Go to `Monitoring` -> `Hosts`.
2.  Find your switch and click `Latest Data`.
3.  Filter by Tag: `Monitoring` : `IP Monitor`.
4.  You should see:
    * `SSH: Device Tracking Database (CLI)` with JSON data.
    * `Net: IP Conflict Monitor` showing "OK".
    * Multiple `Interface ... Neighbor IP Address(es)` items.

---

## 🧠 How It Works (The "Magic")

1.  **Data Fetch:** The `ssh.run[]` item connects to the switch and runs the CLI command.
2.  **Parsing:** JavaScript preprocessing converts the text output (headers, columns) into a standard JSON array.
3.  **Discovery (LLD):** The `Active Ports Discovery` rule iterates through this JSON. If it finds an IP on `Gi1/0/1`, it creates a Dependent Item for that specific port.
4.  **Correlation:** The dependent items extract IPv4 and IPv6 addresses for their specific port and display them as a string.

---

## 🤝 Contributing
Found a bug? Have an idea for QoS monitoring?
Pull requests are welcome! This template is a community effort to tame the Cisco IOS-XE monitoring beast.

**Created by:** [Vendel Cseh]

If you like the project, support it with PayPal (csehvendel@gmail.com) because a lot of coffee is being sold while I'm grinding this...
