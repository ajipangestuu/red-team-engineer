# 01 — System Enumeration

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

````bash
hostname        # Menampilkan nama host
uname -a        # Menampilkan kernel, versi, arsitektur
ps aux          # Menampilkan daftar proses yang sedang berjalan

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

### 🔹 Network Enumeration
Mengetahui konfigurasi dan koneksi jaringan.
```bash
ifconfig         # Informasi interface jaringan (IP, MAC)
ip a             # Alternatif ifconfig, detail IP & interface
ip route         # Menampilkan routing table
arp -a           # Cache ARP, host lain di LAN
netstat -ano     # Koneksi aktif, port listening, PID

### 🔹 Password & Credential Enumeration
Mencari file yang mungkin menyimpan password atau private key.
```bash
locate password | more          # Cari file dengan kata "password"
locate pass | more              # Cari file dengan kata "pass"
find / -name id_rsa 2>/dev/null # Cari private SSH key





````
