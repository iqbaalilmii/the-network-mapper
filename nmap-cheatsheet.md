# 🗺️ Nmap Cheat Sheet — Security Assessment & CTF



---

## 1. 🎯 Target Specification

Cara menentukan target: IP tunggal, range, subnet, hostname, hingga file input.

| Syntax | Contoh | Keterangan |
|--------|--------|------------|
| `nmap <IP>` | `nmap 192.168.1.1` | Scan satu host |
| `nmap <IP1> <IP2>` | `nmap 10.0.0.1 10.0.0.2` | Scan beberapa host |
| `nmap <range>` | `nmap 192.168.1.1-20` | Scan range IP |
| `nmap <CIDR>` | `nmap 192.168.1.0/24` | Scan seluruh subnet |
| `nmap <hostname>` | `nmap scanme.nmap.org` | Scan via hostname |
| `nmap -iL <file>` | `nmap -iL targets.txt` | Baca target dari file (satu per baris) |
| `nmap -iR <n>` | `nmap -iR 10` | Scan `n` host random di internet |
| `nmap --exclude <IP>` | `nmap 10.0.0.0/24 --exclude 10.0.0.1` | Kecualikan host tertentu |
| `nmap --excludefile <file>` | `nmap 10.0.0.0/24 --excludefile skip.txt` | Kecualikan dari file |

---

## 2. 🔍 Basic Scanning Techniques

Jenis scan yang umum digunakan, masing-masing dengan karakteristik dan kegunaan berbeda.

| Flag | Nama | Keterangan |
|------|------|------------|
| `nmap -sT` | TCP Connect Scan | Full 3-way handshake. Tidak butuh root, lebih mudah terdeteksi |
| `nmap -sS` | TCP SYN Scan *(Stealth)* | Half-open scan, lebih cepat & sedikit noise. **Butuh root/sudo** |
| `nmap -sU` | UDP Scan | Scan port UDP. Lambat, tapi penting (DNS, SNMP, DHCP) |
| `nmap -sN` | TCP Null Scan | Kirim paket tanpa flag TCP — lolos beberapa firewall |
| `nmap -sF` | TCP FIN Scan | Hanya flag FIN — lolos beberapa firewall |
| `nmap -sX` | Xmas Scan | Flag FIN+PSH+URG — lolos beberapa firewall |
| `nmap -sA` | ACK Scan | Identifikasi aturan firewall (filtered vs unfiltered) |
| `nmap -sW` | Window Scan | Mirip ACK, menganalisis TCP window size |
| `nmap -sM` | Maimon Scan | Varian FIN/ACK — berguna di beberapa OS lawas |
| `nmap -sI <zombie>` | Idle/Zombie Scan | Scan tersembunyi menggunakan host "zombie" perantara |
| `nmap -sO` | IP Protocol Scan | Deteksi protokol IP yang didukung host (bukan port) |
| `nmap -A` | Aggressive Scan | Kombinasi: OS detect + version + script + traceroute |

---

## 3. 🔌 Port Selection Options

Kontrol port mana yang di-scan untuk efisiensi dan presisi.

| Flag | Contoh | Keterangan |
|------|--------|------------|
| `nmap -p <port>` | `nmap -p 80` | Scan satu port spesifik |
| `nmap -p <range>` | `nmap -p 1-1024` | Scan range port |
| `nmap -p <list>` | `nmap -p 22,80,443,3306` | Scan port-port tertentu |
| `nmap -p U:<port>,T:<port>` | `nmap -p U:53,T:80` | Gabung scan UDP & TCP |
| `nmap -p-` | `nmap -p-` | Scan semua 65535 port |
| `nmap -F` | `nmap -F 10.0.0.1` | Fast scan — hanya 100 port paling umum |
| `nmap --top-ports <n>` | `nmap --top-ports 1000` | Scan `n` port paling populer |
| `nmap -r` | `nmap -r 10.0.0.1` | Scan port secara berurutan (tidak acak) |
| `nmap --open` | `nmap --open 10.0.0.1` | Hanya tampilkan port yang terbuka |

