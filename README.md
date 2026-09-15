# Cisco-EtherChannel-LACP-Implementation-Troubleshooting-Lab
# 🚀 Cisco EtherChannel (LACP) Implementation & Troubleshooting Lab

![Cisco](https://img.shields.io/badge/Cisco-Packet%20Tracer-005073?style=for-the-badge&logo=cisco&logoColor=white)
![Network](https://img.shields.io/badge/Layer%202-EtherChannel%20%2F%20LACP-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Verified%20%26%20Tested-success?style=for-the-badge)

---

## 📌 Project Overview
This lab demonstrates the theoretical and practical deployment of **EtherChannel (Link Aggregation)** between Cisco Catalyst switches using **LACP (IEEE 802.3ad)**. The objective is to combine multiple physical links into a single logical link (`Port-Channel`) to achieve higher aggregated bandwidth, eliminate Spanning Tree Protocol (STP) blocked states, and provide link redundancy.

---

## 🏗️ Topology Architecture
- **Devices:** 2x Cisco 2960 Switches
- **Physical Bundled Links:** `FastEthernet 0/3` and `FastEthernet 0/4`
- **Logical Bundle:** `Port-channel 1 (Po1)`
- **Protocol:** LACP (Mode: `Active` - `Active`)
- **Trunk Encapsulation:** 802.1Q (Trunking mode configured across bundle)
```text
+------------------+                    +------------------+
|                  |---- Fa0/3 (LACP) --|                  |
|  Switch-01 (SW1) |                    |  Switch-02 (SW2) |
|                  |---- Fa0/4 (LACP) --|                  |
+------------------+                    +------------------+
\                                        /




+========= Port-Channel 1 (Po1) ========+
⚙️ Configuration Walkthrough

To prevent bundle discrepancies and dynamic protocol negotiation conflicts (e.g., DTP errors), ports are defaulted and configured strictly within a defined maintenance window (shutdown state):

        

cisco
! === Step 1: Wipe prior inconsistent configurations ===
Switch(config)# default interface range FastEthernet 0/3 - 4

! === Step 2: Configure interfaces inside bundle safely ===
Switch(config)# interface range FastEthernet 0/3 - 4
Switch(config-if-range)# shutdown
Switch(config-if-range)# switchport mode trunk
Switch(config-if-range)# channel-group 1 mode active
Switch(config-if-range)# no shutdown

! === Step 3: Apply configuration to the logical Port-Channel ===
Switch(config)# interface Port-channel 1
Switch(config-if)# switchport mode trunk

🛠️ Real-World Troubleshooting & Gotchas Encountered

During real-world setup, several standard Layer-2 traps were diagnosed and resolved:
Issue / Error Log 	Root Cause 	Solution
(s) - Suspended in summary 	Interface parameter mismatch (Speed/Duplex/VLAN list) 	Cleared ports with default int range and standardized trunk settings.
%EC-5-CANNOT_BUNDLE2 	Conflicting DTP (Dynamic Trunking Protocol) states between member ports 	Explicitly forced both ends to static switchport mode trunk.
STP Loop & Delay 	Using manual mode on without control plane handshakes 	Migrated strictly to LACP (mode active) for dynamic health checks.
🔍 Verification & Output Proof

Executing show etherchannel summary confirms the operational state of the bundle:

        

text

Flags:  D - down        P - bundled in port-channel
I - stand-alone s - suspended
H - Hot-standby (LACP only)
R - Layer3      S - Layer2
U - in use      f - failed to allocate aggregator

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         LACP      Fa0/3(P)    Fa0/4(P)

    Po1(SU): Port-Channel 1 is Layer 2 (S) and currently In Use / UP (U).
    Fa0/3(P) & Fa0/4(P): Both physical members are fully active and bundled into the logical channel.

🧪 Redundancy & Failover Testing

    Initiated continuous ping stream (ping -t) across the link.
    Simulated physical line break by shutting down Fa0/3.
    Result: Zero-packet-loss failover; traffic seamlessly shifted across Fa0/4 inside Po1 without triggering STP topology change recalculations.

👨‍💻 Author

    EHSAN SALEHI
    Network Engineer & Cybersecurity Enthusiast

        
