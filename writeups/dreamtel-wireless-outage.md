# Dreamtel Wireless Internet Outage Investigation

---

**Repository:** `Cybersecurity-Journey`  
**Category:** *Networking / Troubleshooting / Infrastructure Case Study*  
**Author:** **Vishavjit Singh**  
**Status:** 🟢 **Resolved**

### Incident Timeline

- **04 June 2026** *(Severe storm occurred; internet connection stopped working)*
- **05 June 2026** *(Observed WAN LED OFF and Internet LED OFF; performed router factory reset)*
- **06 June 2026** *(Inspected rooftop CPE, Ethernet cable, and connector condition)*
- **07 June 2026** *(First technician replaced RJ45 connector, checked PoE injector, configured static IP, and tested ping connectivity)*
- **08 June 2026** *(Repeated diagnostics showed the CPE management interface was still unreachable)*
- **09 June 2026** *(Second technician tested multiple CPE units, accessed the airOS interface, replaced the faulty rooftop CPE, and restored internet service)*
---

## Executive Summary

This document records a real-world wireless broadband outage investigation involving a Dreamtel wireless ISP connection, a TP-Link Archer C24 router, and a rooftop wireless CPE (Customer Premises Equipment).

The incident began immediately after a severe storm and resulted in a complete loss of internet connectivity for approximately five days. Although the local Wi-Fi network remained operational, both the **WAN** and **Internet** indicators on the router remained inactive, suggesting an upstream connectivity failure rather than a router-side configuration issue.

The investigation included physical inspection of the rooftop wireless receiver, Ethernet connector testing, PoE verification, static IP configuration, ICMP reachability testing, and access attempts to the CPE management interface. Service was eventually restored after the second technician replaced the rooftop CPE and reconfigured the wireless link through the airOS management interface.

This case study demonstrates practical troubleshooting across the **Physical (Layer 1)**, **Data Link (Layer 2)**, and **Network (Layer 3)** layers of the OSI model and highlights the importance of structured diagnosis before assuming software or configuration failure.

---

## Network Environment

### Internet Service Provider

* **ISP:** Dreamtel
* **Connection Type:** Wireless broadband (non-fiber)

### Customer Equipment

* **Router:** TP-Link Archer C24
* **Rooftop Device:** Wireless CPE / AirGrid-type receiver
* **Power Delivery:** PoE (Power over Ethernet) injector hidden behind the switchboard/LCD panel

### Topology

```text
Dreamtel Tower
        ↓
Wireless Signal
        ↓
Rooftop CPE / AirGrid
        ↓ Ethernet Cable
PoE Injector
        ↓
TP-Link Archer C24
        ↓
Local Devices (Phone / Laptop)
```

---

## Initial Problem Statement

The internet connection stopped functioning immediately after a severe storm.

### Observed Symptoms

* Wi-Fi SSID was visible and devices could connect locally.
* Router power LED was **ON**.
* 2.4 GHz and 5 GHz wireless LEDs were **ON**.
* **WAN LED was OFF.**
* **Internet LED was OFF.**
* No internet access was available on any connected device.

This immediately suggested that the router was functioning locally but was unable to establish a link with the upstream network device.

---

## Initial Troubleshooting Performed

### Router Factory Reset

The router was reset using the **WPS/Reset** button for approximately **30 seconds**.

After reconfiguration:

* The original SSID was restored.
* The previous Wi-Fi password worked successfully.
* Wireless connectivity was functional again.

### Conclusion

The successful restoration of Wi-Fi functionality indicated that:

* The router hardware was operational.
* Basic router configuration was valid.
* The failure was unlikely to be caused by the Wi-Fi subsystem.

---

## Rooftop Inspection

A physical inspection of the rooftop wireless receiver was performed.

### Findings

* The antenna was physically mounted correctly.
* The Ethernet cable was connected to the CPE.
* No obvious mechanical damage was visible.
* The mounting hardware showed signs of rust, but no structural failure was observed.

### Night-Time Observation

Under low-light conditions, the LEDs that were previously visible on the CPE were no longer illuminating consistently. This became an important diagnostic clue because a healthy wireless CPE normally shows at least a stable power or status indication.

---

## Ethernet Connector Inspection

The RJ45 connector connected to the rooftop CPE was removed and inspected.

### Observations

* Connector was physically intact.
* Pins appeared worn and somewhat dirty.
* Slight discoloration suggested possible moisture or oxidation exposure.

### Initial Hypothesis

Potential causes considered:

1. Connector corrosion due to rain exposure
2. Ethernet cable damage
3. PoE delivery failure
4. CPE hardware instability

---

## First Technician Visit

The first technician performed several diagnostic actions.

### Actions Observed

#### 1. RJ45 Connector Replacement

The rooftop Ethernet connector was replaced with a new RJ45 termination.

**Result:** *No improvement.*

---

#### 2. PoE Inspection

A previously unknown **PoE injector** was discovered behind the switchboard/LCD panel. The technician disconnected and reconnected the PoE wiring and also tested with another PoE unit temporarily.

**Result:** *No successful restoration of the WAN link.*

---

#### 3. Direct Laptop Diagnostics

The technician connected a laptop directly to the Ethernet/PoE side of the connection, bypassing the router.

### Static IP Configuration

Values observed during troubleshooting:

```text
IP Address: 192.168.1.20
Subnet Mask: 255.255.255.0
```

This was done to place the laptop in the same management subnet as the wireless CPE.

---

#### 4. ICMP Reachability Testing

Repeated requests produced **Request Timed Out** responses, indicating that the target device was not responding to network probes.

This strongly suggested that either:

* the CPE was not booting correctly,
* the Ethernet path was broken,
* or the device was unreachable at Layer 2/Layer 3.

---

#### 5. Web Interface Access Attempts

The technician attempted to open management pages similar to:

```text
http://192.168.1.50
https://192.168.1.50
192.168.7.1:8000/network.cgi
```

The browser failed to load the device management interface.

### Outcome of First Visit

* WAN LED remained **OFF**.
* Internet connectivity was **not restored**.
* Technician suspected a faulty rooftop CPE and recommended a second technician visit.

---

## Second Technician Visit

The second technician focused primarily on the rooftop wireless receiver.

### Actions Performed

#### 1. Multiple CPE Units Tested

Several older CPE devices were connected one by one. During testing, some units exhibited unstable behavior where their LEDs would illuminate briefly and then turn off again.

This suggested that at least some candidate devices were not booting reliably.

---

#### 2. Working CPE Selected

After repeated testing, a CPE unit that maintained stable operation was connected to the Ethernet line and left installed on the rooftop.

---

#### 3. airOS Management Access

A second technician connected a laptop to the network and successfully opened the **airOS** management interface using a URL similar to:

```text
192.168.7.1:8000/network.cgi
```

The login process appeared to use a username derived from the customer identifier along with technician-managed credentials.

---

## airOS Interface Analysis

Although the captured photograph was blurred, the interface layout matched **Ubiquiti airOS 6** firmware commonly used on AirGrid / NanoStation devices.

### Likely Identified Parameters

* **Device:** AirGrid M5 HP
* **Network Mode:** Bridge
* **Wireless Mode:** Station
* **Frequency Band:** 5 GHz
* **LAN Status:** 100Mbps Full Duplex
* **Signal Strength:** approximately -65 to -70 dBm
* **Channel Width:** 20 MHz

### Important Observation

If the **LAN0 Status: 100Mbps Full** reading was accurate, it indicated that:

* the Ethernet cable was physically linked,
* the PoE injector was delivering power correctly,
* and the replacement CPE was communicating successfully with the laptop.

This was a significant difference from the earlier failed diagnostic attempts.

---

## Service Restoration

After the replacement CPE was installed and configured through the airOS interface:

* WAN connectivity was restored.
* Internet access became functional again.
* The router returned to normal operational behavior.

The original PoE injector was **not permanently replaced**; it was reused after reconnection and testing.

---

## Root Cause Analysis

### Confirmed Facts

* Router remained operational throughout the incident.
* Wi-Fi broadcasting functionality was unaffected.
* Direct laptop diagnostics initially failed to reach the rooftop device.
* CPE replacement resulted in successful restoration of service.

### Most Probable Root Cause

The evidence strongly suggests that the **original rooftop wireless CPE became faulty after the storm**, possibly due to one of the following:

* moisture ingress caused by heavy rain,
* electrical surge damage,
* Ethernet interface instability,
* or internal power regulation failure.

Because the technicians restored service by replacing the CPE rather than the router, the router can reasonably be considered **not responsible for the outage**.

---

## OSI Layer Mapping

### Layer 1 – Physical

* Ethernet cable
* RJ45 connectors
* PoE power delivery
* Rooftop antenna hardware

### Layer 2 – Data Link

* WAN link establishment
* Ethernet link negotiation
* MAC-level communication between router and CPE

### Layer 3 – Network

* Static IP assignment
* Ping / ICMP testing
* Accessing management IP addresses

### Layer 7 – Application

* airOS web management interface
* Browser-based configuration access

This incident provided a practical demonstration that **Physical and Data Link layer problems can completely prevent higher-layer network services from functioning**, even when Wi-Fi appears to be working normally.

---

## Cybersecurity and Networking Learnings

### Practical Concepts Learned

* Difference between **Wi-Fi availability** and **internet availability**
* Meaning of **WAN LED OFF**
* Role of **PoE injectors** in wireless ISP deployments
* Purpose of **static IP configuration** during infrastructure troubleshooting
* How **ping** is used to verify device reachability
* How wireless CPE devices expose **web-based management interfaces**
* Importance of **layered troubleshooting** instead of immediately blaming software configuration

### Key Insight

A critical lesson from this investigation was:

> **Never assume a router is the source of an internet outage simply because internet access is unavailable.**

The local wireless network remained fully functional while the actual failure existed in the upstream wireless CPE communication path.

---

## Preventive Recommendations

To reduce the impact of similar incidents in the future:

### Hardware Protection

* Use weather-sealed outdoor Ethernet connectors.
* Add proper drip loops to rooftop cabling.
* Inspect connectors periodically for corrosion.

### Electrical Protection

* Install surge protection for the PoE power supply.
* Use a UPS or surge-protected power strip for networking equipment.

### Monitoring

* Learn the normal LED behavior of the CPE.
* Record the management IP address and credentials securely.
* Keep a basic Ethernet cable tester available if possible.

---

## Final Conclusion

This incident evolved from what initially appeared to be a simple **“Wi-Fi not working”** complaint into a multi-layer wireless infrastructure troubleshooting exercise involving **CPE hardware, PoE power delivery, Ethernet connectivity, static IP networking, ICMP diagnostics, and wireless bridge configuration**.

The final resolution was achieved through **replacement of the faulty rooftop CPE and re-establishment of the wireless bridge connection using the airOS management interface**.

Beyond restoring internet access, the investigation provided valuable hands-on understanding of how wireless ISP networks operate and demonstrated the importance of systematic troubleshooting across the lower layers of the OSI model before making assumptions about software or router configuration failures.

---

## Repository Placement

This document is intended to be stored at:

```text
Cybersecurity-Journey/
└── writeups/
    └── dreamtel-wireless-outage.md
```