---

## 4. 🔬 Service and OS Detection

Identifikasi versi layanan yang berjalan dan sistem operasi target.

| Flag | Contoh | Keterangan |
|------|--------|------------|
| `nmap -sV` | `nmap -sV 10.0.0.1` | Deteksi versi service di tiap port terbuka |
| `nmap -sV --version-intensity <0-9>` | `nmap -sV --version-intensity 5` | Level intensitas probe (0=ringan, 9=agresif) |
| `nmap -sV --version-light` | `nmap -sV --version-light` | Probe minimal (intensity 2), lebih cepat |
| `nmap -sV --version-all` | `nmap -sV --version-all` | Coba semua probe (intensity 9), paling akurat |
| `nmap -O` | `nmap -O 10.0.0.1` | Deteksi sistem operasi via TCP/IP fingerprinting |
| `nmap -O --osscan-guess` | `nmap -O --osscan-guess` | Tebak OS meski tidak 100% yakin |
| `nmap -O --osscan-limit` | `nmap -O --osscan-limit` | Lewati host yang tidak mungkin diidentifikasi |
| `nmap -A` | `nmap -A 10.0.0.1` | All-in-one: OS + Version + NSE scripts + Traceroute |
| `nmap --traceroute` | `nmap --traceroute 10.0.0.1` | Tampilkan jalur paket ke target |

---

## 5. ⏱️ Timing and Performance

Kontrol kecepatan scan — penting untuk menghindari deteksi atau mempercepat proses.

| Template | Flag | Keterangan |
|----------|------|------------|
| Paranoid | `-T0` | Sangat lambat, satu probe tiap 5 menit. Hindari IDS |
| Sneaky | `-T1` | Sangat lambat, satu probe tiap 15 detik |
| Polite | `-T2` | Lambat, tidak membebani jaringan |
| Normal | `-T3` | **Default** — keseimbangan kecepatan & akurasi |
| Aggressive | `-T4` | Cepat, diasumsikan jaringan stabil & cepat |
| Insane | `-T5` | Sangat agresif, bisa melewatkan port / tidak akurat |

### Kontrol Manual Lanjutan

| Flag | Contoh | Keterangan |
|------|--------|------------|
| `--min-rate <n>` | `--min-rate 1000` | Minimal `n` paket per detik |
| `--max-rate <n>` | `--max-rate 500` | Maksimal `n` paket per detik |
| `--min-parallelism <n>` | `--min-parallelism 10` | Minimal probe berjalan paralel |
| `--max-parallelism <n>` | `--max-parallelism 1` | Batasi probe paralel (satu per satu) |
| `--host-timeout <ms>` | `--host-timeout 30000` | Batas waktu per host (ms) |
| `--scan-delay <ms>` | `--scan-delay 500` | Jeda antar probe (ms) — bantu hindari rate limiting |
| `--max-retries <n>` | `--max-retries 2` | Batas percobaan ulang per port |

---

## 6. 🛡️ Firewall / IDS Evasion & Spoofing

Teknik untuk melewati firewall, IDS/IPS, atau menyembunyikan identitas scanner.

