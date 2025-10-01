# KlantFans CMS

Headless CMS gebouwd met Strapi voor de KlantFans website.

## 🎯 Wat is dit?

Dit is een **Strapi 4 headless CMS** waarmee je alle content van je KlantFans website kunt beheren:
- ✏️ Cases (portfolio items)
- 📝 Hero content (homepage teksten)
- 📊 Stats (statistieken)
- 🖼️ Client logos

**Voordelen:**
- Content aanpassen zonder code
- Foto's en logo's uploaden via admin panel
- Cases toevoegen/bewerken/verwijderen
- Professionele REST API voor je frontend

---

## 🚀 Deployment op Dokploy - SNELSTART

### ⚡ Je hebt al PostgreSQL? Perfect!

Je hoeft **geen nieuwe PostgreSQL** aan te maken! Hergebruik gewoon je bestaande.

#### Stap 1: Maak alleen een nieuwe database

In Dokploy → Ga naar je bestaande PostgreSQL → **Console**:

```sql
CREATE DATABASE klantfans_cms;
```

Klaar! Je hebt nu een aparte database voor dit CMS binnen je bestaande PostgreSQL.

#### Stap 2: Deploy Strapi in Dokploy

1. **Applications** → **Create Application**
2. **Git Repository**:
   - Repository: `Cooper3513CS9/klantfans-cms`
   - Branch: `main`
   - Application naam: `klantfans-cms`

#### Stap 3: Environment Variables

**Genereer eerst 5 secrets:**
```bash
openssl rand -base64 32  # Doe dit 5x
```

**Voeg toe in Dokploy:**

```bash
# Server
NODE_ENV=production
HOST=0.0.0.0
PORT=1337

# Database (gebruik je bestaande PostgreSQL!)
DATABASE_CLIENT=postgres
DATABASE_HOST=postgres                    # ← Naam van je bestaande container
DATABASE_PORT=5432
DATABASE_NAME=klantfans_cms              # ← Nieuwe database die je net maakte
DATABASE_USERNAME=<jouw-postgres-user>   # ← Bestaande user
DATABASE_PASSWORD=<jouw-postgres-pass>   # ← Bestaande wachtwoord
DATABASE_SSL=false

# Secrets (gebruik de gegenereerde keys)
APP_KEYS=<secret-1>
API_TOKEN_SALT=<secret-2>
ADMIN_JWT_SECRET=<secret-3>
TRANSFER_TOKEN_SALT=<secret-4>
JWT_SECRET=<secret-5>

# Public URL
PUBLIC_URL=https://cms.klantfans.nl
ADMIN_URL=/admin
```

#### Stap 4: Domein & Deploy

1. **Domains** → Add: `cms.klantfans.nl` (Enable SSL)
2. Klik **Deploy**
3. Wacht 5-10 minuten
4. Open: `https://cms.klantfans.nl/admin`
5. Maak je admin account aan

🎉 **Klaar!**

---

## 📚 Volledige Documentatie

Voor uitgebreide stap-voor-stap instructies:

### 📖 [DOKPLOY.md](./DOKPLOY.md)
Complete deployment guide met alle details, troubleshooting en configuratie opties.

### 🔌 [FRONTEND-INTEGRATIE.md](./FRONTEND-INTEGRATIE.md)
Hoe je de CMS data gebruikt in je Vercel website, met API voorbeelden en code snippets.

---

## 🗄️ Database Opties

### Optie A: Bestaande PostgreSQL hergebruiken ✅ **AANBEVOLEN**

**Waarom?**
- ✅ Minder resources
- ✅ Eenvoudiger beheer
- ✅ PostgreSQL is gebouwd voor meerdere databases
- ✅ 100% veilig gescheiden (databases kunnen elkaar niet zien)

**Hoe?**
- Maak alleen nieuwe database: `CREATE DATABASE klantfans_cms;`
- Gebruik bestaande PostgreSQL container in DATABASE_HOST
- Klaar!

### Optie B: Eigen PostgreSQL container

Als je toch een aparte wilt (niet nodig, maar kan):

```bash
# Gebruik dan docker-compose.yml uit deze repo
docker-compose up -d
```

Dit start zowel Strapi als een nieuwe PostgreSQL container.

---

## 📊 Content Types

Dit CMS bevat de volgende content types:

### 1. **Cases** (`/api/cases`)
Portfolio items met:
- Client naam
- Emoji/icon (🏦 ⚽ 🏥 etc)
- Branche + kleur
- Probleem beschrijving (richtext)
- Resultaat beschrijving (richtext)
- Border kleur
- Featured flag
- Volgorde

