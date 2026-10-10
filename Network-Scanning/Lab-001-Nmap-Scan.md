# Lab 001: Localhost Network Scanning with Nmap

## 1. Lab Information
Date: 10 October 2026
Analyst: Cybersecurity Lab Trainee
Target: 127.0.0.1 (localhost)
Operating System: Microsoft Windows
Tool: Nmap 7.991
Scan Type: TCP service/version detection

## 2. Objective
Identify open TCP ports and attempt to identify services
running on my own Windows computer.

## 3. Command Executed
nmap -sV 127.0.0.1 -oN C:\Cybersecurity-Labs\Network-Scanning\scan-report.txt

## 4. Scan Summary
Host status: Up
Reported latency: 0.00053 seconds
Open TCP ports: 5
Closed TCP ports: 995
Ports covered: 1000 commonly scanned TCP ports
Scan duration: 15.34 seconds

## 5. Findings

### Port 135/TCP
Service: Microsoft Windows RPC (msrpc)
Observation: A Windows RPC service is listening.
Assessment: Verify that firewall rules restrict access
to trusted systems and networks where appropriate.

### Port 445/TCP
Service: Microsoft-DS (not conclusively identified)
Observation: Commonly associated with SMB file sharing.
Assessment: Review SMB usage, sharing permissions,
and Windows Firewall rules.

### Port 1234/TCP
Service: Unknown (Nmap reported hotline?)
Observation: The service returned a JSON error response.
Assessment: Identify the process listening on this port
and determine whether it is an expected application.

### Port 3389/TCP
Service: Microsoft Terminal Services
Observation: Commonly associated with Remote Desktop Protocol.
Assessment: Verify whether Remote Desktop is required
and ensure access is appropriately restricted.

### Port 5357/TCP
Service: Microsoft HTTPAPI httpd 2.0
Observation: A Windows HTTP service endpoint responded.
Assessment: Identify its purpose and verify firewall rules.

## 6. Security Analysis
The scan identified five open TCP ports on localhost.
Open ports are not, by themselves, proof of vulnerabilities
or compromise.

The service on port 1234 requires further identification.
The scan does not establish whether these services are
reachable from other computers or the internet.

## 7. Evidence
Raw scan output: scan-report.txt
Target: 127.0.0.1
Nmap version: 7.991

## 8. Recommendations
1. Identify the application listening on TCP port 1234.
2. Review Remote Desktop configuration and access controls.
3. Review SMB sharing and firewall restrictions.
4. Confirm whether RPC and HTTP-related services are required.
5. Repeat testing only against authorized systems.
6. Record follow-up actions and verification results.

## 9. Conclusion
The Nmap scan completed successfully and identified five
open TCP ports on the local computer. Further investigation
is required before determining whether any security issue exists.

## 10. Next Steps
Identify the process listening on port 1234.
Review the Windows Remote Desktop and SMB configurations.
Compare findings after any approved security changes.

## 13. File Metadata and Hash Analysis

Executable:
C:\ProgramData\ManyCam\Service\ManyCamService.exe

Company: Visicom Media Inc.
Product: ManyCam Virtual Webcam
Description: ManyCam Service
File version: 1.0.0.3
File size: 544984 bytes
Creation time: 2016-03-31 13:03:02
Last modified: 2016-03-31 13:03:02

SHA-256:
F2CE20E7019C029388A24326B149B0FBF17649F2BA805C96E9F6FB27EB4CB3CD

Assessment:
The executable metadata is consistent with ManyCam software.
Its signature was reported as valid by Windows. The old
file date and version warrant checking whether the software
is still required and supported. No compromise has been
established by these checks.

Recommended actions:
- Confirm whether ManyCam is needed.
- Check for software updates from the official publisher.
- Compare the hash with a trusted reference if available.
- Recheck port 1234 after any approved software changes.

Status: Initial investigation documented; final disposition pending.
