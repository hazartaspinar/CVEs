# Mullvad VPN - Weak SHA1 Hash in Certificate Chain Vulnerability

## Vulnerability Details
- **Vulnerability Title**: Weak SHA1 Hash Algorithm in Code Signing Certificate Chain
- **Date**: 2025-11-05  
- **Discoverer**: Hazar Taşpınar
- **Vendor**: Mullvad VPN AB
- **Product**: Mullvad VPN Installer
- **Version**: 1.2.0
- **CVE**: Reported, Waiting for CVE number

## Vulnerability Description
Mullvad VPN installer version 1.2.0 uses a certificate chain that contains the cryptographically broken SHA1 hash algorithm in the DigiCert root certificate (Algorithm: sha1RSA). SHA1 is vulnerable to collision attacks, which could allow attackers to create malicious certificates and bypass code signing validation.

## Affected Component
- Code signing certificate chain
- DigiCert root certificate (Thumbprint: 0563B8630D62D75ABBC8AB1E4BDFB5A899B24D43)

## Proof of Concept

### Detection Method
```bash
# Using SigCheck to verify the vulnerability
sigcheck -i "Install Mullvad VPN.exe"
