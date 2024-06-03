# 802.1.x-Security-Vulnerabilities
The IEEE 802.1X is a standard for port-based network access control (PNAC) commonly used in enterprise Wi-Fi and wired Ethernet networks to provide strong authentication, authorization, and accounting (AAA). Despite its security benefits, 802.1X has several vulnerabilities and attack vectors


| Attack Vector               | Test Case                  | Test Steps                                                                                   |
|-----------------------------|----------------------------|---------------------------------------------------------------------------------------------|
| 1. MitM - Rogue APs         | Set up a rogue AP          | 1. Configure a laptop as a rogue AP with hostapd                                              |
|                             |                            | 2. Clone SSID and BSSID of a legitimate AP                                                    |
|                             |                            | 3. Try to intercept 802.1X handshakes                                                         |
| 1. MitM - Evil Twin         | Deploy evil twin AP        | 1. Use EAPHammer to set up an evil twin                                                       |
|                             |                            | 2. Offer stronger signal than legitimate AP                                                   |
|                             |                            | 3. Capture user credentials                                                                   |
| 2. EAP-MD5 Weakness         | Test EAP-MD5 strength      | 1. Set up a network using EAP-MD5                                                             |
|                             |                            | 2. Capture EAP-MD5 challenge-response                                                         |
|                             |                            | 3. Use John the Ripper for offline cracking                                                   |
| 2. LEAP Vulnerability       | Attack Cisco LEAP          | 1. Configure a client to use LEAP                                                             |
|                             |                            | 2. Capture LEAP handshake with Wireshark                                                      |
|                             |                            | 3. Use asleap tool for dictionary attack                                                      |
| 2. EAP-TLS MitM             | Exploit weak TLS           | 1. Set up EAP-TLS without cert validation                                                     |
|                             |                            | 2. Use sslsplit for MitM interception                                                         |
|                             |                            | 3. Verify decrypted traffic access                                                            |
| 3. EAP Identity Leak        | Sniff EAP Identity         | 1. Start 802.1X authentication                                                                |
|                             |                            | 2. Capture EAP-Identity-Response                                                              |
|                             |                            | 3. Check if username is in clear text                                                         |
| 3. IMSI Catching            | Capture IMSI in EAP-SIM    | 1. Configure EAP-SIM on network                                                               |
|                             |                            | 2. Use a fake base station (like YateBTS)                                                     |
|                             |                            | 3. Trigger EAP-SIM and capture IMSI                                                           |
| 4. RADIUS Impersonation     | Fake RADIUS server         | 1. Set up FreeRADIUS with stolen certs                                                        |
|                             |                            | 2. Configure AP to use this server                                                            |
|                             |                            | 3. Verify client connects to fake server                                                      |
| 4. RADIUS DoS               | Flood RADIUS server        | 1. Use RadSpoof or Scapy                                                                      |
|                             |                            | 2. Generate thousands of fake RADIUS requests                                                 |
|                             |                            | 3. Monitor server for slowdown or crash                                                       |
| 5. Weak Supplicant          | Misconfigure client        | 1. Set up client with EAP-MD5                                                                 |
|                             |                            | 2. Disable server cert validation                                                             |
|                             |                            | 3. Test if it connects to rogue AP                                                            |
| 5. Credential Theft         | Plant malware              | 1. Create a trojan with PowerShell                                                            |
|                             |                            | 2. Target `wpa_supplicant.conf` or registry                                                   |
|                             |                            | 3. Exfiltrate saved Wi-Fi credentials                                                         |
| 6. Weak Key Generation      | Test PRF strength          | 1. Use EAP method with weak PRF (e.g., MD5 in LEAP)                                           |
|                             |                            | 2. Capture 4-way handshake                                                                    |
|                             |                            | 3. Analyze key strength with cryptool                                                         |
| 6. KRACK Attack             | Reinstall WPA2 keys        | 1. Set up WPA2-Enterprise network                                                             |
|                             |                            | 2. Use krackattacks-poc-zerokey tool                                                          |
|                             |                            | 3. Force key reinstallation and decrypt                                                       |
| 7. EAPoL Start Flood        | DoS authenticator          | 1. Use Scapy to craft EAPoL-Start frames                                                      |
|                             |                            | 2. Flood AP with these frames                                                                 |
|                             |                            | 3. Check if legitimate users can't connect                                                    |
| 7. MAC Spoofing             | Bypass MAC filtering       | 1. Sniff a valid client's MAC                                                                 |
|                             |                            | 2. Use macchanger to spoof this MAC                                                           |
|                             |                            | 3. Attempt to start 802.1X auth                                                               |
| 8. Session Hijacking        | Takeover session           | 1. Use Wireshark to get session identifiers                                                   |
|                             |                            | 2. Craft packets with Scapy to mimic user                                                     |
|                             |                            | 3. Try to continue the session                                                                |
| 8. VLAN Hopping             | Jump VLANs                 | 1. Auth to a low-privilege VLAN                                                               |
|                             |                            | 2. Use yersinia for double tagging                                                            |
|                             |                            | 3. Attempt to access restricted VLAN                                                          |
| 9. Port Stealing            | Hijack Ethernet port       | 1. Locate an authenticated device                                                             |
|                             |                            | 2. Physically unplug its Ethernet                                                             |
|                             |                            | 3. Plug in attacker's device                                                                  |
| 9. MAC Flooding             | Turn switch to hub         | 1. Use macof to send many MAC addresses                                                       |
|                             |                            | 2. Fill switch's CAM table                                                                    |
|                             |                            | 3. Use Wireshark to see all traffic                                                           |
| 10. Offline Dictionary      | Attack EAP-TTLS            | 1. Set up EAP-TTLS with weak password                                                         |
|                             |                            | 2. Use hcxdumptool to capture handshake                                                       |
|                             |                            | 3. Run hashcat for offline cracking                                                           |
| 10. Downgrade Attack        | Force weaker EAP           | 1. Use hostapd-wpe as rogue AP                                                                |
|                             |                            | 2. Only offer EAP-MD5 to clients                                                              |
|                             |                            | 3. See if clients downgrade                                                                   |
| 11. Buffer Overflow         | Exploit EAP code           | 1. Find open-source EAP supplicant                                                            |
|                             |                            | 2. Fuzz it with AFL or Boofuzz                                                                |
|                             |                            | 3. Look for crashes indicating overflow                                                       |
| 11. State Machine Bug       | Confuse 802.1X states      | 1. Use Scapy to craft invalid state transitions                                               |
|                             |                            | 2. Send logoff during negotiation                                                             |
|                             |                            | 3. Check for erratic AP behavior                                                              |
| 12. Phishing                | Fake captive portal        | 1. Set up a fake AP without 802.1X                                                            |
|                             |                            | 2. Redirect to phishing page                                                                  |
|                             |                            | 3. Collect entered credentials                                                                |
| 12. Shoulder Surfing        | Observe password entry     | 1. Position near victim in public space                                                       |
|                             |                            | 2. Watch as they type 802.1X password                                                         |
|                             |                            | 3. Note down the password                                                                     |
| 13. EAPoL-Logoff Flood      | Mass deauthentication      | 1. Use MDK3 or custom script                                                                  |
|                             |                            | 2. Send EAPoL-Logoff to all clients                                                           |
|                             |                            | 3. Verify they're disconnected                                                                |
| 13. Resource Exhaustion     | Overwhelm resources        | 1. Use Metasploit's auxiliary/dos/radius                                                      |
|                             |                            | 2. Target RADIUS server or switch                                                             |
|                             |                            | 3. Monitor CPU and memory usage                                                               |
| 14. Advanced Evil Twin      | 802.1X-capable twin        | 1. Use hostapd-wpe for full EAP support                                                       |
|                             |                            | 2. Clone all settings including CA certs                                                      |
|                             |                            | 3. Try to intercept full session                                                              |
| 14. Rogue Authenticators    | Fake network devices       | 1. Configure a device as 802.1X authenticator                                                 |
|                             |                            | 2. Make it look like a legitimate switch                                                      |
|                             |                            | 3. See if clients attempt to auth                                                             |
| 15. Timing Attack           | Time password checks       | 1. Modify wpa_supplicant for timing info                                                      |
|                             |                            | 2. Send many EAP-Response/Identity                                                            |
|                             |                            | 3. Analyze response times for leaks                                                           |
| 15. Power Analysis          | Attack EAP-TLS smartcard   | 1. Set up EAP-TLS with smartcard                                                              |
|                             |                            | 2. Use ChipWhisperer for power traces                                                         |
|                             |                            | 3. Try to extract private key                                                                 |
