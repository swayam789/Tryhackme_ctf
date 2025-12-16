# Jack of All Trades — TryHackMe  
### Boot‑to‑Root Challenge (Securi‑Tay 2020)

---

## 📌 Overview
This challenge focused on **web enumeration**, **steganographic data extraction**, **manual HTTP interaction**, and **exploitation of misconfigurations**.  
The objective was to retrieve both **user** and **root** flags **without requiring a full privilege escalation to a root shell**.

---

## 🔍 Enumeration

### Network Scanning
Initial reconnaissance using `nmap` revealed the following open ports:

- **22/tcp — SSH**
- **80/tcp — HTTP**

⚠️ The services were **intentionally swapped**, indicating a misconfiguration designed to mislead automated assumptions.

---

### Web Enumeration
Directory and file enumeration using **ffuf** and **gobuster** identified:

- `index.html`
- `recovery.php`

---

## 🧪 Web Analysis & Data Extraction

### `index.html`
Analysis of `index.html` revealed:
- **Three JPG images**
- A **Base64‑encoded string**

Files were retrieved and analyzed manually using `curl`.

Image analysis results:
- One image acted as a **decoy**
- `header.jpg` contained a **password**
- The password unlocked `jackofalltrades.jpg`, which revealed **`cms.creds`**

---

## ⚙️ Command Execution

### `recovery.php`
Accessing `recovery.php` exposed a vulnerable parameter:

```text
index.php?cmd=
```

---

## 👤 User Access
Using the RCE:
-Navigated to /home
-Discovered jacks_password_list.txt
-Extracted and transferred the password list to the attacking machine
-A brute‑force attack using Hydra against SSH successfully authenticated as the user jack.

## 🚩 Post‑Exploitation
###User Flag:
-Located user.jpg containing the user flag
-Transferred via scp and extracted locally

### Root Flag Access
A SUID enumeration was performed:
```bash
find / -perm -4000 2>/dev/null
```
This revealed a misconfigured binary named strings.

The binary was abused to read sensitive files:

```bash
strings /root/root.txt

````

###Result:

✅ Root flag obtained

❌ No root shell required

❌ No traditional privilege escalation performed

## 🧠 Key Takeaways
-Enumeration and logic flaws outweighed exploitation complexity
-Sensitive assets can be exposed through misconfigured SUID binaries
-Root access is not always required to compromise high‑value targets

