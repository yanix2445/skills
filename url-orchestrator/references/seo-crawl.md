---
title: SEO, Crawl et Canonical
impact: HIGH
tags: seo, canonical, robots, sitemap, indexation
---

# SEO, Crawl et Canonical

Règles pour contrôler l'indexation, les canoniques et le comportement des crawlers.

---

## 1. Canonical côté serveur

**Redirect > balise canonical.** Si tu peux rediriger, fais-le.

| Situation | Action |
|-----------|--------|
| Variante accessible | Redirect 308 vers canonique |
| Impossible de redirect | Balise `<link rel="canonical">` |

❌ **Interdit**
```
/Pulse-CRM/docs       → 200 (mauvais casing)
/pulse-crm/docs/      → 200 (trailing slash)
```

✅ **Correct**
```
/Pulse-CRM/docs       → 308 → /pulse-crm/docs
/pulse-crm/docs/      → 308 → /pulse-crm/docs
```

---

## 2. Policy d'indexation

| Type de route | index | follow | Cache |
|---------------|-------|--------|-------|
| Public (marketing) | ✅ | ✅ | public, max-age |
| Contenu (blog/docs) | ✅ | ✅ | public, stale-while-revalidate |
| Privé (app/dashboard) | ❌ | ❌ | no-store |
| Technique (api/preview) | ❌ | ❌ | no-store |
| Erreurs (404/500) | ❌ | ❌ | no-cache |

### Implémentation Next.js

```typescript
// Route publique
export const metadata: Metadata = {
  robots: { index: true, follow: true }
}

// Route privée
export const metadata: Metadata = {
  robots: { index: false, follow: false }
}
```

---

## 3. Query params

### Politique

| Type de param | Traitement |
|---------------|-----------|
| Tracking (utm_*) | Ignorer pour canonical |
| Filtres | Ignorer ou noindex si combinatoire |
| Pagination | Canonical vers page de base |
| Critiques (id, token) | Inclure dans canonical |

❌ **Interdit dans les liens internes**
```
/pulse-crm/pricing?utm_source=internal
```

---

## 4. robots.txt

### Template

```typescript
// app/robots.ts
import type { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/api/', '/admin/', '/preview/', '/app/', '/_next/'],
    },
    sitemap: 'https://example.com/sitemap.xml',
  }
}
```

---

## 5. sitemap.xml

### Règles

- Inclure : pages indexables uniquement
- Exclure : privées, noindex, preview, paginées
- Mettre à jour : lastmod réel, pas fake

```typescript
// app/sitemap.ts
import type { MetadataRoute } from 'next'

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const baseUrl = 'https://example.com'
  
  return [
    { url: baseUrl, lastModified: new Date(), priority: 1 },
    { url: `${baseUrl}/pulse-crm/marketing/pricing`, priority: 0.8 },
    // ... pages dynamiques depuis DB
  ]
}
```

---

## 6. Preview et drafts

| Règle | Détail |
|-------|--------|
| noindex | Toujours |
| Cache | no-store |
| robots.txt | Disallow /preview/ |
| Auth | Token ou session obligatoire |

---

## Interdits (hard bans)

- Page 200 non canonique accessible au crawl
- Sitemap avec pages noindex
- robots.txt en contradiction avec policy d'indexation
- Canonical cross-domain sans raison
- Preview indexable
