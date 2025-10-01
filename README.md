# KlantFans CMS

Headless CMS gebouwd met Strapi voor de KlantFans website.

## 🚀 Deployment op Dokploy

### Stap 1: Maak een nieuwe app in Dokploy

1. Log in op je Dokploy dashboard
2. Klik op **"Create Application"**
3. Kies **"Git Repository"**
4. Vul in:
   - Repository: `https://github.com/Cooper3513CS9/klantfans-cms`
   - Branch: `main`
   - Application naam: `klantfans-cms`

### Stap 2: Configureer Database

1. Maak een PostgreSQL database aan in Dokploy
2. Noteer de database credentials:
   - Host
   - Port
   - Database naam
   - Username
   - Password

### Stap 3: Environment Variables

Voeg deze environment variables toe in Dokploy:

```bash
# Server
NODE_ENV=production
HOST=0.0.0.0
PORT=1337

# Database
DATABASE_CLIENT=postgres
DATABASE_HOST=<jouw-postgres-host>
DATABASE_PORT=5432
DATABASE_NAME=<jouw-db-naam>
DATABASE_USERNAME=<jouw-db-username>
DATABASE_PASSWORD=<jouw-db-password>
DATABASE_SSL=false

# Secrets (genereer met: openssl rand -base64 32)
APP_KEYS=<genereer-unieke-key>
API_TOKEN_SALT=<genereer-unieke-key>
ADMIN_JWT_SECRET=<genereer-unieke-key>
TRANSFER_TOKEN_SALT=<genereer-unieke-key>
JWT_SECRET=<genereer-unieke-key>

# Public URL
PUBLIC_URL=https://cms.klantfans.nl
```

### Stap 4: Domein Configuratie

1. Voeg subdomain toe: `cms.klantfans.nl`
2. Wijs deze toe aan je Strapi app in Dokploy
3. Dokploy regelt automatisch SSL via Let's Encrypt

### Stap 5: Deploy

1. Klik op **"Deploy"** in Dokploy
2. Wacht tot de build compleet is (~5 minuten)
3. Open `https://cms.klantfans.nl/admin`
4. Maak je eerste admin account aan

---

## 📚 Content Types

Dit CMS bevat de volgende content types:

### 1. **Hero Section** (`hero`)
- Titel (text)
- Subtitel (text)
- CTA Tekst (text)
- Profielfoto (media)
- Naam (text)
- Functie (text)
- Bio (richtext)

### 2. **Stats** (`stat`)
- Label (text) - bijv. "Jaar Ervaring"
- Waarde (text) - bijv. "25+"
- Volgorde (number)

### 3. **Cases** (`case`) ⭐
- Client Naam (text)
- Emoji/Icon (text)
- Branche (text)
- Branche Kleur (enumeration: blue, red, green, yellow, purple)
- Probleem (richtext)
- Resultaat (richtext)
- Border Kleur (enumeration: blue, red, green, yellow, purple)
- Featured (boolean)
- Volgorde (number)

### 4. **Client Logos** (`client-logo`)
- Logo (media)
- Client Naam (text)
- Alt Text (text)
- Link URL (text, optional)
- Volgorde (number)

### 5. **Feature Cards** (`feature-card`)
- Titel (text)
- Beschrijving (richtext)
- Emoji/Icon (text)
- Type (enumeration: probleem, oplossing, usp)
- Volgorde (number)

### 6. **Awards** (`award`)
- Naam (text)
- Beschrijving (text)
- Jaar (number)
- Volgorde (number)

### 7. **Contact/CTA** (`contact-cta`)
- Titel (text)
- Beschrijving (text)
- CTA Button Tekst (text)
- CTA Button Link (text)

### 8. **Footer** (`footer`)
- Copyright Tekst (text)
- KvK Nummer (text)
- BTW Nummer (text)

---

## 🔌 API Gebruik

### Public API (voor frontend)

Alle content is beschikbaar via REST API:

```
GET https://cms.klantfans.nl/api/cases?populate=*
GET https://cms.klantfans.nl/api/hero?populate=*
GET https://cms.klantfans.nl/api/stats
GET https://cms.klantfans.nl/api/client-logos?populate=*
```

### API Token

Voor productie gebruik:
1. Ga naar Strapi admin: Settings → API Tokens
2. Maak een Read-Only token aan
3. Gebruik deze in je Vercel frontend

---

## 🛠️ Lokale Development

```bash
# Install dependencies
npm install

# Start development server (met SQLite)
npm run develop

# Admin panel beschikbaar op:
# http://localhost:1337/admin
```

---

## 📝 Volgende Stappen na Deployment

1. ✅ Maak admin account aan
2. ✅ Configureer API permissions (Settings → Users & Permissions → Public)
3. ✅ Upload alle bestaande content
4. ✅ Maak API token voor frontend
5. ✅ Update frontend om data van CMS te halen

---

## 🔒 Security

- Admin panel is beschermd met JWT
- Database credentials in environment variables
- CORS geconfigureerd voor alleen klantfans.nl domein
- API tokens voor productie gebruik

---

## 📞 Support

Voor vragen of problemen: ronald@klantfans.nl

