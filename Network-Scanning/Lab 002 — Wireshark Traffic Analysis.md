
# Lab 002: Wireshark Traffic Analysis

## 1. Lab Overview

**Project:** Cybersecurity Practical Labs  
**Lab Number:** 002  
**Lab Title:** Wireshark Traffic Analysis  
**Category:** Network Security / SOC Analysis  
**Tool:** Wireshark  
**Operating System:** Windows 10 Pro  
**Status:** In Progress — packet counts pending verification

## 2. Objective

The objective of this lab was to capture and analyze network traffic using Wireshark, identify common network protocols, inspect DNS queries and responses, examine TCP connections, and identify TLS-encrypted application traffic.

This exercise simulates basic network traffic investigation performed by a Security Operations Center (SOC) analyst.

## 3. Tools and Technologies

- Wireshark — packet capture and protocol analysis
- Windows PowerShell — network testing
- `ipconfig` — network configuration inspection
- `nslookup` — DNS lookup and reverse DNS investigation
- `curl.exe` — HTTP/HTTPS request testing
- `ping` — ICMP connectivity testing

## 4. Initial Network Configuration

The initial `ipconfig` output showed the following Wi-Fi configuration:

| Parameter | Observed Value |
|---|---|
| Network interface | Wi-Fi |
| IPv4 address | 192.168.100.31 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 192.168.100.1 |
| Configured DNS server | 192.168.100.1 |

The output also showed additional Ethernet interfaces, including disconnected interfaces and interfaces with other IPv4 addresses.

**Observation:** The Wi-Fi interface was the appropriate initial interface to investigate because it had an IPv4 address and a default gateway.

Note: A later Wireshark packet showed the source IP address `192.168.0.126`. This differs from the initial `ipconfig` result and should be verified against the interface and network configuration used during that capture.

## 5. Methodology

The following steps were used during the investigation:

1. Inspected the Windows network configuration.
2. Performed DNS lookups.
3. Tested HTTP and HTTPS connectivity.
4. Tested local gateway connectivity using ICMP.
5. Captured network traffic using Wireshark.
6. Inspected DNS response records.
7. Examined TCP and TLS packet details.
8. Recorded observed network endpoints and protocol information.

## 6. Commands Executed

### 6.1 Inspect network configuration

```powershell
ipconfig
```

Purpose: Identify network interfaces, IPv4 addresses, subnet masks, and the default gateway.

### 6.2 Perform DNS lookups

```powershell
nslookup 192.168.100.31
nslookup 192.168.100.1
nslookup 142.250.151.100
nslookup 104.18.32.47
nslookup 95.100.104.4
```

Purpose: Investigate reverse DNS records associated with the tested IP addresses.

Some addresses returned hostnames, while others did not return reverse DNS records. A failed reverse lookup does not by itself indicate a security issue.

### 6.3 Test an HTTP endpoint

```powershell
curl.exe -I 95.100.104.4
```

Observed response:

```text
HTTP/1.0 400 Bad Request
Server: AkamaiGHost
```

Interpretation: The server returned an HTTP 400 response. The precise cause was not established by this test.

### 6.4 Test the local gateway

```powershell
curl.exe -I http://192.168.100.1/
```

Observed response:

```text
HTTP/1.1 200 OK
Content-Type:text/html; charset=UTF-8
```

Interpretation: The local gateway returned a successful HTTP response.

Security consideration: Check whether the router supports HTTPS for its administration interface. Avoid submitting credentials over unencrypted HTTP.

### 6.5 Test gateway connectivity

```powershell
ping 192.168.100.1
```

