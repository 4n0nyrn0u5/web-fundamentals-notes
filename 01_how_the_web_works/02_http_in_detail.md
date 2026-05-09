# 🌍 HTTP in Detail (HTTP Batafsil)

> **Maqsad:** HTTP protokoli qanday ishlashi, so'rov va javob tuzilishi, metodlar va status kodlarini tushunish.

---

## 📖 HTTP nima?

**HTTP (HyperText Transfer Protocol — Gipermatn Uzatish Protokoli)** — brauzer va veb-server orasida ma'lumot almashish qoidalari to'plami.

**HTTPS** = HTTP + **TLS/SSL** (shifrlash) — ma'lumotlar shifrlangan holda uzatiladi.

```
HTTP:  Ma'lumot ochiq uzatiladi → Tinglash mumkin ⚠️
HTTPS: Ma'lumot shifrlangan   → Xavfsiz ✅
```

---

## 🔄 HTTP So'rov/Javob Jarayoni

```
1. Brauzer: "tryhackme.com/page ga kirmoqchiman"
2. DNS: "tryhackme.com = 104.26.10.229"
3. Brauzer → Server: HTTP So'rov yuboradi
4. Server → Brauzer: HTTP Javob qaytaradi
5. Brauzer: HTML ni ko'rsatadi
```

---

## 📤 HTTP So'rov Tuzilishi (Request)

```http
GET /page HTTP/1.1
Host: tryhackme.com
User-Agent: Mozilla/5.0 (Windows NT 10.0)
Accept: text/html
Accept-Language: en-US
Cookie: session=abc123
Connection: keep-alive

[body — faqat POST/PUT da bo'ladi]
```

| Qism | Misol | Ma'nosi |
|------|-------|---------|
| **Metod** | `GET` | Qanday amal |
| **URL** | `/page` | Qaysi resurs |
| **Versiya** | `HTTP/1.1` | HTTP versiyasi |
| **Host** | `tryhackme.com` | Qaysi server |
| **Headers** | `User-Agent: ...` | Qo'shimcha ma'lumot |
| **Body** | `username=admin` | Yuborilayotgan ma'lumot |

---

## 📥 HTTP Javob Tuzilishi (Response)

```http
HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Thu, 07 May 2026 10:00:00 GMT
Content-Type: text/html; charset=utf-8
Set-Cookie: session=xyz789; HttpOnly
Content-Length: 1234

<!DOCTYPE html>
<html>...</html>
```

---

## 🔧 HTTP Metodlar (Methods)

| Metod | Ishlatilishi | Ma'lumot joyi |
|-------|-------------|---------------|
| **GET** | Ma'lumot olish | URL da |
| **POST** | Ma'lumot yuborish | Body da |
| **PUT** | Ma'lumot yangilash (to'liq) | Body da |
| **PATCH** | Ma'lumot yangilash (qisman) | Body da |
| **DELETE** | Ma'lumot o'chirish | URL da |
| **HEAD** | Faqat header olish | — |
| **OPTIONS** | Qaysi metodlar ruxsat? | — |

### Pentesting uchun:
```
GET  → Ma'lumot o'qish, IDOR
POST → Login, forma yuborish, SQL injection
PUT  → Fayl yuklash, ma'lumot yangilash
DELETE → Ma'lumot o'chirish (ruxsatsiz)
OPTIONS → Qaysi metodlar mavjud (CORS tekshirish)
```

---

## 📊 HTTP Status Kodlari

### 1xx — Ma'lumot (Informational)
```
100 Continue → Davom eting
```

### 2xx — Muvaffaqiyat (Success)
```
200 OK           → Muvaffaqiyatli
201 Created      → Yangi resurs yaratildi (POST)
204 No Content   → Muvaffaqiyatli, javob yo'q
```

### 3xx — Yo'naltirish (Redirection)
```
301 Moved Permanently  → Doimiy ko'chdi
302 Found              → Vaqtinchalik ko'chdi (login muvaffaqiyatli)
304 Not Modified       → Keshdan foydalaning
```

### 4xx — Mijoz Xatosi (Client Error)
```
400 Bad Request        → Noto'g'ri so'rov
401 Unauthorized       → Login kerak
403 Forbidden          → Ruxsat yo'q (mavjud lekin taqiqlangan)
404 Not Found          → Topilmadi
405 Method Not Allowed → Bu metod ruxsat etilmagan
429 Too Many Requests  → Ko'p so'rov (rate limit)
```

