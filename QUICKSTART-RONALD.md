# 🚀 KlantFans CMS - Jouw Deployment Checklist

**Voor:** Ronald Beute  
**Situatie:** Je hebt al Zentrack project in Dokploy met PostgreSQL  
**Doel:** KlantFans CMS toevoegen (hergebruik bestaande PostgreSQL)

---

## ✅ **Stap 1: Nieuwe Database Aanmaken**

### In Dokploy:
1. Open: http://91.98.144.128:3000/dashboard
2. Ga naar je **PostgreSQL database** (die Zentrack gebruikt)
3. Klik op **"Console"** of **"Shell"**
4. Voer uit:

```sql
CREATE DATABASE klantfans_cms;
```

✅ **Klaar!** Je hebt nu 2 databases in dezelfde PostgreSQL:
- `zentrack` (bestaand)
- `klantfans_cms` (nieuw) ← Volledig gescheiden!

---

## ✅ **Stap 2: Nieuwe Applicatie Toevoegen**

### In Dokploy Dashboard:
1. Ga naar je project: **L9REkZF75ricHLGMlmatv**
2. Klik **"+ Add Service"** of **"Create Application"**
3. Kies: **"Git Repository"**

### Configuratie:
```
Application Name: klantfans-cms
Repository URL: https://github.com/Cooper3513CS9/klantfans-cms
Branch: main
Build Type: Docker
Dockerfile Path: ./Dockerfile
```

---

## ✅ **Stap 3: Environment Variables**

### In de applicatie → Environment tab, voeg toe:

```bash
# Server
NODE_ENV=production
HOST=0.0.0.0
PORT=1337

# Database (hergebruik Zentrack PostgreSQL!)
DATABASE_CLIENT=postgres
DATABASE_HOST=postgres
DATABASE_PORT=5432
DATABASE_NAME=klantfans_cms
DATABASE_USERNAME=<zelfde-als-zentrack>
DATABASE_PASSWORD=<zelfde-als-zentrack>
DATABASE_SSL=false

# Secrets (al gegenereerd voor jou)
APP_KEYS=4trWnlVx0nDJPGO91wah2GeLkM9BsYOiOprR/3Sro/o=
API_TOKEN_SALT=68Ex9UsIg8hAU6XPhhVScslELTaXjIdQymjQ3nRAce4=
ADMIN_JWT_SECRET=gOrHp737LJZf9pUKQGspXKteDyLDPaeti2YpyElqlCY=
TRANSFER_TOKEN_SALT=Ql3Xr4uxhqU518zu6WRFx8F6NW9XqpDJiKtmEGBu/bI=
JWT_SECRET=aeRaWXnH9OcC5P/zrKyEomgaM8tBXvTxXoX7P1HKYoM=

# Public URL
PUBLIC_URL=https://cms.klantfans.nl
ADMIN_URL=/admin
```

### ⚠️ **Pas aan:**
- `DATABASE_USERNAME` → Vul in met Zentrack PostgreSQL user
- `DATABASE_PASSWORD` → Vul in met Zentrack PostgreSQL password
- `DATABASE_HOST` → Check in Zentrack config hoe deze heet (waarschijnlijk `postgres`)

---

## ✅ **Stap 4: Domein Toevoegen**

