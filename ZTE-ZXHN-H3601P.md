# ZTE ZXHN H3601P - Unauthenticated DNS Zone Transfer (AXFR) & Misconfiguration

- **Exploit Title:** ZTE ZXHN H3601P - Unauthenticated DNS Zone Transfer (AXFR)
- **Date:** 2025-12-24
- **Exploit Author:** Hazar Taşpınar
- **Vendor Homepage:** https://www.zte.com.cn/
- **Product Name:** ZTE ZXHN H3601P V9.0
- **Firmware Version:** V9.0.0P7_TN2
- **Hardware Version:** V9.0.7
- **Bootloader Version:** V1.0.0
- **Affected Component:** DNS Service (DNS Daemon / ISC BIND Component)
- **Severity:** High (CVSS 3.1 Base Score: 7.5)
- **CVE:** Waiting for assignment (Submitted to MITRE)
- **References:**
  - https://cwe.mitre.org/data/definitions/200.html
  - https://cwe.mitre.org/data/definitions/284.html
  - https://cwe.mitre.org/data/definitions/16.html

---

### Description
A misconfiguration vulnerability exists in the DNS service of the **ZTE ZXHN H3601P V9.0** router. The device improperly exposes **DNS Zone Transfers (AXFR)** without any authentication on the LAN interface and simultaneously operates in a mixed mode:
1.  As an **authoritative DNS server** for root zone data.
2.  As a **recursive resolver** for external queries.

The embedded DNS daemon (detected as ISC BIND) is configured with:
- **No ACL** to restrict AXFR requests.
- **Authoritative root zone data** preloaded.
- **Recursion enabled** for unauthenticated clients.

This combination violates standard DNS security practices and leads to unauthorized exposure of root DNS records, internal configuration leakage, and potential misuse for DNS amplification attacks.

---

### Proof of Concept (PoC)

The vulnerability requires no authentication and can be exploited by any attacker with LAN access (wired or wireless).

#### Step 1: Identify DNS Service
Scanning the gateway IP reveals the open DNS port running ISC BIND.

```bash
nmap -sV -p 53 192.168.1.1
```
<img width="481" height="179" alt="image" src="https://github.com/user-attachments/assets/d03ac87d-57bd-4bb7-93dd-2fc4e762ed31" />

#### Step 2: Attempt Zone Transfer (AXFR) - The Exploit
Requesting a full zone transfer succeeds and leaks the Root Zone configuration.

```Bash
dig axfr @192.168.1.1
```
<img width="724" height="790" alt="image" src="https://github.com/user-attachments/assets/e9e1569b-f27f-45c5-83ea-3f15c99c37d8" />
<img width="718" height="164" alt="image" src="https://github.com/user-attachments/assets/7d0ecb95-a969-4a7f-954d-fb7503bb0d64" />

Observation: The router returns full root zone records (A, AAAA, and NS records for all root nameservers), confirming it treats itself as authoritative for the root zone.

#### Step 3: Verify Recursion
Checking if the router also resolves external domains (Mixed Mode).

```Bash
dig @192.168.1.1 google.com
```
<img width="724" height="285" alt="image" src="https://github.com/user-attachments/assets/4d92a695-b823-4e9b-bbe6-b11067e6072f" />

Observation: The router successfully resolves external domains, confirming recursion is enabled alongside the authoritative misconfiguration.

#### Step 4: Test Non-Existent Domain Handling
```Bash
dig @192.168.1.1 randomstring.local
```
<img width="724" height="325" alt="image" src="https://github.com/user-attachments/assets/9d8cd487-ac2b-4b33-b178-35478b5e7f9b" />


### Attack Scenarios & Impact
An attacker connected to the LAN can exploit this vulnerability to:

- Reconnaissance: Perform unauthorized AXFR requests to obtain root DNS zone data and identify internal DNS configuration inconsistencies.
- DNS Spoofing: Exploit the mixed authoritative/recursive resolver behavior to potentially poison the cache.
- DoS / Amplification: Leverage the open recursive resolver configuration to perform DNS amplification attacks within the LAN or exhaust device resources.
- Information Disclosure: Leakage of internal network topology or device hostnames via the exposed DNS records (CWE-200).

### Mitigation Suggestions
The following actions are recommended for the vendor/ISP to fix the firmware:

- Disable AXFR: Strictly disable Zone Transfers on all LAN/WAN interfaces.
- Implement ACLs: Apply Access Control Lists to restrict critical DNS operations to trusted internal processes only.
- Remove Root Zone: Remove authoritative root zone records from the firmware configuration; the device should act purely as a forwarding/recursive resolver.
- Review Configuration: Audit the embedded DNS daemon (BIND) configuration for security best practices.

### Vendor Response
The vendor was contacted regarding this vulnerability. They acknowledged the behavior but refused to classify it as a security issue or issue a fix, stating it is a configuration requested by the ISP.
