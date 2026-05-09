# 🌐 DNS in Detail (DNS Batafsil)

> **Maqsad:** DNS qanday ishlashini tushunish — domen nomlarini IP manzillarga aylantirish jarayoni.

---

## 📖 DNS nima?

**DNS (Domain Name System — Domen Nomlari Tizimi)** — internet telefon kitobi. Inson o'qiy oladigan domen nomlarini (`google.com`) kompyuter tushunadigan IP manzillarga (`142.250.185.46`) aylantiradi.

```
Brauzer: "google.com ga kirmoqchiman"
DNS:     "google.com = 142.250.185.46"
Brauzer: "142.250.185.46 ga ulanaman"
```

---

## 🏗️ Domen Ierarxiyasi (Domain Hierarchy)

```
                    . (Root — ildiz)
                    |
          ┌─────────┴─────────┐
         .com               .org
          |                   |
       google               wikipedia
          |
        www
```

### Domen turlari:

| Tur | Misol | Ma'nosi |
|-----|-------|---------|
| **Root Domain** | `.` | Eng yuqori daraja (ko'rinmaydi) |
| **TLD** (Top Level Domain) | `.com`, `.org`, `.uz` | Eng yuqori ko'rinadigan daraja |
| **Second Level Domain** | `google.com` | Asosiy domen nomi |
| **Subdomain** | `www.google.com` | Qo'shimcha bo'lim |

### TLD turlari:
```
gTLD (Generic — Umumiy):
  .com → Tijorat
  .org → Tashkilot
  .net → Tarmoq
  .edu → Ta'lim
  .gov → Hukumat

ccTLD (Country Code — Mamlakat kodi):
  .uz  → O'zbekiston
  .uk  → Buyuk Britaniya
  .ru  → Rossiya
  .de  → Germaniya
```

---

## 📋 DNS Yozuv Turlari (Record Types)

| Yozuv | To'liq nomi | Ma'nosi | Misol |
|-------|-------------|---------|-------|
| **A** | Address | IPv4 manzil | `google.com → 142.250.185.46` |
| **AAAA** | Address (v6) | IPv6 manzil | `google.com → 2607:f8b0::200e` |
| **CNAME** | Canonical Name | Taxallus — boshqa domeni ko'rsatadi | `shop.google.com → stores.google.com` |
| **MX** | Mail Exchange | Pochta serveri | `google.com → smtp.google.com` |
| **TXT** | Text | Matn ma'lumoti | SPF, DKIM, tasdiqlash |
| **NS** | Name Server | DNS serveri | `google.com → ns1.google.com` |
| **SOA** | Start of Authority | Zona ma'lumotlari | |
| **PTR** | Pointer | IP → Domen (teskari) | `142.250.185.46 → google.com` |

---

## 🔄 DNS So'rov Jarayoni (DNS Resolution)

```
1. Brauzerda "tryhackme.com" kiritasiz

2. Lokal kesh tekshiriladi
   → Avval ko'rilganmi? Ha → IP qaytariladi ✅
   → Yo'q → davom etadi

3. Recursive DNS Resolver (ISP yoki 8.8.8.8)
   → Sizning so'rovingizni oladi

4. Root Name Server (.) ga so'rov
   → ".com" uchun qaysi server? → TLD server manzili

5. TLD Name Server (.com) ga so'rov
   → "tryhackme.com" uchun qaysi server? → Authoritative server

6. Authoritative Name Server ga so'rov
   → "tryhackme.com" = 104.26.10.229 ✅

7. Javob keshga saqlanadi (TTL muddat)

8. Brauzer IP ga ulanadi
```

### TTL (Time To Live — Yashash Muddati):
```
TTL = 300   → 5 daqiqa keshlash
TTL = 3600  → 1 soat keshlash
TTL = 86400 → 24 soat keshlash

# Past TTL = Tez-tez yangilanadi
# Yuqori TTL = Uzoq keshlash (server yukini kamaytiradi)
```

---

## 🛠️ DNS Buyruqlari

```bash
# Asosiy so'rov
nslookup google.com
nslookup -type=MX google.com      # MX yozuv
nslookup -type=TXT google.com     # TXT yozuv

# dig — kuchli vosita
dig google.com                     # A yozuv
dig google.com MX                  # MX yozuv
dig google.com ANY                 # Barcha yozuvlar
dig google.com +short              # Faqat IP
dig @8.8.8.8 google.com           # Google DNS dan so'rash

# host — sodda
host google.com
host -t MX google.com

# Teskari qidirish (IP → Domen)
dig -x 142.250.185.46
nslookup 142.250.185.46

# Zone Transfer urinish (AXFR)
dig axfr @ns1.target.com target.com
# Agar muvaffaqiyatli → barcha DNS yozuvlar ko'rinadi!
```

---

## 🔍 Pentesting uchun DNS

### Muhim tekshiruvlar:
```bash
# 1. Barcha yozuvlarni ko'rish
dig target.com ANY

# 2. Name serverlarni topish
dig target.com NS

# 3. Mail serverlarni topish
dig target.com MX

# 4. TXT yozuvlarni ko'rish (maxfiy ma'lumot bo'lishi mumkin)
dig target.com TXT

# 5. Zone Transfer (zaif serverlarda ishlaydi)
dig axfr @ns1.target.com target.com

# 6. Subdomenlarni DNS brute force
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 7. PTR yozuv — IP dan domen topish
dig -x IP_MANZIL
```

### DNS Cache Poisoning (DNS Kesh Zaharlash):
```
Hujumchi DNS keshiga noto'g'ri yozuv kiritadi:
google.com → Hujumchi IP
→ Qurbonlar hujumchi serveriga yo'naltiriladi
```

---

## 💡 Eslab Qolish Uchun

| Tushuncha | Ma'nosi |
|-----------|---------|
| **DNS** | Domen → IP aylantiruvchi tizim |
| **A yozuv** | IPv4 manzil |
| **CNAME** | Taxallus (boshqa domeni) |
| **MX** | Pochta serveri |
| **TXT** | Matn (SPF, tasdiqlash) |
| **TTL** | Kesh muddati (soniyada) |
| **Zone Transfer** | Barcha DNS yozuvlarni olish (AXFR) |

**Tezkor buyruqlar:**
```bash
dig target.com ANY          # Barcha yozuvlar
dig target.com NS           # Name serverlar
dig axfr @ns1.target.com target.com  # Zone transfer
nslookup -type=MX target.com         # Pochta serveri
```