### In de applicatie → Domains tab:
1. Klik **"Add Domain"**
2. Vul in: `cms.klantfans.nl`
3. Enable **SSL** (Let's Encrypt)
4. Save

### DNS Check:
Zorg dat in je DNS (waar klantfans.nl staat):
```
Type: A
Name: cms
Value: 91.98.144.128
TTL: 3600
```

---

## ✅ **Stap 5: Deploy!**

1. Klik op **"Deploy"** knop
2. Volg de build logs (duurt ~5-10 minuten)
3. Wacht tot status = **"Running"**

---

## ✅ **Stap 6: Admin Account Aanmaken**

### Wanneer deployment klaar is:
1. Open: **https://cms.klantfans.nl/admin**
2. Je ziet: Welkom scherm voor eerste admin
3. Vul in:
   - Voornaam: **Ronald**
   - Achternaam: **Beute**
   - Email: **ronald@klantfans.nl**
   - Wachtwoord: *(kies sterk wachtwoord)*
4. Klik **"Let's Start"**

🎉 **Je bent nu in je CMS!**

---

## ✅ **Stap 7: API Permissions Instellen**

### In Strapi Admin Panel:
1. Ga naar **Settings** (tandwiel icoon linksonder)
2. **Users & Permissions** → **Roles** → **Public**
3. Voor elk content type, enable:
   - ✅ `find` (lijst ophalen)
   - ✅ `findOne` (enkel item)
4. Klik **"Save"**

**Content types om te enablen:**
- Case
- Hero
- Stat
- Client Logo

---

## ✅ **Stap 8: Content Toevoegen**

### Je eerste Case toevoegen:
1. **Content Manager** (links in menu) → **Cases**
2. Klik **"Create new entry"**
3. Vul in (voorbeeld RegioBank):
   ```
   Client Naam: RegioBank
   Emoji: 🏦
   Branche: Finance
   Branche Kleur: blue
   Border Kleur: blue
   Volgorde: 1
   
   Probleem:
   Een onbekend B2B-label transformeren naar de meest 
   klantvriendelijke B2B2C-bank van Nederland, met 540 
   franchisers als cruciaal kanaal.
   
   Resultaat:
   Klantwaardering van 6,9 → 9,7. NPS van negatief → +17. 
   Naamsbekendheid van 31% → 76%.
   ```
4. Klik **"Save"**
5. Klik **"Publish"**

**Herhaal voor alle 5 cases:**
- RegioBank (Finance, blue, 🏦)
- Feyenoord (Topsport, red, ⚽)
- Star-shl (Healthcare, green, 🏥)
- Newton Energy (Scale-up Energy, yellow, ⚡)
- B2B Diensten (Abonnementen, purple, 💼)

---

## ✅ **Stap 9: Test API**

### In terminal of browser:
```bash
curl https://cms.klantfans.nl/api/cases?populate=*
```

Je zou JSON moeten zien met je cases! 🎉

---

## 📊 **Jouw Setup:**

```
┌─────────────────────────────────┐
│   Hetzner Server                │
│   91.98.144.128                 │
│                                 │
│   ┌─────────────────────────┐   │
│   │  Dokploy                │   │
│   │  :3000                  │   │
│   │                         │   │
│   │  ┌──────────────────┐   │   │
│   │  │ Zentrack         │   │   │
│   │  │ (bestaand)       │   │   │
│   │  └──────────────────┘   │   │
│   │                         │   │
│   │  ┌──────────────────┐   │   │
│   │  │ KlantFans CMS    │   │   │
│   │  │ cms.klantfans.nl │   │   │ ← NIEUW!
│   │  └──────────────────┘   │   │
│   │           │             │   │
│   │           ↓             │   │
│   │  ┌──────────────────┐   │   │
│   │  │ PostgreSQL       │   │   │
│   │  │ - zentrack       │   │   │ ← Bestaand
│   │  │ - klantfans_cms  │   │   │ ← Nieuw
│   │  └──────────────────┘   │   │
│   └─────────────────────────┘   │
└─────────────────────────────────┘
```

---

## 🔍 **Troubleshooting**

### "Cannot connect to database"
- Check of DATABASE_HOST correct is (waarschijnlijk `postgres`)
- Verify DATABASE_USERNAME en PASSWORD (zelfde als Zentrack)

### "502 Bad Gateway"
- Wacht 1-2 minuten (SSL certificate genereert)
- Check build logs in Dokploy
- Verify PORT=1337 in environment variables

### Admin panel niet bereikbaar
- Check DNS: `dig cms.klantfans.nl` → moet 91.98.144.128 zijn
- Check SSL certificate status in Dokploy
- Wacht 1-2 minuten na deployment

### API geeft 404
- Check of content **Published** is (niet alleen Saved)
- Verify API permissions (Stap 7)
- Test: `curl https://cms.klantfans.nl/api/cases`

---

## 📝 **Next Steps (Later)**

Na je CMS draait:

1. ⏭️ Alle 5 cases overzetten naar CMS
2. ⏭️ Hero content invullen
3. ⏭️ Stats toevoegen (25+ jaar, 9.7 score)
4. ⏭️ Client logos uploaden
5. ⏭️ Frontend integreren (zie FRONTEND-INTEGRATIE.md)
6. ⏭️ Webhook instellen voor auto-rebuild Vercel

---

## 🎯 **Deployment Checklist**

Print deze uit en vink af tijdens deployment:

- [ ] Database `klantfans_cms` aangemaakt in bestaande PostgreSQL
- [ ] Nieuwe applicatie toegevoegd in Dokploy
- [ ] Git repo gekoppeld: `Cooper3513CS9/klantfans-cms`
- [ ] Alle Environment Variables ingevuld
- [ ] DATABASE_USERNAME & PASSWORD van Zentrack gebruikt
- [ ] Domein `cms.klantfans.nl` toegevoegd met SSL
- [ ] DNS A-record aangemaakt: cms → 91.98.144.128
- [ ] Deploy knop geklikt
- [ ] Build logs gevolgd (5-10 min)
- [ ] Status = "Running"
- [ ] https://cms.klantfans.nl/admin bereikbaar
- [ ] Admin account aangemaakt
- [ ] API permissions ingesteld (Public role)
- [ ] Eerste case toegevoegd en gepubliceerd
- [ ] API test succesvol: `curl https://cms.klantfans.nl/api/cases`

---

## 📞 **Hulp Nodig?**

**Volledige documentatie:**
- [README.md](./README.md) - Project overzicht
- [DOKPLOY.md](./DOKPLOY.md) - Uitgebreide deployment guide
- [FRONTEND-INTEGRATIE.md](./FRONTEND-INTEGRATIE.md) - API gebruik in Vercel site

**Contact:**
- Email: ronald@klantfans.nl
- GitHub: https://github.com/Cooper3513CS9/klantfans-cms

---

## 💾 **Backup Dit Bestand!**

Kopieer deze checklist naar je notities, dan kun je hem later rustig doorlopen!

**Veel succes met de deployment!** 🚀

---

*Laatste update: 1 januari 2025*  
*Dokploy Server: 91.98.144.128:3000*  
*Project ID: L9REkZF75ricHLGMlmatv*