Observed results:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Minimum = 3ms, Maximum = 35ms, Average = 11ms
```

Interpretation: The gateway responded to all four ICMP echo requests during this test.

## 7. DNS Traffic Analysis

### 7.1 DNS query

The queried domain was:

```text
example.com
```

### 7.2 DNS response addresses

The DNS response contained the following addresses:

| Record Type | IP Address |
|---|---|
| AAAA (IPv6) | 2606:4700:10::6814:179a |
| AAAA (IPv6) | 2606:4700:10::ac42:93f3 |
| A (IPv4) | 104.20.23.154 |
| A (IPv4) | 172.66.147.243 |

### 7.3 DNS interpretation

The DNS response returned two IPv6 addresses and two IPv4 addresses for `example.com`.

This demonstrates successful DNS resolution for the observed response.

The following Wireshark display filter was used:

```text
dns
```

**DNS packet count:** Pending verification.

## 8. TCP Traffic Analysis

A TCP packet was inspected in the Wireshark packet details pane.

### 8.1 Observed packet details

| Field | Observed Value |
|---|---|
| Source IP address | 192.168.0.126 |
| Destination IP address | 172.64.148.235 |
| Source port | 1992 |
| Destination port | 443 |
| TCP flags | PSH, ACK |
| TCP payload length | 28 bytes |
| Packet number shown in details | 363 |

The selected packet was associated with TLS application data.

### 8.2 TCP interpretation

Destination port 443 is commonly used for HTTPS traffic.

The PSH and ACK flags indicate that the TCP segment carries data with an acknowledgment. These flags are ordinary TCP behavior and do not independently indicate malicious activity.

The TCP display filter used for this investigation was:

```text
tcp
```

**TCP packet count:** Pending verification.

The packet number and TCP-filtered packet count are different measurements. The value `363` identifies a packet in the capture; it is not the total TCP packet count.

## 9. TLS Traffic Analysis

Wireshark displayed the following protocol information in the selected packet:

```text
Transport Layer Security
Application Data
Encrypted Application Data
```

The packet details also showed TLS-related version information, including a TLS 1.2 version field within the decoded record.

### 9.1 TLS interpretation

The capture contained TLS application data. TLS encrypts application data in transit, so the payload shown in the packet details was not directly readable as ordinary HTTP content.

The presence of TLS traffic is consistent with encrypted network communication. It does not, by itself, prove that the destination or connection is trustworthy.

The following display filter was used:

```text
tls
```

**TLS packet count:** Pending verification.

## 10. Findings Summary

| Finding | Result | Assessment |
|---|---|---|
| Local gateway connectivity | 4 replies, 0% loss | Successful during test |
| DNS resolution | Four addresses observed for example.com | Successful resolution |
| Public HTTP test | HTTP 400 response | Cause undetermined |
| Gateway HTTP test | HTTP 200 response | Gateway returned HTML |
| TCP communication | TCP packet inspected | TCP traffic confirmed |
| TLS application data | Visible in packet details | Encrypted traffic confirmed |
| DNS packet count | Not recorded | Requires verification |
| TCP packet count | Not recorded | Requires verification |
| TLS packet count | Not recorded | Requires verification |

## 11. Security Assessment

The observed DNS, TCP, and TLS activity is consistent with ordinary network communication.

No confirmed malicious activity was established from the evidence collected in this lab.

The HTTP response from the public server and the local gateway's HTTP response warrant contextual interpretation rather than automatic classification as security incidents.

Further investigation could include:

- Reviewing the destination domain and IP reputation using trusted sources.
- Checking the process responsible for a connection.
- Investigating unexpected ports or repeated connection attempts.
- Reviewing DNS queries for suspicious or unusual domain names.
- Confirming whether router administration is protected by HTTPS.
- Correlating packet timestamps with endpoint logs.

These are potential follow-up activities, not findings that have already been confirmed.

## 12. Limitations

1. The DNS, TCP, and TLS filtered packet counts have not yet been recorded.
2. The precise cause of the HTTP 400 response was not determined.
3. The source address in the Wireshark screenshot differs from the initial Wi-Fi IPv4 address, so the capture interface and network configuration should be confirmed.
4. The capture has not yet been independently reviewed for suspicious traffic.
5. The saved PCAPNG file and screenshot evidence should be verified before publishing the final repository.

## 13. Lessons Learned

Through this exercise, I practiced:

- Inspecting Windows network configuration.
- Performing DNS and reverse DNS lookups.
- Testing network connectivity with ICMP.
- Generating HTTP and HTTPS traffic.
- Using Wireshark display filters.
- Identifying TCP source and destination ports.
- Inspecting TCP flags and payload metadata.
- Recognizing TLS-encrypted application data.
- Distinguishing observed evidence from assumptions in a security report.

## 14. Evidence and Deliverables

Recommended repository contents:

```text
lab-002-wireshark-traffic-analysis/
├── README.md
├── screenshots/
│   ├── dns-analysis.png
│   ├── tcp-analysis.png
│   └── tls-analysis.png
└── evidence/
    └── lab002-traffic.pcapng
```

Only add files that have actually been created.

Before publishing the PCAPNG file, inspect it for sensitive information. Prefer publishing sanitized screenshots and the written report if the capture contains private network details or information belonging to other users.

## 15. Conclusion

This lab provided practical experience in network traffic analysis using Wireshark and Windows networking tools.

The investigation successfully identified DNS responses for `example.com`, confirmed local gateway connectivity, and examined TCP traffic carrying TLS application data.

The results demonstrate foundational packet-analysis skills relevant to entry-level SOC analyst work. Final packet counts and evidence-file verification remain outstanding.

**Project:** Cybersecurity Practical Labs  
**Lab:** 002 — Wireshark Traffic Analysis  
**Status:** In Progress — pending final evidence verification
