# 🔌 Frontend Integratie - Vercel Website

Hoe je de Strapi CMS data gebruikt in je Vercel website.

---

## 📡 API Endpoints

Na deployment zijn deze endpoints beschikbaar:

```
https://cms.klantfans.nl/api/cases?populate=*
https://cms.klantfans.nl/api/hero?populate=*
https://cms.klantfans.nl/api/stats?sort=volgorde:asc
https://cms.klantfans.nl/api/client-logos?populate=*&sort=volgorde:asc
```

**Let op:** Voeg altijd `?populate=*` toe voor media velden!

---

## 🔧 Optie 1: Client-Side Fetch (Simpel)

Voeg dit JavaScript toe aan je `index.html`:

### Cases ophalen en weergeven

```javascript
// Fetch cases from CMS
async function loadCases() {
    try {
        const response = await fetch('https://cms.klantfans.nl/api/cases?populate=*&sort=volgorde:asc');
        const data = await response.json();
        
        if (data.data && data.data.length > 0) {
            renderCases(data.data);
        }
    } catch (error) {
        console.error('Fout bij ophalen cases:', error);
        // Fallback: gebruik hardcoded cases
    }
}

function renderCases(cases) {
    const casesContainer = document.querySelector('#cases .grid');
    casesContainer.innerHTML = ''; // Clear existing
    
    cases.forEach(caseItem => {
        const attrs = caseItem.attributes;
        const caseCard = `
            <div class="bg-white rounded-xl overflow-hidden shadow-lg hover:shadow-2xl transition-all duration-300 border-l-4 border-${attrs.borderKleur}-500 flex flex-col transform hover:-translate-y-1">
                <div class="bg-gradient-to-r from-${attrs.brancheKleur}-50 to-white p-6 flex-grow">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-2xl font-bold text-navy">${attrs.clientNaam}</h3>
                        <span class="text-3xl">${attrs.emoji}</span>
                    </div>
                    <p class="text-xs font-bold text-${attrs.brancheKleur}-600 mb-4 uppercase tracking-wider inline-block bg-${attrs.brancheKleur}-100 px-3 py-1 rounded-full">${attrs.branche}</p>
                    <div class="text-gray-700 mb-3 leading-relaxed">${attrs.probleem}</div>
                    <div class="text-gray-700 leading-relaxed">${attrs.resultaat}</div>
                </div>
            </div>
        `;
        casesContainer.insertAdjacentHTML('beforeend', caseCard);
    });
}

// Laad cases bij page load
document.addEventListener('DOMContentLoaded', loadCases);
```

### Hero content ophalen

```javascript
async function loadHero() {
    try {
        const response = await fetch('https://cms.klantfans.nl/api/hero?populate=*');
        const data = await response.json();
        
        if (data.data) {
            const hero = data.data.attributes;
            
            // Update hero teksten
            document.querySelector('#hero h1').textContent = hero.titel;
            document.querySelector('#hero .subtitel').textContent = hero.subtitel;
            document.querySelector('#hero .cta-button').textContent = hero.ctaTekst;
            
            // Update profielfoto
            if (hero.profielfoto && hero.profielfoto.data) {
                const foto = hero.profielfoto.data.attributes;
                document.querySelector('#hero img').src = `https://cms.klantfans.nl${foto.url}`;
            }
            
            // Update naam en functie
            document.querySelector('#hero h2').textContent = hero.naam;
            document.querySelector('#hero .functie').textContent = hero.functie;
        }
    } catch (error) {
        console.error('Fout bij ophalen hero:', error);
    }
}
```

### Stats ophalen

```javascript
async function loadStats() {
    try {
        const response = await fetch('https://cms.klantfans.nl/api/stats?sort=volgorde:asc');
        const data = await response.json();
        
        if (data.data && data.data.length > 0) {
            const statsContainer = document.querySelector('#stats-row');
            statsContainer.innerHTML = '';
            
            data.data.forEach(stat => {
                const attrs = stat.attributes;
                const statHtml = `
                    <div class="stat-item">
                        <span class="stat-value">${attrs.waarde}</span>
                        <span class="stat-label">${attrs.label}</span>
                    </div>
                `;
                statsContainer.insertAdjacentHTML('beforeend', statHtml);
            });
        }
    } catch (error) {
        console.error('Fout bij ophalen stats:', error);
    }
}
```

---

## 🚀 Optie 2: Build-Time Generation (Sneller)

Voor betere performance kun je de data ophalen tijdens Vercel build:

### 1. Maak een API helper

Voeg toe aan je repo: `lib/strapi.js`

```javascript
const STRAPI_URL = process.env.NEXT_PUBLIC_STRAPI_URL || 'https://cms.klantfans.nl';