### 5xx — Server Xatosi (Server Error)
```
500 Internal Server Error → Server ichki xatosi
502 Bad Gateway           → Gateway xatosi
503 Service Unavailable   → Server band/o'chiq
```

---

## 📋 Muhim HTTP Headerlar (Headers)

### So'rov Headerlari:
```http
Host: target.com                    → Qaysi server (virtual hosting)
User-Agent: Mozilla/5.0             → Brauzer/dastur turi
Accept: text/html,application/json  → Qabul qilish formati
Cookie: session=abc123              → Cookie lar
Authorization: Bearer eyJhbG...    → Token autentifikatsiya
Content-Type: application/json      → Body formati
Content-Length: 123                 → Body uzunligi
Referer: https://google.com        → Qayerdan keldi
X-Forwarded-For: 127.0.0.1        → Asl IP (proxy orqali)
Origin: https://target.com         → So'rov manbasi (CORS)
```

### Javob Headerlari:
```http
Set-Cookie: session=xyz; HttpOnly; Secure
Content-Type: text/html; charset=utf-8
Location: /dashboard               → Redirect manzili
Server: nginx/1.18                 → Server turi
X-Frame-Options: DENY              → Clickjacking himoya
Content-Security-Policy: ...       → CSP
Access-Control-Allow-Origin: *     → CORS
Strict-Transport-Security: ...     → HSTS
```

---

## 🍪 Cookie lar

```http
# Server cookie o'rnatadi:
Set-Cookie: session=abc123; 
            HttpOnly;        → JS o'qiy olmaydi (XSS himoya)
            Secure;          → Faqat HTTPS
            SameSite=Strict; → CSRF himoya
            Path=/;          → Qaysi yo'lda ishlaydi
            Expires=...      → Muddati

# Brauzer har so'rovda qaytaradi:
Cookie: session=abc123
```

### Cookie xavfsizlik bayroqlari:
| Bayroq | Himoya |
|--------|--------|
| `HttpOnly` | XSS dan — JS o'qiy olmaydi |
| `Secure` | Faqat HTTPS da yuboriladi |
| `SameSite=Strict` | CSRF dan — boshqa saytdan yuborilmaydi |

---

## 🔍 URL Tuzilishi

```
https://user:pass@www.example.com:8080/path/page?key=val&k2=v2#section
  ↑      ↑    ↑    ↑               ↑    ↑         ↑              ↑
Sxema  Foyda- Parol  Host          Port  Yo'l    Parametrlar    Anchor
       lanuvchi
```

---

## 🎯 Amaliy Misol: curl bilan HTTP

```bash
# GET so'rov
curl http://target.com

# Header ko'rish
curl -I http://target.com          # Faqat headerlar
curl -v http://target.com          # Batafsil (verbose)

# POST so'rov
curl -X POST http://target.com/login \
     -d "username=admin&password=test"

# JSON yuborish
curl -X POST http://target.com/api \
     -H "Content-Type: application/json" \
     -d '{"username":"admin","password":"test"}'

# Cookie bilan
curl -b "session=abc123" http://target.com

# Custom header
curl -H "X-Forwarded-For: 127.0.0.1" http://target.com/admin

# Redirect kuzatish
curl -L http://target.com          # -L = follow redirects

# HTTPS sertifikatni e'tiborsiz qoldirish
curl -k https://target.com
```

---

## 💡 Eslab Qolish Uchun

| Tushuncha | Ma'nosi |
|-----------|---------|
| **GET** | Ma'lumot olish — URL da |
| **POST** | Ma'lumot yuborish — body da |
| **200** | OK ✅ |
| **301/302** | Redirect |
| **401** | Login kerak |
| **403** | Ruxsat yo'q |
| **404** | Topilmadi |
| **500** | Server xatosi |
| **HttpOnly** | XSS dan cookie himoya |
| **HTTPS** | Shifrlangan HTTP |

**Pentester uchun muhim:**
- `403` = Mavjud lekin taqiqlangan → bypass urinib ko'r
- `302` = Login muvaffaqiyatli (redirect)
- `500` = Server xatosi → zaiflik bo'lishi mumkin
- `X-Forwarded-For: 127.0.0.1` → IP filterni chetlab o'tish
