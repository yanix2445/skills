---
name: url-orchestrator
description: Gouvernance d'URL multi-scopes (produits/activités) pour sites modernes, avec canonisation serveur, SEO/crawl, migrations safe, et implémentation Next.js App Router. À utiliser quand tu conçois, refactors, ou migres des routes/URLs (pages publiques/privées/techniques) pour garder un pattern stable, scalable et testable.
---

# URL Orchestrator

Guide de gouvernance pour standardiser les URLs d'un écosystème multi-sites/multi-produits.

## Quand l'appliquer

- Créer/refactor un **routing** Next.js App Router
- Concevoir une **arborescence** (`/{scope}/{channel}/{page}`)
- Renommer des slugs, déplacer des pages, migrer un domaine
- Mettre en place **canonical**, `robots`, `sitemap`, `noindex`
- Ajouter des **routes techniques** (API, webhooks, preview)
- Débugger des problèmes **404 / redirects / soft-404 / duplication SEO**

## Fichiers de référence

| Fichier | Contenu | Impact |
|---------|---------|--------|
| [architecture.md](references/architecture.md) | Pattern unique, vocabulaire, profondeur, multi-roots | CRITICAL |
| [governance.md](references/governance.md) | Slugs, channels, collisions, URL builder | HIGH |
| [seo-crawl.md](references/seo-crawl.md) | Canonical, indexation, robots, sitemap | HIGH |
| [migrations.md](references/migrations.md) | Redirects, status codes, golden set, sunset | HIGH |
| [nextjs-patterns.md](references/nextjs-patterns.md) | Exemples TypeScript App Router | HIGH |
| [extensions.md](references/extensions.md) | API, webhooks, downloads, cache, preview | MEDIUM |

## Résumé des règles (CORE)

### Architecture (CRITICAL)

- **Un seul pattern** : `/{scope}/{channel}/{page}` partout
- **Profondeur max** : 3 segments (public), 4 (contenu)
- **Pages d'index** : obligatoires pour chaque scope/channel
- **Multi-roots** : choisir path ou subdomain, ne pas mixer

### Gouvernance (HIGH)

- **Slugs** : kebab-case, minuscules, pas d'accents/underscores
- **Channels whitelistés** : pas de synonymes (docs ≠ documentation)
- **Mots réservés** : système > contenu
- **URL Builder** : génération centralisée, jamais de concat

### SEO/Crawl (HIGH)

- **Canonical serveur** : redirect > balise
- **Policy indexation** : public=index, privé=noindex
- **robots.txt + sitemap** : alignés avec policy
- **Preview** : noindex + no-store

### Migrations (HIGH)

- **308 permanent** : pour tout changement d'URL
- **Pas de chaînes** : pointer direct vers destination finale
- **Golden set** : tester URLs critiques après chaque migration
- **Soft-404 interdit** : redirect vers équivalent ou 410

### Next.js (HIGH)

Voir [nextjs-patterns.md](references/nextjs-patterns.md) pour :
- **Proxy (ex-Middleware)** : `proxy.ts`, redirects/rewrites
- **Advanced Routing** : Parallel (`@folder`), Intercepting (`(.)folder`)
- **Async Params** : `params` et `searchParams` (Promesses Next.js 15+)
- **Typed Routes** : Configuration et typage des liens (Next.js 16)
- **Dynamic Segments** : `[slug]`, `[...slug]`, `[[...slug]]`
- **SEO/Metadata** : `generateMetadata`, `robots.ts`, `sitemap.ts`
- **Validation** : `notFound()`, `redirects()` next.config.js

### Extensions (MEDIUM)

- **API** : `/api/{scope}/{resource}`, versioning par header
- **Webhooks** : auth obligatoire, noindex
- **Preview** : noindex, no-store, auth
- **Downloads** : publics vs privés séparés
