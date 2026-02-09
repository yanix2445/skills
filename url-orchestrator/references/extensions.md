---
title: Extensions (API, webhooks, downloads, cache)
impact: MEDIUM
tags: api, webhooks, downloads, cache, preview
---

# Extensions

Règles pour les routes techniques : API, webhooks, downloads, preview, cache.

---

## 1. Routes API

### Séparation stricte

```
Web:  /{scope}/{channel}/{page}
API:  /api/{scope}/{resource}
```

❌ **Interdit** : mixer API et contenu dans mêmes channels.

### Versioning

| Stratégie | Recommandation |
|-----------|----------------|
| Header (Accept-Version) | ✅ Préférée |
| URL (/api/v1/) | ⚠️ Si nécessaire |

Si URL versioning :
- Ne pas versionner tout par réflexe
- v1 supportée jusqu'à date de sunset annoncée
- Documentation obligatoire

### Stabilité

- Ne jamais casser un endpoint sans période de transition
- Dépréciation : header `Deprecation` + docs
- Compat serveur > redirect HTTP

---

## 2. Webhooks et callbacks

### Règles

| Règle | Détail |
|-------|--------|
| Pattern stable | `/api/{scope}/webhooks/{provider}` |
| noindex | Toujours |
| Auth | Signature/token obligatoire |
| Logs | Tracer tous les appels |

```
/api/pulse-crm/webhooks/stripe
/api/pulse-crm/callbacks/oauth
```

---

## 3. Preview et drafts

| Règle | Valeur |
|-------|--------|
| noindex | ✅ Obligatoire |
| Cache | no-store |
| robots.txt | Disallow |
| Auth | Token/session requis |
| URL | `/preview/{token}` ou query param |

---

## 4. Downloads et exports

### Séparation

| Type | Pattern | Indexable |
|------|---------|-----------|
| Public (PDF, assets) | `/downloads/{file}` | ✅ Si pertinent |
| Privé (exports user) | `/api/{scope}/exports/{id}` | ❌ |

### Règles

- Exports privés = auth obligatoire
- Pas d'indexation des fichiers générés
- URLs stables pour assets publics

---

## 5. Cache-Control

| Type de route | Cache-Control |
|---------------|---------------|
| Public static | `public, max-age=31536000, immutable` |
| Public dynamic | `public, max-age=0, stale-while-revalidate=86400` |
| Privé | `private, no-store` |
| API | `no-store` ou `private, max-age=60` |
| Preview | `no-store` |

---

## 6. Shortlinks et vanity URLs

### Shortlinks (liens entrants)

```
/go/{code} → redirect vers destination
```

- Pour campagnes/tracking
- Redirect permanent vers canonique
- Ne pas polluer l'architecture principale

### Vanity URLs

```
/pricing → redirect → /pulse-crm/marketing/pricing
```

- Alias courts pour marketing
- Toujours redirect vers canonique
- Pas de contenu dupliqué

---

## 7. Status et health

### Routes techniques standard

```
/health          → liveness check
/health/ready    → readiness check
/_status         → status page
```

| Règle | Détail |
|-------|--------|
| noindex | ✅ |
| Auth | Optionnel (basic auth si sensible) |
| Stable | Même path sur tous les roots |

---

## Interdits (hard bans)

- API endpoints dans channels publics
- Webhooks sans auth
- Preview indexable
- Exports privés accessibles sans auth
- Casser un endpoint API sans transition
