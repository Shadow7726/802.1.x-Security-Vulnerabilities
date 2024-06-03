# 802.1.x-Security-Vulnerabilities
The IEEE 802.1X is a standard for port-based network access control (PNAC) commonly used in enterprise Wi-Fi and wired Ethernet networks to provide strong authentication, authorization, and accounting (AAA). Despite its security benefits, 802.1X has several vulnerabilities and attack vectors


| Attack Vector               | Test Case                  | Test Steps                                                                                   | Description                                                                                     |
|-----------------------------|----------------------------|---------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| 1. MitM - Rogue APs         | Set up a rogue AP          | 1. Configure a laptop as a rogue AP with hostapd                                             |
|                             |                            | 2. Clone SSID and BSSID of a legitimate AP                                                   |
|                             |                            | 3. Try to intercept 802.1X handshakes                                                        | Rogue APs are unauthorized access points set up to mimic legitimate APs to intercept network traffic. |
| 1. MitM - Evil Twin         | Deploy evil twin AP        | 1. Use EAPHammer to set up an evil twin                                                      |
|                             |                            | 2. Offer stronger signal than legitimate AP                                                  |
|                             |                            | 3. Capture user credentials                                                                  | Evil twin APs are malicious APs that trick users into connecting by presenting a stronger signal. |
| 2. EAP-MD5 Weakness         | Test EAP-MD5 strength      | 1. Set up a network using EAP-MD5                                                            |
|                             |                            | 2. Capture EAP-MD5 challenge-response                                                        |
|                             |                            | 3. Use John the Ripper for offline cracking                                                  | EAP-MD5 is susceptible to offline dictionary attacks due to its weak cryptographic properties. |
| 2. LEAP Vulnerability       | Attack Cisco LEAP          | 1. Configure a client to use LEAP                                                            |
|                             |                            | 2. Capture LEAP handshake with Wireshark                                                     |
|                             |                            | 3. Use asleap tool for dictionary attack                                                     | Cisco LEAP has known vulnerabilities, making it easy to crack using dictionary attacks. |
| 2. EAP-TLS MitM             | Exploit weak TLS           | 1. Set up EAP-TLS without cert validation                                                    |
|                             |                            | 2. Use sslsplit for MitM interception                                                        |
|                             |                            | 3. Verify decrypted traffic access                                                           | Weak EAP-TLS configurations can be exploited if certificate validation is not enforced. |
| 3. EAP Identity Leak        | Sniff EAP Identity         | 1. Start 802.1X authentication                                                               |
|                             |                            | 2. Capture EAP-Identity-Response                                                             |
|                             |                            | 3. Check if username is in clear text                                                        | EAP identity frames can reveal usernames in plaintext, potentially compromising user privacy. |
| 3. IMSI Catching            | Capture IMSI in EAP-SIM    | 1. Configure EAP-SIM on network                                                              |
|                             |                            | 2. Use a fake base station (like YateBTS)                                                    |
|                             |                            | 3. Trigger EAP-SIM and capture IMSI                                                          | IMSI catching involves capturing International Mobile Subscriber Identity during EAP-SIM authentication. |
| 4. RADIUS Impersonation     | Fake RADIUS server         | 1. Set up FreeRADIUS with stolen certs                                                       |
|                             |                            | 2. Configure AP to use this server                                                           |
|                             |                            | 3. Verify client connects to fake server                                                     | A fake RADIUS server can be set up to impersonate a legitimate one and intercept authentication data. |
| 4. RADIUS DoS               | Flood RADIUS server        | 1. Use RadSpoof or Scapy                                                                     |
|                             |                            | 2. Generate thousands of fake RADIUS requests                                                |
|                             |                            | 3. Monitor server for slowdown or crash                                                      | Denial of Service (DoS) attacks on RADIUS servers can disrupt authentication services by overwhelming them with fake requests. |
| 5. Weak Supplicant          | Misconfigure client        | 1. Set up client with EAP-MD5                                                                |
|                             |                            | 2. Disable server cert validation                                                            |
|                             |                            | 3. Test if it connects to rogue AP                                                           | Misconfigured supplicants can easily be exploited, especially if they don't validate server certificates. |
| 5. Credential Theft         | Plant malware              | 1. Create a trojan with PowerShell                                                           |
|                             |                            | 2. Target `wpa_supplicant.conf` or registry                                                  |
|                             |                            | 3. Exfiltrate saved Wi-Fi credentials                                                        | Malware can be used to steal saved Wi-Fi credentials from a client's device. |
| 6. Weak Key Generation      | Test PRF strength          | 1. Use EAP method with weak PRF (e.g., MD5 in LEAP)                                          |
|                             |                            | 2. Capture 4-way handshake                                                                   |
|                             |                            | 3. Analyze key strength with cryptool                                                        | Weak Pseudo-Random Function (PRF) implementations can lead to weak cryptographic keys. |
| 6. KRACK Attack             | Reinstall WPA2 keys        | 1. Set up WPA2-Enterprise network                                                            |
|                             |                            | 2. Use krackattacks-poc-zerokey tool                                                         |
|                             |                            | 3. Force key reinstallation and decrypt                                                      | The KRACK attack targets WPA2 protocol weaknesses to reinstall previously used keys, enabling decryption of traffic. |
| 7. EAPoL Start Flood        | DoS authenticator          | 1. Use Scapy to craft EAPoL-Start frames                                                     |
|                             |                            | 2. Flood AP with these frames                                                                |
|                             |                            | 3. Check if legitimate users can't connect                                                   | Flooding an AP with EAPoL-Start frames can prevent legitimate users from authenticating. |
| 7. MAC Spoofing             | Bypass MAC filtering       | 1. Sniff a valid client's MAC                                                                |
|                             |                            | 2. Use macchanger to spoof this MAC                                                          |
|                             |                            | 3. Attempt to start 802.1X auth                                                              | MAC spoofing involves changing the MAC address of an attacker’s device to bypass MAC filtering. |
| 8. Session Hijacking        | Takeover session           | 1. Use Wireshark to get session identifiers                                                  |
|                             |                            | 2. Craft packets with Scapy to mimic user                                                    |
|                             |                            | 3. Try to continue the session                                                               | Session hijacking allows attackers to take over an active session by mimicking the user's session identifiers. |
| 8. VLAN Hopping             | Jump VLANs                 | 1. Auth to a low-privilege VLAN                                                              |
|                             |                            | 2. Use yersinia for double tagging                                                           |
|                             |                            | 3. Attempt to access restricted VLAN                                                         | VLAN hopping attacks exploit vulnerabilities to access different VLANs beyond the one assigned. |
| 9. Port Stealing            | Hijack Ethernet port       | 1. Locate an authenticated device                                                            |
|                             |                            | 2. Physically unplug its Ethernet                                                            |
|                             |                            | 3. Plug in attacker's device                                                                 | Port stealing involves physically taking over a network port by disconnecting a legitimate device and connecting an attacker's device. |
| 9. MAC Flooding             | Turn switch to hub         | 1. Use macof to send many MAC addresses                                                      |
|                             |                            | 2. Fill switch's CAM table                                                                   |
|                             |                            | 3. Use Wireshark to see all traffic                                                          | MAC flooding attacks overload a switch's CAM table, forcing it to act like a hub and broadcast all traffic. |
| 10. Offline Dictionary      | Attack EAP-TTLS            | 1. Set up EAP-TTLS with weak password                                                        |
|                             |                            | 2. Use hcxdumptool to capture handshake                                                      |
|                             |                            | 3. Run hashcat for offline cracking                                                          | Offline dictionary attacks involve capturing authentication handshakes and cracking them offline using password dictionaries. |
| 10. Downgrade Attack        | Force weaker EAP           | 1. Use hostapd-wpe as rogue AP                                                               |
|                             |                            | 2. Only offer EAP-MD5 to clients                                                             |
|                             |                            | 3. See if clients downgrade                                                                  | Downgrade attacks force clients to use a less secure EAP method by only offering the weaker option. |
| 11. Buffer Overflow         | Exploit EAP code           | 1. Find open-source EAP supplicant                                                           |
|                             |                            | 2. Fuzz it with AFL or Boofuzz                                                               |
|                             |                            | 3. Look for crashes indicating overflow                                                      | Buffer overflow vulnerabilities in EAP implementations can be exploited to execute arbitrary code. |
| 11. State Machine Bug       | Confuse 802.1X states      | 1. Use Scapy to craft invalid state transitions                                              |
|                             |                            | 2. Send logoff during negotiation                                                            |
|                             |                            | 3. Check for erratic AP behavior                                                             | State machine bugs exploit weaknesses in the state handling logic of 802.1X implementations. |
| 12. Phishing                | Fake captive portal        | 1. Set up a fake AP without 802.1X                                                           |
|                             |                            | 2. Redirect to phishing page                                                                 |
|                             |                            | 3. Collect entered credentials                                                               | Phishing attacks involve setting up fake APs to trick users into entering their credentials on a fake login page. |
| 12. Shoulder

 Surfing        | Observe password entry     | 1. Position near victim in public space                                                      |
