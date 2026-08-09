# Cybersecurity-Journey
Documenting my Cybersecuirty learning journey , labs , notes , writeups and projects .

## 📡 Featured Case Study: Dreamtel Wireless Outage Investigation

A real-world networking and troubleshooting case study documenting a **5-day wireless broadband outage** caused by a failed rooftop **Ubiquiti AirGrid M5 HP CPE** after a severe storm.

### What this investigation demonstrates

* Network topology analysis
* WAN vs LAN fault isolation
* Static IP troubleshooting
* ICMP connectivity testing
* airOS management diagnostics
* OSI layer mapping
* Root Cause Analysis (RCA)
* Preventive infrastructure recommendations

### Key Technical Finding

> **Local Wi-Fi connectivity does not guarantee Internet connectivity.**

The TP-Link router continued broadcasting Wi-Fi normally, but the **WAN link to the rooftop wireless CPE was physically down**, resulting in a complete loss of Internet access while local LAN services remained operational.

📄 **Full Writeup:** [`writeups/dreamtel-wireless-outage.md`](writeups/dreamtel-wireless-outage.md)

