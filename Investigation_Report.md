# Digital Forensic Investigation Report

---

## Case Title  
Suspicious Network Activity – Potential Data Exfiltration

## Investigator  
Duhaim B. (Cybersecurity Analyst)

## Date  
2025-05-29

---

## 1. Introduction

On May 27, 2025, the IT Security Department received an alert from the organization's intrusion detection system (IDS) indicating unusual outbound network traffic originating from a workstation belonging to a finance department employee. The alert suggested potential data exfiltration activity due to large file transfers over non-standard ports.

A packet capture (pcap) file was generated for the timeframe in question, and a forensic analysis was initiated to determine whether sensitive data had been transmitted outside the corporate network without authorization.

The purpose of this report is to document the investigation process, findings, and recommendations based on the analysis of the captured network traffic.

---

## 2. Scope of the Investigation

This investigation is limited to the analysis of:  
- Network traffic logs captured in the file `suspect_traffic_27may2025.pcap`  
- Host IP: `192.168.10.24`  
- Timeframe: From `12:15 PM` to `12:35 PM` on May 27, 2025

The investigation **does not** include:  
- Deep analysis of the endpoint device  
- Disk imaging or memory capture of the host  
- Attribution to a specific attacker  

---

## 3. Tools Used

| Tool           | Purpose                                  |
| -------------- | -------------------------------------- |
| Wireshark      | Packet-level network traffic analysis  |
| Enmap (Nmap GUI) | Scanning for open ports and services  |
| VirusTotal     | Checking hashes and file artifacts      |
| Strings / binwalk | Extracting readable data from binaries |
| Notepad++ / Hex editor | Manual inspection of payloads      |
| SHA256 calculator | Hash verification for integrity       |

---

## 4. Investigation Steps

### 4.1 Initial Inspection  
- Loaded `.pcap` file into **Wireshark**.  
- Applied filters to isolate traffic from `192.168.10.24`.  
- Verified timeframe matched alert window (12:15 PM to 12:35 PM).

### 4.2 Protocol Filtering  
- Protocol hierarchy analysis revealed DNS, HTTP, and FTP (unusual).  
- Applied filter: `ftp || ftp-data`.

### 4.3 FTP Session Analysis  
- FTP connection from `192.168.10.24` to `45.155.204.9` on port 21.  
- Credentials sent in plaintext: `USER finance_backup` / `PASS temp123!`.  
- File `finance_report_q1.pdf` uploaded using `STOR` command.

### 4.4 File Reconstruction  
- Extracted FTP data stream and saved file locally.  
- SHA256 hash: `8fbc44d123d01f219ef322ac97f3d9e4e36c68d3c2c519a8afcfae20bbf24b1a`.  
- File is a password-protected PDF, recently modified before upload.

### 4.5 DNS & IP Analysis  
- Reverse DNS on `45.155.204.9` showed no known domain.  
- IP registered to a hosting provider in Eastern Europe, linked to malicious activity.  
- No legitimate business relation with this IP.

---

## 5. Findings

### 5.1 Unauthorized FTP Usage  
- FTP session initiated from internal host to untrusted external IP.  
- FTP is not authorized by organizational policy.

### 5.2 Data Exfiltration Attempt  
- Credentials transmitted in plaintext.  
- Confidential file uploaded to external server.

### 5.3 Sensitive File Characteristics  
- Password-protected PDF, likely confidential financial data.  
- File modified shortly before upload.

### 5.4 Untrusted External IP  
- External IP flagged for malicious hosting activity.

### 5.5 Weak Security Controls  
- Plaintext FTP credentials.  
- No outbound port restrictions on workstation.  
- No endpoint alert triggered.

---

## 6. Conclusion and Recommendations

### Conclusion  
Confirmed data exfiltration via unauthorized FTP from internal host. Weak security controls enabled the incident.

### Recommendations  
1. Enforce network segmentation and restrict outbound FTP traffic.  
2. Use encrypted transfer protocols like SFTP or FTPS.  
3. Deploy endpoint detection and response (EDR) tools.  
4. Conduct regular security awareness training.  
5. Perform periodic audits and log reviews.

---

**Final Note:**  
Continuous monitoring and proactive security are vital to prevent future incidents.

---

*Prepared by:*  
Duhaim B.  
Cybersecurity Analyst  
2025-05-29
