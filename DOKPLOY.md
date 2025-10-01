# 🚀 KlantFans CMS - Dokploy Deployment Guide

Complete stap-voor-stap instructies om dit Strapi CMS te deployen op Dokploy.

---

## ✅ Vereisten

- Dokploy geïnstalleerd en draaiend op je Hetzner server
- GitHub account met toegang tot deze repository
- Subdomain DNS record: `cms.klantfans.nl` → je Hetzner server IP

---

## 📝 Stap 1: Database Setup

### ✅ Optie A: Bestaande PostgreSQL hergebruiken (AANBEVOLEN)

**Heb je al een PostgreSQL container in Dokploy? Perfect!**

1. Log in op je Dokploy dashboard
2. Ga naar je **bestaande PostgreSQL database**
3. Klik op **"Console"** of **"Shell"**
4. Voer uit:
   ```sql
   CREATE DATABASE klantfans_cms;
   ```
5. Klaar! Noteer je bestaande PostgreSQL gegevens:
   - **Internal Host**: (bijv. `postgres` of naam van je container)
   - **Port**: `5432`
   - **Database Name**: `klantfans_cms` ← Nieuwe database
   - **Username**: (je bestaande PostgreSQL user)
   - **Password**: (je bestaande PostgreSQL wachtwoord)

**Waarom deze optie?**
- ✅ Geen extra container nodig
- ✅ Minder resources
- ✅ Eenvoudiger beheer
- ✅ PostgreSQL is gebouwd voor meerdere databases
- ✅ 100% veilig gescheiden van je andere databases

---

### Optie B: Nieuwe PostgreSQL container aanmaken

**Wil je toch een aparte PostgreSQL?**

1. Ga naar **"Databases"** → **"Create Database"**
2. Kies **"PostgreSQL"**
3. Configuratie:
   - **Name**: `klantfans-cms-db`
   - **PostgreSQL Version**: `16`
   - **Database Name**: `strapi`
   - **Username**: `strapi`
   - **Password**: Kies een sterk wachtwoord (bijv. genereer via: `openssl rand -base64 32`)
4. Klik **"Create"**
5. **Noteer** de volgende gegevens:
   - Internal Host (bijv. `klantfans-cms-db`)
   - Port (`5432`)
   - Database naam (`strapi`)
   - Username (`strapi`)
   - Password (je gekozen wachtwoord)

---

## 🔐 Stap 2: Secrets Genereren

Open een terminal en genereer 5 unieke secrets:

```bash
openssl rand -base64 32
openssl rand -base64 32
openssl rand -base64 32
openssl rand -base64 32
openssl rand -base64 32
```

Noteer deze 5 strings, je hebt ze nodig voor de environment variables.

---

## 🚢 Stap 3: Applicatie aanmaken in Dokploy

1. Ga in Dokploy naar **"Applications"** → **"Create Application"**
2. Kies **"Git Repository"**
3. Configuratie:
   - **Application Name**: `klantfans-cms`
   - **Git Provider**: GitHub
   - **Repository**: `Cooper3513CS9/klantfans-cms` (of jouw fork)
   - **Branch**: `main`
   - **Build Type**: Docker
   - **Dockerfile Path**: `./Dockerfile`

---

## ⚙️ Stap 4: Environment Variables configureren

In de applicatie settings, ga naar **"Environment"** en voeg toe:

### Server Settings
```
NODE_ENV=production
HOST=0.0.0.0
PORT=1337
```

### Database Settings

**Als je Optie A koos (bestaande PostgreSQL):**
```
DATABASE_CLIENT=postgres
DATABASE_HOST=<naam-van-je-bestaande-postgres-container>  # bijv. "postgres"
DATABASE_PORT=5432
DATABASE_NAME=klantfans_cms                               # De nieuwe database
DATABASE_USERNAME=<je-bestaande-postgres-user>
DATABASE_PASSWORD=<je-bestaande-postgres-password>
DATABASE_SSL=false
```

**Als je Optie B koos (nieuwe PostgreSQL):**
```
DATABASE_CLIENT=postgres
DATABASE_HOST=klantfans-cms-db
DATABASE_PORT=5432
DATABASE_NAME=strapi
DATABASE_USERNAME=strapi
DATABASE_PASSWORD=<jouw-nieuwe-db-password>
DATABASE_SSL=false
```

### Secrets (gebruik de gegenereerde secrets uit Stap 2)
```
APP_KEYS=<secret1>
API_TOKEN_SALT=<secret2>
ADMIN_JWT_SECRET=<secret3>
TRANSFER_TOKEN_SALT=<secret4>
JWT_SECRET=<secret5>
```

### Public URL
```
PUBLIC_URL=https://cms.klantfans.nl
ADMIN_URL=/admin
```

---

## 🌐 Stap 5: Domein configureren