export async function fetchAPI(path, options = {}) {
    const defaultOptions = {
        headers: {
            'Content-Type': 'application/json',
        },
    };

    const mergedOptions = {
        ...defaultOptions,
        ...options,
    };

    const response = await fetch(`${STRAPI_URL}/api${path}`, mergedOptions);

    if (!response.ok) {
        throw new Error(`API call failed: ${response.status}`);
    }

    return response.json();
}

export async function getCases() {
    const data = await fetchAPI('/cases?populate=*&sort=volgorde:asc');
    return data.data;
}

export async function getHero() {
    const data = await fetchAPI('/hero?populate=*');
    return data.data;
}

export async function getStats() {
    const data = await fetchAPI('/stats?sort=volgorde:asc');
    return data.data;
}
```

### 2. Gebruik in je page

Als je Next.js wilt gebruiken (recommended voor performance):

```javascript
import { getCases, getHero, getStats } from '../lib/strapi';

export async function getStaticProps() {
    const [cases, hero, stats] = await Promise.all([
        getCases(),
        getHero(),
        getStats(),
    ]);

    return {
        props: {
            cases,
            hero,
            stats,
        },
        revalidate: 60, // Revalidate elke 60 seconden
    };
}

export default function Home({ cases, hero, stats }) {
    return (
        <div>
            <Hero data={hero} />
            <Stats data={stats} />
            <Cases data={cases} />
        </div>
    );
}
```

---

## 🔄 Optie 3: Incremental Static Regeneration (ISR)

Voor de beste combinatie van snelheid én freshness:

```javascript
export async function getStaticProps() {
    const cases = await getCases();

    return {
        props: {
            cases,
        },
        revalidate: 300, // Herbouw elke 5 minuten als er traffic is
    };
}
```

---

## 🪝 Optie 4: Webhooks (Auto-rebuild)

Laat Strapi automatisch je Vercel site rebuilden bij content wijzigingen:

### In Strapi:
1. Ga naar **Settings** → **Webhooks** → **Create new webhook**
2. Configuratie:
   - Name: `Vercel Deploy`
   - URL: `https://api.vercel.com/v1/integrations/deploy/<your-hook-id>`
   - Events: `entry.create`, `entry.update`, `entry.delete`, `entry.publish`, `entry.unpublish`

### Vercel Deploy Hook:
1. Ga in Vercel naar je project → **Settings** → **Git** → **Deploy Hooks**
2. Maak nieuwe hook: `strapi-content-update`
3. Kopieer de URL
4. Plak in Strapi webhook

Nu wordt je site automatisch gerebuild bij elke content wijziging! 🎉

---

## 🖼️ Images/Uploads ophalen

Strapi uploads zijn beschikbaar via:

```javascript
// Als je een media field hebt in je content type:
const imageUrl = `https://cms.klantfans.nl${item.attributes.image.data.attributes.url}`;

// Of voor thumbnails:
const thumbnailUrl = `https://cms.klantfans.nl${item.attributes.image.data.attributes.formats.thumbnail.url}`;
```

**Tip:** Voeg altijd `?populate=*` toe aan je API calls om media te krijgen!

---

## 🔐 Optionele: API Token gebruiken

Voor extra security kun je een API token gebruiken:

### In Strapi:
1. Settings → API Tokens → Create new token
2. Type: Read-only
3. Kopieer de token

### In Vercel:
1. Settings → Environment Variables
2. Voeg toe: `STRAPI_API_TOKEN=<your-token>`

### In je code:
```javascript
const response = await fetch('https://cms.klantfans.nl/api/cases?populate=*', {
    headers: {
        'Authorization': `Bearer ${process.env.STRAPI_API_TOKEN}`,
    },
});
```

---

## 📝 Response Format

Strapi geeft data in dit format:

```json
{
  "data": [
    {
      "id": 1,
      "attributes": {
        "clientNaam": "RegioBank",
        "emoji": "🏦",
        "branche": "Finance",
        "probleem": "...",
        "resultaat": "...",
        "volgorde": 1,
        "createdAt": "2025-01-01T00:00:00.000Z",
        "updatedAt": "2025-01-01T00:00:00.000Z",
        "publishedAt": "2025-01-01T00:00:00.000Z"
      }
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 25,
      "pageCount": 1,
      "total": 5
    }
  }
}
```

---

## 🎯 Aanbevolen Implementatie

Voor jouw situatie raad ik aan:

1. ✅ **Start met Client-Side Fetch** (Optie 1) - eenvoudig toe te voegen aan huidige HTML
2. ⏭️ Later migreer naar **Next.js + ISR** (Optie 3) - beste performance
3. ⏭️ Voeg **Webhooks** toe (Optie 4) - voor auto-updates

---

## 🧪 Testen

Test je API endpoints:

```bash
# Cases
curl https://cms.klantfans.nl/api/cases?populate=*

# Hero
curl https://cms.klantfans.nl/api/hero?populate=*

# Stats
curl https://cms.klantfans.nl/api/stats
```

---

**Klaar om te integreren!** 🚀

Voor vragen: ronald@klantfans.nl

