# 🛡️ Red Team Labs – Privilege Escalation (Linux & Windows)  

Koleksi **lab praktikum Red Teaming** untuk belajar dan melatih teknik **Privilege Escalation** di Linux & Windows.  
Tujuan repo ini:  
- Menyediakan dokumentasi langkah-langkah eksploitasi  
- Latihan hands-on di VM/CTF style  
- Referensi cepat saat pentest / red team engagement  

---

## 📂 Daftar Lab  

### 🐧 Linux Privilege Escalation  

#### 🔑 Fundamental  
- **Lab 01 – Enumeration**  
  - Enumerasi user, group, SUID binaries  
  - Tools: `linpeas.sh`, `linux-exploit-suggester`, manual checks  

- **Lab 02 – SUID & Capabilities Exploits**  
  - Abuse `find`, `nmap`, `vim`, dll (GTFOBins)  
  - Check capabilities (`getcap`)  

- **Lab 03 – Weak File Permissions**  
  - `/etc/passwd` writable → tambah root user  
  - Cron job insecure → root escalation  

- **Lab 04 – Exploiting Services & Cron Jobs**  
  - PATH hijacking  
  - Systemd misconfigurations  

- **Lab 05 – Kernel Exploits**  
  - DirtyCow, DirtyPipe, dsb  

#### 🚀 Advanced  
- **Lab 06 – PrivEsc via Docker/LXC**  
- **Lab 07 – PrivEsc via Password Reuse & SSH Keys**  
- **Lab 08 – Real-World CTF Simulation**  

---

### 🪟 Windows Privilege Escalation  

#### 🔑 Fundamental  
- **Lab 01 – Enumeration**  
  - `whoami /priv`, `systeminfo`, `winPEAS`  
  - Cari patch missing & privilege  

- **Lab 02 – Exploiting Services**  
  - Insecure service permissions  
  - Unquoted service paths  
  - DLL hijacking  

- **Lab 03 – Registry & Scheduled Tasks**  
  - Weak registry keys  
  - Abusing scheduled tasks  

- **Lab 04 – UAC Bypass & Token Impersonation**  
  - RunAs abuse  
  - Potato exploits (JuicyPotato, PrintSpoofer)  

#### 🚀 Advanced  
- **Lab 05 – Kernel Exploits**  
  - MS16-032, MS17-010 (EternalBlue)  
- **Lab 06 – Credential Dumping & Reuse**  
  - Mimikatz  
  - LSASS dump → pass-the-hash  
- **Lab 07 – AD PrivEsc**  
  - Kerberoasting  
  - AS-REP Roasting  
  - GPP Passwords  

---

## 🎯 Tujuan  
- Pahami teknik **Privilege Escalation** di Linux & Windows  
- Siapkan **cheat sheet pribadi** untuk CTF & real engagement  
- Dokumentasi eksploitasi step-by-step  

---

## 🔧 Tools  

### Linux  
- `linpeas.sh`  
- `linux-exploit-suggester`  
- [GTFOBins](https://gtfobins.github.io/)  

### Windows  
- `winPEAS`  
- `Mimikatz`  
- `PowerUp.ps1`  
- `Seatbelt`  

---

## 🚀 Status (2025)  

### Linux Labs  
- [ ] Lab 01 – Enumeration  
- [ ] Lab 02 – SUID & Capabilities  
- [ ] Lab 03 – Weak File Permissions  
- [ ] Lab 04 – Exploiting Services  
- [ ] Lab 05 – Kernel Exploits  
- [ ] Lab 06 – Docker/LXC Escape  
- [ ] Lab 07 – Password Reuse  
- [ ] Lab 08 – Real-World  

### Windows Labs  
- [ ] Lab 01 – Enumeration  
- [ ] Lab 02 – Exploiting Services  
- [ ] Lab 03 – Registry & Scheduled Tasks  
- [ ] Lab 04 – UAC Bypass & Token Impersonation  
- [ ] Lab 05 – Kernel Exploits  
- [ ] Lab 06 – Credential Dumping & Reuse  
- [ ] Lab 07 – AD PrivEsc  

---

✍️ Dibuat oleh: **Aji Pangestu**  
