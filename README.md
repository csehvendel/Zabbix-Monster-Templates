# Zabbix-Cisco-9300-Monster-Template
under development!

Cisco Catalyst 9300 Zabbix Template
A comprehensive Zabbix template for monitoring Cisco Catalyst 9300 Series switches via SNMP. Designed for high-density environments where tracking hardware health, transceiver levels, and network stability is critical.

Key Features
Conflict Tracking: Advanced JavaScript preprocessing to detect IPv4/IPv6 address conflicts (ARP/NDP) in real-time.

Hardware Discovery:

Chassis & Modules: Automatic discovery of switch members, uplink modules, and stack cables with Serial Numbers and PIDs.

Power Supplies: Status, model identification, and wattage extraction.

Fans: Detailed monitoring of fan tray health and status.

Interface Monitoring:

Traffic Stats: Detailed bits/packets (Unicast/Multicast/Broadcast) per second.

Error Tracking: FCS errors, inbound/outbound discards, and physical layer issue detection.

Intelligent Neighbors: Detailed CDP/LLDP neighbor discovery (identifies if the neighbor is an IP Phone, AP, Switch, or Router).

Optics (SFP) Monitoring: Transceiver power levels (dBm), temperature, and voltage with pre-configured high/low signal triggers.

PoE Management: Monitors total power budget vs. actual consumption per switch member, with high-usage alerts.

Spanning Tree (RPVST): Tracks port states and alerts on blocked or "broken" states.

🛠️ Requirements
Zabbix Version: 7.4 or newer.

Protocol: SNMPv2 or SNMPv3.

Device Configuration: Ensure SNMP is enabled on your Cisco switches. For detailed neighbor info, CDP/LLDP must be active.

Installation
Download the zbx_export_templates.yaml file.

In Zabbix, go to Data collection > Templates.

Click Import and select the file.

Link the template to your Cisco 9300 hosts.

☕ Support the Project
A lot of work and even more coffee went into this "scraping". If you find the script useful and would like to support the project (or just throw in a coffee/pizza for further development), you can do so here:
PayPal: csehvendel@gmail.com
