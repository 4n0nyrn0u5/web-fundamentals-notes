# 🔗 Putting It All Together (Hammasini Birlashtirish)

> **Maqsad:** DNS, HTTP, veb-server, backend, ma'lumotlar bazasi — barchasining birgalikda qanday ishlashini tushunish.

---

## 📖 To'liq Jarayon: URL dan Sahifagacha

```
Brauzerda: https://tryhackme.com/room/test

1️⃣  DNS → tryhackme.com = 104.26.10.229
2️⃣  TCP ulanish (3-way handshake)
3️⃣  TLS shifrlash (HTTPS uchun)
4️⃣  HTTP So'rov yuboriladi
5️⃣  Veb-server (Nginx/Apache) qabul qiladi
6️⃣  Backend (PHP/Python) ishlov beradi
7️⃣  Ma'lumotlar bazasi so'raladi
8️⃣  HTML generatsiya qilinadi
9️⃣  HTTP Javob qaytariladi
🔟  Brauzer render qiladi
```

---

## 🔄 Har Bir Qadam

### 1. DNS Resolution
```
Brauzer keshi → /etc/hosts → ISP DNS → Root → TLD → Authoritative
→ IP manzil olinadi → davom etadi
```

### 2. TCP 3-Way Handshake
```
Brauzer → Server: SYN
Server  → Brauzer: SYN-ACK
Brauzer → Server: ACK
→ Ulanish o'rnatildi!
```

### 3. HTTP So'rov
```http
GET /room/test HTTP/1.1
Host: tryhackme.com
Cookie: session=abc123
```

### 4. Veb-server
```
Statik fayl (HTML/CSS/JS/rasm) → To'g'ridan qaytaradi
Dinamik so'rov               → Backend ga yo'naltiradi
Topilmadi                    → 404 qaytaradi
```

### 5. Backend + Baza
```python
# So'rovni qayta ishlash:
room = db.query("SELECT * FROM rooms WHERE slug='test'")
return render_template('room.html', room=room)
```

### 6. Javob va Render
```http
HTTP/1.1 200 OK
Content-Type: text/html

<html>...</html>
```
```
Brauzer: HTML → DOM → CSS → Render → Ko'rsatish
```

---

## 🧩 Komponentlar va Vazifalari

| Komponent | Misol | Vazifasi |
|-----------|-------|---------|
| **DNS** | 8.8.8.8 | Domen → IP |
| **CDN** | Cloudflare | Tez yetkazish, DDoS himoya |
| **Load Balancer** | HAProxy | Yukni taqsimlash |
| **Veb-server** | Nginx, Apache | HTTP boshqarish |
| **Backend** | PHP, Python | Biznes mantiq |
| **Ma'lumotlar bazasi** | MySQL | Ma'lumot saqlash |
| **Kesh** | Redis | Tez javob |

---

## 🔍 Pentesting Nuqtai Nazaridan

```
DNS          → Cache poisoning, hijacking
TLS/HTTPS    → Sertifikat muammolari
HTTP So'rov  → Header injection, method bypass
Veb-server   → Path traversal, konfiguratsiya xato
Backend      → SQLi, XSS, IDOR, LFI, SSRF, RCE
Baza         → SQL Injection
Session      → Cookie o'g'irlash
```

---

## 💡 Eslab Qolish Uchun

**Har bir qadam = Potentsial hujum nuqtasi!**

```
URL → DNS → TCP → TLS → HTTP So'rov
→ Veb-server → Backend → Baza
→ HTML → HTTP Javob → Brauzer Render
```

**Muhim fayllar (Linux):**
```
/var/www/html/        → Veb fayllari
/etc/nginx/           → Nginx konfiguratsiya
/var/log/apache2/     → Apache loglar
/etc/hosts            → Lokal DNS
```
