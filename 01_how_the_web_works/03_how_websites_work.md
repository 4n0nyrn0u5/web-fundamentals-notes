# 🖥️ How Websites Work (Veb-saytlar Qanday Ishlaydi)

> **Maqsad:** Veb-saytlarning asosiy tuzilishi — HTML, CSS, JavaScript va server-side texnologiyalarni tushunish.

---

## 📖 Veb-sayt Tuzilishi

| Qism | Nomi | Ishlash joyi |
|------|------|-------------|
| **Front-end** | Client-side | Brauzerda |
| **Back-end** | Server-side | Serverda |

---

## 🎨 HTML — Tuzilish

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Sahifa nomi</title>
  </head>
  <body>
    <h1>Sarlavha</h1>
    <p>Matn</p>
    <a href="/login">Kirish</a>

    <!-- Forma -->
    <form action="/login" method="POST">
      <input type="text" name="username">
      <input type="password" name="password">
      <button type="submit">Kirish</button>
    </form>

    <!-- Yashirin maydon -->
    <input type="hidden" name="price" value="100">

    <!-- Izoh — maxfiy bo'lishi mumkin! -->
    <!-- Admin: /secret-admin  Pass: Summer2024! -->
  </body>
</html>
```

**Pentester uchun muhim:**
```html
<!-- Izohlar → maxfiy ma'lumot -->
<!-- Yashirin inputlar → qiymatni o'zgartirish mumkin -->
<input type="hidden" name="price" value="100">
<input type="hidden" name="role" value="user">
<!-- Form action → so'rov qayerga ketadi -->
<form action="/api/purchase" method="POST">
```

---

## ⚡ JavaScript — Dinamik

**JS fayllarida nima qidirish:**
```javascript
// F12 → Sources → .js fayllar → Ctrl+F:
password
secret
api_key
token
admin
/api/
/internal/

// Xavfli kod:
eval(userInput);              // ⚠️ Code injection
document.innerHTML = input;   // ⚠️ XSS
const API_KEY = "sk-abc123";  // ⚠️ Maxfiy kalit
```

---

## ⚙️ Back-end Texnologiyalar

### Server-side tillar:
```
PHP     → WordPress, eski saytlar
Python  → Django, Flask
Node.js → Express
ASP.NET → Microsoft
```

### Ma'lumotlar bazalari:
```
MySQL      → Eng keng tarqalgan
PostgreSQL → Kuchli SQL
MongoDB    → NoSQL
Redis      → Kesh, session
```

### Texnologiyani aniqlash:
```bash
# HTTP headerlardan:
curl -I https://target.com
# Server: Apache/2.4.41
# X-Powered-By: PHP/7.4.3

# URL dan:
/wp-admin/     → WordPress
/.aspx         → ASP.NET
/.php          → PHP

# Wappalyzer (brauzer extension) → Avtomatik
whatweb https://target.com
```

---

## 🔍 Saytni Qo'lda Tekshirish

```
1. CTRL+U → Manba kodi
   → Izohlarni qidiring: <!-- -->
   → Yashirin inputlar: type="hidden"
   → JS fayl manzillari: <script src="...">

2. F12 → Network tab
   → API endpoint larni toping

3. F12 → Sources
   → JS fayllarni oching → {} formatlab
   → password, secret, api_key qidiring

4. F12 → Application
   → Cookies, localStorage

5. robots.txt va sitemap.xml
   → https://target.com/robots.txt
```

---

## 🛡️ Asosiy Zaifliklar

### Yashirin inputlarni o'zgartirish:
```html
<!-- Asl: -->
<input type="hidden" name="price" value="1000">
<!-- F12 → Inspector → value="1" ga o'zgartir → 1 so'mga sotib ol! -->
```

### JS fayllarida maxfiy ma'lumot:
```javascript
const apiKey = "AIzaSyXXXXXXXX";     // Google API key
const dbPassword = "SuperSecret123";  // ⚠️
```

---

## 💡 Eslab Qolish Uchun

| Texnologiya | Zaiflik |
|------------|---------|
| **HTML** | Izohlar, hidden input |
| **JavaScript** | API key, endpoint, XSS |
| **PHP** | SQLi, LFI, RCE |
| **MySQL** | SQL Injection |

```
CTRL+U      → Manba kodi
F12         → DevTools
robots.txt  → Yashirin sahifalar
Wappalyzer  → Texnologiya aniqlash
```
