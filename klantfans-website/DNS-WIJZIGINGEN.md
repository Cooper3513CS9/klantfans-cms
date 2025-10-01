# 🌐 DNS Wijzigingen voor klantfans.nl → Vercel

**Datum:** 1 oktober 2025  
**Doel:** Website verhuizen van oude server (37.97.166.105) naar Vercel  
**Email:** Blijft op oude server (i24.host)

---

## ✅ **Wat moet er gebeuren:**

### **Stap 1: Wijzig klantfans.nl A-record**
**VOOR:**
```
klantfans.nl.    3600    A    37.97.166.105
```

**NA:**
```
klantfans.nl.    3600    A    76.76.21.21
```

**Actie:**
1. Klik op potlood 🖊️ bij `klantfans.nl.` A-record
2. Wijzig IP van `37.97.166.105` → `76.76.21.21`
3. Opslaan

---

### **Stap 2: Wijzig www naar CNAME**
**VOOR:**
```
www    3600    A    37.97.166.105
```

**NA:**
```
www    3600    CNAME    cname.vercel-dns.com
```

**Actie:**
1. Klik op potlood 🖊️ bij `www` A-record
2. Wijzig Type van `A` → `CNAME`
3. Wijzig Waarde van `37.97.166.105` → `cname.vercel-dns.com`
4. Opslaan

---

### **Stap 3: Verwijder klantfans.nl AAAA-record**
**VERWIJDEREN:**
```
klantfans.nl.    3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
```

**Actie:**
1. Vink aan ☑️ bij `klantfans.nl.` AAAA-record
2. Klik op "Verwijderen" 🗑️
3. Bevestigen

---

### **Stap 4: Verwijder www AAAA-record**
**VERWIJDEREN:**
```
www    3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
```

**Actie:**
1. Vink aan ☑️ bij `www` AAAA-record
2. Klik op "Verwijderen" 🗑️
3. Bevestigen

---

## ❌ **NIET AANRAKEN!**

Deze records blijven **ONGEWIJZIGD** (voor email):

```
ftp                      3600    A       37.97.166.105
mail                     3600    A       37.97.166.105
pop                      3600    A       37.97.166.105
smtp                     3600    A       37.97.166.105
webmail                  3600    A       37.97.166.105
klantfans.nl.                    NS      ns1.i24.host.
klantfans.nl.                    NS      ns2.i24.host.
klantfans.nl.            3600    MX      10 web50.i24.host.
transip-A._domainkey     3600    CNAME   _dkim-A.transip.email.
transip-B._domainkey     3600    CNAME   _dkim-B.transip.email.
transip-C._domainkey     3600    CNAME   _dkim-C.transip.email.
_dmarc                   3600    TXT     "v=DMARC1; p=none..."
klantfans.nl.            3600    TXT     "v=spf1 a mx include:_spf.transip.email ~all"
x-transip-mail-auth      3600    TXT     "3e43f050a14737a81e55d8720c1ff..."
ftp                      3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
mail                     3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
pop                      3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
smtp                     3600    AAAA    2a01:07c8:aac0:01cd:0000:0000:0000:0001
```

---

## 📧 **Email blijft werken!**

✅ ronald@klantfans.nl blijft werken  
✅ Webmail blijft werken op oude server  
✅ Versturen/ontvangen blijft werken  

**Alleen de WEBSITE gaat naar Vercel!**

---

## ⏱️ **Verwachte doorlooptijd:**

- DNS wijzigingen: **Direct actief**
- Wereldwijde propagatie: **5-30 minuten**
- Maximale propagatie: **24-48 uur**

---

## 🔍 **Controleren of het werkt:**

### **Optie 1: Browser**
```
https://klantfans.nl
https://www.klantfans.nl
```

### **Optie 2: Terminal**
```bash
# Check A-record
dig klantfans.nl A +short
# Moet tonen: 76.76.21.21

# Check CNAME
dig www.klantfans.nl CNAME +short
# Moet tonen: cname.vercel-dns.com
```

### **Optie 3: Online tool**
```
https://dnschecker.org
```
Vul in: `klantfans.nl` en check of het IP `76.76.21.21` toont

---

## 🚨 **Troubleshooting**

### **Website laadt niet na 30 minuten:**
1. Check of DNS wijzigingen correct zijn in control panel
2. Check Vercel dashboard of domain correct is geconfigureerd
3. Wacht nog 1-2 uur (DNS cache)

### **Email werkt niet meer:**
1. Check of MX record nog steeds `10 web50.i24.host.` is
2. Check of mail/pop/smtp A-records nog `37.97.166.105` zijn
3. Herstel indien nodig

### **SSL certificaat fout:**
1. Wacht 5-10 minuten (Vercel genereert automatisch SSL)
2. Check Vercel dashboard → Domains → SSL status

---

## 📞 **Contact**

Bij problemen:
- Vercel Support: https://vercel.com/support
- i24.host Support: (voor email problemen)

---

**Succes! 🚀**