| Flag | Contoh | Keterangan |
|------|--------|------------|
| `nmap -f` | `nmap -f 10.0.0.1` | Fragment paket menjadi 8-byte chunk — hindari DPI |
| `nmap --mtu <n>` | `nmap --mtu 16 10.0.0.1` | Atur ukuran MTU custom (kelipatan 8) |
| `nmap -D <decoy1,decoy2,ME>` | `nmap -D 1.1.1.1,2.2.2.2,ME` | Kirim scan seolah dari banyak IP (decoy). `ME` = posisi IP asli |
| `nmap -S <IP>` | `nmap -S 1.2.3.4 -e eth0` | Spoof source IP address |
| `nmap -e <iface>` | `nmap -e eth0` | Gunakan interface jaringan tertentu |
| `nmap -g <port>` | `nmap -g 53` | Spoof source port (misal: port 53/DNS untuk bypass firewall) |
| `nmap --source-port <port>` | `nmap --source-port 443` | Alias untuk `-g` |
| `nmap --spoof-mac <val>` | `nmap --spoof-mac 0` | Spoof MAC address (0 = random MAC) |
| `nmap --spoof-mac <vendor>` | `nmap --spoof-mac Apple` | Spoof MAC sesuai vendor tertentu |
| `nmap --badsum` | `nmap --badsum 10.0.0.1` | Kirim paket dengan checksum invalid — deteksi firewall lemah |
| `nmap --data-length <n>` | `nmap --data-length 25` | Tambahkan `n` byte data random ke paket |
| `nmap --ip-options <opts>` | `nmap --ip-options "L 10.0.0.1"` | Tambahkan IP options (loose source routing, dll) |
| `nmap --proxies <proxy>` | `nmap --proxies http://proxy:8080` | Arahkan scan lewat HTTP/SOCKS proxy |
| `nmap -6` | `nmap -6 fe80::1` | Enable IPv6 scanning |

---

## 7. 📜 Nmap Scripting Engine (NSE)

NSE memungkinkan otomasi task lanjutan: vulnerability detection, brute force, enumeration, dll.

### Cara Menggunakan Script

| Flag | Contoh | Keterangan |
|------|--------|------------|
| `nmap -sC` | `nmap -sC 10.0.0.1` | Jalankan script **default** (setara `--script=default`) |
| `nmap --script <name>` | `nmap --script http-title` | Jalankan satu script spesifik |
| `nmap --script <cat>` | `nmap --script vuln` | Jalankan semua script dalam kategori |
| `nmap --script "<pattern>"` | `nmap --script "http-*"` | Wildcard — semua script dengan prefix `http-` |
| `nmap --script-args <k=v>` | `nmap --script-args user=admin,pass=secret` | Pass argumen ke script |
| `nmap --script-args-file <f>` | `nmap --script-args-file args.txt` | Baca argumen script dari file |
| `nmap --script-help <name>` | `nmap --script-help smb-vuln-ms17-010` | Tampilkan dokumentasi script |
| `nmap --script-updatedb` | `nmap --script-updatedb` | Update database script NSE |

### Kategori Script Penting

| Kategori | Flag | Kegunaan Umum |
|----------|------|---------------|
| **default** | `--script=default` | Script aman & informatif, dijalankan bersama `-sC`. Cocok untuk recon awal |
| **auth** | `--script=auth` | Test autentikasi: cek anonymous login, default credentials |
| **vuln** | `--script=vuln` | Scan kerentanan umum (EternalBlue, Heartbleed, dll) |
| **safe** | `--script=safe` | Script yang tidak merusak / tidak agresif |
| **discovery** | `--script=discovery` | Enumerasi lebih lanjut: DNS, SNMP, SMB shares |
| **broadcast** | `--script=broadcast` | Temukan host di LAN tanpa scan langsung |
| **brute** | `--script=brute` | Brute force login (SSH, FTP, HTTP, SMB, dll) |
| **exploit** | `--script=exploit` | Coba eksploitasi aktif (gunakan hati-hati!) |
| **malware** | `--script=malware` | Cek backdoor & tanda-tanda infeksi malware |
| **fuzzer** | `--script=fuzzer` | Kirim input tak terduga untuk temukan bug |
| **intrusive** | `--script=intrusive` | Script agresif yang bisa mengganggu layanan target |

### Script NSE Populer untuk CTF / Pentest

```bash
# SMB Enumeration & EternalBlue
nmap --script smb-enum-shares,smb-enum-users -p 445 10.0.0.1
nmap --script smb-vuln-ms17-010 -p 445 10.0.0.1

# HTTP Enumeration
nmap --script http-title,http-headers,http-methods -p 80,443 10.0.0.1
nmap --script http-enum -p 80 10.0.0.1

# FTP Anonymous Login
nmap --script ftp-anon,ftp-bounce -p 21 10.0.0.1

# SSH Brute Force
nmap --script ssh-brute -p 22 --script-args userdb=users.txt,passdb=pass.txt 10.0.0.1

# DNS Enumeration
nmap --script dns-brute --script-args dns-brute.domain=target.com

# SSL/TLS Vulnerabilities
nmap --script ssl-heartbleed,ssl-poodle,ssl-enum-ciphers -p 443 10.0.0.1

# MySQL / Database
nmap --script mysql-info,mysql-empty-password -p 3306 10.0.0.1
```