1. In de applicatie settings, ga naar **"Domains"**
2. Klik **"Add Domain"**
3. Voer in: `cms.klantfans.nl`
4. Zet **"Enable SSL"** aan (Let's Encrypt)
5. Klik **"Save"**

**DNS Check:** Zorg dat `cms.klantfans.nl` in je DNS naar je Hetzner server IP wijst:
```
Type: A
Name: cms
Value: <jouw-hetzner-ip>
TTL: 3600
```

---

## 🏗️ Stap 6: Deploy!

1. Klik op **"Deploy"** knop
2. Volg de build logs:
   - Dockerfile wordt gebouwd
   - Dependencies worden geïnstalleerd
   - Strapi admin panel wordt gebouwd
3. Wacht ~5-10 minuten voor de eerste deployment

---

## ✨ Stap 7: Eerste Admin Account aanmaken

1. Wanneer deployment klaar is, open: `https://cms.klantfans.nl/admin`
2. Je ziet het **Welkom scherm**
3. Maak je eerste admin account aan:
   - Voornaam: Ronald
   - Achternaam: Beute
   - Email: ronald@klantfans.nl
   - Wachtwoord: Kies een sterk wachtwoord
4. Klik **"Let's Start"**

🎉 **Je CMS is nu live!**

---

## 📚 Stap 8: API Permissions configureren

Voor de frontend moet je de API public maken:

1. Ga in Strapi admin naar **Settings** → **Users & Permissions** → **Roles** → **Public**
2. Voor elk content type dat je wilt exposeren, enable:
   - ✅ `find` (lijst ophalen)
   - ✅ `findOne` (enkel item ophalen)
3. Klik **"Save"**

Nu zijn je API endpoints publiek beschikbaar:
- `https://cms.klantfans.nl/api/cases?populate=*`
- `https://cms.klantfans.nl/api/hero?populate=*`
- `https://cms.klantfans.nl/api/stats`
- etc.

---

## 🔑 Stap 9: API Token voor Frontend (optioneel)

Voor betere security kun je een Read-Only API token maken:

1. Ga naar **Settings** → **API Tokens** → **Create new API Token**
2. Configuratie:
   - Name: `Frontend Read Token`
   - Token type: `Read-only`
   - Duration: `Unlimited`
3. Klik **"Save"**
4. **Kopieer** de token (je ziet 'm maar 1x!)
5. Voeg deze toe aan je Vercel environment als: `STRAPI_API_TOKEN`

---

## 📦 Stap 10: Content toevoegen

Nu kun je content gaan toevoegen via het Strapi admin panel!

### Cases toevoegen
1. Ga naar **Content Manager** → **Cases** → **Create new entry**
2. Vul in:
   - Client Naam: bijv. "RegioBank"
   - Emoji: 🏦
   - Branche: "Finance"
   - Branche Kleur: blue
   - Probleem: (copy from current site)
   - Resultaat: (copy from current site)
   - Border Kleur: blue
   - Volgorde: 1
3. Klik **"Save"** en **"Publish"**

Herhaal dit voor alle 5 je huidige cases!

---

## 🔄 Automatische Deploys

Dokploy is nu gekoppeld aan je GitHub repo. Elke keer dat je iets pusht naar `main` branch, wordt automatisch een nieuwe deployment getriggerd.

---

## 🐛 Troubleshooting

### Database connection errors
- Check of PostgreSQL container draait in Dokploy
- Verify DATABASE_HOST naam (moet internal hostname zijn)
- Check DATABASE_PASSWORD

### 502 Bad Gateway
- Check build logs in Dokploy
- Verify PORT is 1337
- Check of container is gestart

### Admin panel niet bereikbaar
- Verify SSL certificate is gegenereerd (kan 1-2 min duren)
- Check DNS propagatie: `dig cms.klantfans.nl`

### Upload errors
- Check disk space in Dokploy
- Verify volume mounts in docker-compose

---

## 📞 Volgende Stappen

1. ✅ CMS is live op `https://cms.klantfans.nl/admin`
2. ⏭️ Voeg alle content toe vanuit je huidige site
3. ⏭️ Update je Vercel frontend om data van CMS te halen
4. ⏭️ Test API endpoints
5. ⏭️ Configureer webhooks voor auto-rebuild (optioneel)

---

## 💾 Backups

Dokploy maakt automatisch backups van je PostgreSQL database. Check:
- **Databases** → **klantfans-cms-db** → **Backups**

Je kunt ook handmatig een backup maken of een schedule instellen.

---

## 📝 Notities

- Admin panel: `https://cms.klantfans.nl/admin`
- API base URL: `https://cms.klantfans.nl/api`
- Strapi versie: 4.25.12
- Database: PostgreSQL 16
- Node: 18

---

**Succes! 🚀** 

Voor vragen: ronald@klantfans.nl