|                             |                            | 2. Watch as they type 802.1X password                                                        |
|                             |                            | 3. Note down the password                                                                    | Shoulder surfing involves physically observing a user entering their credentials, typically in a public place. |
| 13. EAPoL-Logoff Flood      | Mass deauthentication      | 1. Use MDK3 or custom script                                                                 |
|                             |                            | 2. Send EAPoL-Logoff to all clients                                                          |
|                             |                            | 3. Verify they're disconnected                                                               | Flooding a network with EAPoL-Logoff frames can force all clients to disconnect, causing a denial of service. |
| 13. Resource Exhaustion     | Overwhelm resources        | 1. Use Metasploit's auxiliary/dos/radius                                                     |
|                             |                            | 2. Target RADIUS server or switch                                                            |
|                             |                            | 3. Monitor CPU and memory usage                                                              | Resource exhaustion attacks overload network devices or servers, rendering them unusable. |
| 14. Advanced Evil Twin      | 802.1X-capable twin        | 1. Use hostapd-wpe for full EAP support                                                      |
|                             |                            | 2. Clone all settings including CA certs                                                     |
|                             |                            | 3. Try to intercept full session                                                             | Advanced evil twin attacks use sophisticated tools to mimic legitimate APs and capture full session data. |
| 14. Rogue Authenticators    | Fake network devices       | 1. Configure a device as 802.1X authenticator                                                |
|                             |                            | 2. Make it look like a legitimate switch                                                     |
|                             |                            | 3. See if clients attempt to auth                                                            | Rogue authenticators are fake network devices that trick clients into authenticating against them. |
| 15. Timing Attack           | Time password checks       | 1. Modify wpa_supplicant for timing info                                                     |
|                             |                            | 2. Send many EAP-Response/Identity                                                           |
|                             |                            | 3. Analyze response times for leaks                                                          | Timing attacks exploit the time differences in processing different inputs to infer sensitive information. |
| 15. Power Analysis          | Attack EAP-TLS smartcard   | 1. Set up EAP-TLS with smartcard                                                             |
|                             | 2. Use ChipWhisperer for power traces                                                             |
|                             | 3. Try to extract private key                                                                    | Power analysis attacks involve measuring power consumption of a device to extract cryptographic keys. |
