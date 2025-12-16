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