---

## 8. 📄 Output Formats

Simpan hasil scan dalam berbagai format untuk analisis atau dokumentasi.

| Flag | Ekstensi Umum | Keterangan |
|------|---------------|------------|
| `-oN <file>` | `.txt` | **Normal** — output teks seperti di terminal |
| `-oX <file>` | `.xml` | **XML** — terstruktur, cocok untuk import ke tool lain (Metasploit, dll) |
| `-oG <file>` | `.gnmap` | **Grepable** — satu host per baris, mudah di-`grep`/`awk` |
| `-oS <file>` | `.txt` | **Script Kiddie** — output stylized (jarang dipakai) |
| `-oA <basename>` | `.nmap/.xml/.gnmap` | **All formats** — simpan semua format sekaligus dengan basename yang sama |
| `-v` | — | Verbose — tampilkan info lebih banyak saat scan berlangsung |
| `-vv` | — | Very verbose — lebih detail lagi |
| `-d` | — | Debug mode — sangat detail untuk troubleshooting |
| `--reason` | — | Tampilkan alasan mengapa port dianggap open/closed/filtered |
| `--stats-every <time>` | — | Tampilkan progress setiap `n` detik (misal: `--stats-every 10s`) |
| `--packet-trace` | — | Tampilkan semua paket yang dikirim/diterima |
| `--resume <file>` | — | Lanjutkan scan yang terhenti dari file output `-oN`/`-oG` |

### Contoh Penyimpanan Output

```bash
# Simpan ke semua format sekaligus
nmap -A -oA hasil_scan 192.168.1.1

# Hasil: hasil_scan.nmap  |  hasil_scan.xml  |  hasil_scan.gnmap

# Grep dari grepable output untuk host dengan port 80 open
grep "80/open" hasil_scan.gnmap

# Parsing XML dengan xmllint
xmllint --xpath "//port[@portid='443']" hasil_scan.xml
```

---

## 🚀 Quick Reconnaissance — Perintah All-in-One

Perintah lengkap yang biasa dipakai untuk recon awal dalam CTF atau pentest:

```bash
# Full recon — OS, versi service, default scripts, semua port, simpan semua format
sudo nmap -sS -sV -sC -O -p- -T4 --min-rate 5000 -oA full_recon <TARGET_IP>

# Cepat — top 1000 port dengan deteksi versi dan script default
sudo nmap -sS -sV -sC -T4 -oA quick_recon <TARGET_IP>

# UDP + TCP gabungan (komprehensif, lebih lambat)
sudo nmap -sS -sU -sV -sC -O --top-ports 200 -T4 -oA combined_scan <TARGET_IP>

# Vulnerability scan setelah recon awal
sudo nmap -sV --script=vuln -p <OPEN_PORTS> -oA vuln_scan <TARGET_IP>

# Stealth + evasion untuk bypass firewall sederhana
sudo nmap -sS -f -T2 --source-port 53 -D RND:5 -oA stealth_scan <TARGET_IP>
```

### Alur Recon yang Disarankan

```
1. Quick scan dulu       →  nmap -T4 --top-ports 1000 <IP>
2. Full port scan        →  nmap -p- -T4 --min-rate 5000 <IP>
3. Deep scan port open   →  nmap -sV -sC -O -p <OPEN_PORTS> <IP>
4. Vuln scan             →  nmap --script=vuln -p <OPEN_PORTS> <IP>
5. Simpan semua output   →  tambahkan -oA <nama_file>
```

---

*Referensi: [nmap.org/book](https://nmap.org/book/man.html) | Script NSE: [nmap.org/nsedoc](https://nmap.org/nsedoc/)*