**Voorbeeld API:**
```
GET https://cms.klantfans.nl/api/cases?populate=*&sort=volgorde:asc
```

### 2. **Hero Section** (`/api/hero`)
Homepage content:
- Titel
- Subtitel
- CTA button tekst
- Profielfoto (upload)
- Naam
- Functie
- Bio (richtext)

**Voorbeeld API:**
```
GET https://cms.klantfans.nl/api/hero?populate=*
```

### 3. **Stats** (`/api/stats`)
Statistieken row:
- Label (bijv. "Jaar Ervaring")
- Waarde (bijv. "25+")
- Volgorde

**Voorbeeld API:**
```
GET https://cms.klantfans.nl/api/stats?sort=volgorde:asc
```

### 4. **Client Logos** (`/api/client-logos`)
Client logo grid:
- Client naam
- Logo upload
- Alt text (voor SEO)
- Link URL (optioneel)
- Volgorde

**Voorbeeld API:**
```
GET https://cms.klantfans.nl/api/client-logos?populate=*&sort=volgorde:asc
```

---

## 🔐 API Toegang

### Publieke API (default)

Na deployment moet je API permissions instellen:

1. Strapi admin → **Settings** → **Users & Permissions** → **Roles** → **Public**
2. Enable voor elk content type:
   - ✅ `find` (lijst ophalen)
   - ✅ `findOne` (enkel item)
3. Save

Nu zijn je API endpoints publiek toegankelijk.

### API Token (optioneel, extra veilig)

Voor productie kun je een Read-Only token maken:

1. **Settings** → **API Tokens** → **Create new token**
2. Type: `Read-only`
3. Kopieer token
4. Gebruik in je frontend:

```javascript
fetch('https://cms.klantfans.nl/api/cases?populate=*', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
})
```

---

## 🛠️ Lokale Development

```bash
# Clone repository
git clone https://github.com/Cooper3513CS9/klantfans-cms.git
cd klantfans-cms

# Install dependencies
npm install

# Copy env example
cp .env.example .env

# Edit .env met je lokale database credentials

# Start development server (gebruikt SQLite als DATABASE_CLIENT niet postgres is)
npm run develop

# Admin panel beschikbaar op:
# http://localhost:1337/admin
```

---

## 🏗️ Architectuur

```
┌─────────────────────────────────┐
│   Vercel Frontend               │
│   klantfans-website.vercel.app  │  ← Blijft zoals nu
└──────────────┬──────────────────┘
               │ API Calls
               ↓
┌─────────────────────────────────┐
│   Strapi CMS (Dokploy)          │
│   cms.klantfans.nl              │  ← Nieuw!
└──────────────┬──────────────────┘
               │
               ↓
┌─────────────────────────────────┐
│   PostgreSQL (Dokploy)          │
│   Database: klantfans_cms       │  ← Nieuwe database
└─────────────────────────────────┘
     (in bestaande PostgreSQL)
```

**Waarom deze setup?**
- Frontend op Vercel = Super snel, gratis CDN
- CMS op Dokploy = Volledige controle, self-hosted
- Bestaande PostgreSQL = Efficiënt, eenvoudig beheer

---

## 📁 Project Structuur

```
klantfans-cms/
├── config/                  # Strapi configuratie
│   ├── database.js         # PostgreSQL setup
│   ├── server.js           # Server settings
│   ├── admin.js            # Admin panel config
│   ├── middlewares.js      # CORS & security
│   └── api.js              # API settings
├── src/
│   └── api/                # Content Types
│       ├── case/           # Cases (portfolio)
│       ├── hero/           # Hero section
│       ├── stat/           # Stats row
│       └── client-logo/    # Client logos
├── public/
│   └── uploads/            # Uploaded files
├── Dockerfile              # Production build
├── docker-compose.yml      # Local development (met eigen PostgreSQL)
├── package.json            # Dependencies
├── .env.example            # Environment template
├── DOKPLOY.md             # 📚 Complete deployment guide
├── FRONTEND-INTEGRATIE.md # 🔌 API integratie voorbeelden
└── README.md              # Dit bestand
```

---

## 🔄 Content Workflow

### 1. Content toevoegen in Strapi
```
Admin Panel → Cases → Create new entry → Save → Publish
```

### 2. Data verschijnt in API
```
GET https://cms.klantfans.nl/api/cases?populate=*
```

