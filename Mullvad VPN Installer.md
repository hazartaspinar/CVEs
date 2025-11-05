# Mullvad VPN - Weak SHA1 Hash in Certificate Chain
+ *Exploit Title:* Mullvad VPN - Weak SHA1 Hash in Certificate Chain
+ *Date:* 2025-11-05
+ *Exploit Author:* Hazar Taşpınar
+ *Vendor Homepage:* https://mullvad.net/
+ *Software Link:* https://mullvad.net/en/download/
+ *Version:* 1.2.0
+ *Tested on:* Windows 11 Professional
+ *CVE:* Reported, Waiting for CVE number

## References: 
+ https://shattered.io/
+ https://csrc.nist.gov/pubs/sp/800/131/a/r2/final

## Description:
Mullvad VPN installer 1.2.0 uses a certificate chain that contains the cryptographically broken SHA1 hash algorithm in the DigiCert root certificate (Algorithm: sha1RSA), making it vulnerable to collision attacks and potential certificate forgery.

## Proof of Concept:
+ Download Mullvad VPN installer from official website
+ Run sigcheck analysis tool on the installer
+ Check certificate chain for weak hash algorithms

### SigCheck Command:
sigcheck -i "Install Mullvad VPN.exe"

### Expected Vulnerable Output:
<img width="1095" height="939" alt="image" src="https://github.com/user-attachments/assets/195fb460-87a9-413e-9a55-77cda0bc970e" />
