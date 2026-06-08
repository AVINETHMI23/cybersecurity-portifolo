# Vulnerability Assessment & Penetration Testing (VAPT) — Mayo Industries Simulation

## 📋 Overview
A comprehensive offensive and defensive security assessment modeled around a simulated production environment ("Mayo Industries" Mayonnaise Factory). The project establishes structural visibility across corporate network boundaries down to critical operational technology floor controls.

## ⚔️ Technical Implementation & Methodology
* **Offensive Reconnaissance (Red Team):** Executed advanced port scanning, OS fingerprinting, and service enumeration using aggressive Nmap configurations targeting host boundaries (`nmap -A 192.168.216.6`). Evaluated perimeter entry vectors, weak corporate authentication mechanisms, and privilege escalation pathways.
* **Defensive Analytics (Blue Team):** Conducted passive and active packet monitoring using Wireshark network traffic captures. Analyzed indicators of compromise (IoCs), lateral movement, unauthorized ICMP polling bursts, and abnormal TCP Retransmissions originating from office networks down to factory mixing machine controllers.

## 📂 Deliverables
* [Download VAPT Pentest Report File](./IT23545212_AIA__Assignment__2.pdf)