### 3. Frontend haalt data op
```javascript
// In je Vercel website
const response = await fetch('https://cms.klantfans.nl/api/cases?populate=*');
const data = await response.json();
// Render cases dynamisch
```

### 4. Automatische rebuild (optioneel)
Strapi webhook → Vercel Deploy Hook → Site rebuilds automatisch

---

## 🚦 Na Deployment Checklist

- [ ] ✅ CMS draait op `https://cms.klantfans.nl/admin`
- [ ] ✅ Admin account aangemaakt
- [ ] ✅ API permissions ingesteld (Public role)
- [ ] ✅ Eerste case toegevoegd en gepubliceerd
- [ ] ✅ API test: `curl https://cms.klantfans.nl/api/cases`
- [ ] ⏭️ Alle huidige cases overgezet
- [ ] ⏭️ Client logos geüpload
- [ ] ⏭️ Hero content ingevuld
- [ ] ⏭️ Frontend integratie (zie FRONTEND-INTEGRATIE.md)
- [ ] ⏭️ Webhook voor auto-rebuild (optioneel)

---

## 🔒 Security

- ✅ Admin panel beschermd met JWT
- ✅ Database credentials in environment variables
- ✅ CORS geconfigureerd voor alleen klantfans.nl domein
- ✅ SSL via Let's Encrypt (auto in Dokploy)
- ✅ API tokens voor productie gebruik
- ✅ Rate limiting enabled
- ✅ Geen .env in Git (via .gitignore)

---

## 🐛 Troubleshooting

### "Cannot connect to database"
- Check DATABASE_HOST naam (moet je PostgreSQL container naam zijn)
- Verify DATABASE_PASSWORD
- Check of PostgreSQL container draait

### "404 Not Found" op API endpoints
- Check of content is **Published** (niet alleen Saved)
- Verify API permissions in Strapi (Public role)
- Check URL: moet `/api/cases` zijn, niet `/cases`

### Admin panel laadt niet
- Check SSL certificate (kan 1-2 min duren na eerste deploy)
- Verify PUBLIC_URL in environment variables
- Check browser console voor errors

### Uploads werken niet
- Check disk space in Dokploy
- Verify `public/uploads/` directory heeft write permissions
- Check max upload size in Strapi settings

Voor meer troubleshooting: zie [DOKPLOY.md](./DOKPLOY.md)

---

## 📦 Dependencies

- **Strapi**: 4.25.12 (headless CMS)
- **PostgreSQL**: 16 (database, hergebruik bestaande!)
- **Node.js**: 18-20 (runtime)
- **Docker**: Voor deployment

---

## 🔄 Updates

Om Strapi te updaten:

```bash
npm install @strapi/strapi@latest
npm run build
git commit -am "Update Strapi"
git push
```

Dokploy zal automatisch redeployen.

---

## 💾 Backups

### Database Backups
Dokploy maakt automatisch backups van je PostgreSQL:
- **Databases** → `klantfans_cms` → **Backups**

### Handmatige Backup
```bash
# In Dokploy PostgreSQL console
pg_dump -U strapi klantfans_cms > backup.sql
```

### Content Export
Via Strapi admin:
- **Settings** → **Transfer Tokens** → Export content als JSON

---

## 🌐 Useful Links

- 📖 [Strapi Documentation](https://docs.strapi.io/)
- 🚀 [Dokploy Documentation](https://dokploy.com/docs)
- 🎓 [Strapi Tutorials](https://strapi.io/blog)
- 💬 [Strapi Community](https://discord.strapi.io/)

---

## 📞 Support

**Voor vragen over dit project:**
- Email: ronald@klantfans.nl
- GitHub Issues: [Open een issue](https://github.com/Cooper3513CS9/klantfans-cms/issues)

**Voor Strapi vragen:**
- [Strapi Discord](https://discord.strapi.io/)
- [Strapi Forum](https://forum.strapi.io/)

---

## 📝 License

Dit project is private en eigendom van KlantFans.

---

## 🎉 Ready to Deploy!

1. 📖 Lees [DOKPLOY.md](./DOKPLOY.md) voor volledige instructies
2. 🚀 Deploy in Dokploy (20 min)
3. ✏️ Voeg content toe via admin panel
4. 🔌 Integreer met frontend (zie [FRONTEND-INTEGRATIE.md](./FRONTEND-INTEGRATIE.md))
5. 🎊 Geniet van je headless CMS!

**Veel succes!** 💪
