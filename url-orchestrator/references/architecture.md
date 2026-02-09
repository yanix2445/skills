---
title: Architecture d'URL - Règles fondamentales
impact: CRITICAL
tags: architecture, routing, pattern, urls, scalability
---

# Architecture d'URL

Règles fondamentales pour concevoir une architecture d'URL stable et scalable.



## 1. Pattern unique obligatoire

**Un seul pattern par root.** Dès que tu as 2 structures parallèles, tu crées du duplicate content, des liens cassés et des migrations ingérables.

### Patterns autorisés (choisir 1)

| Pattern | Format | Quand l'utiliser |
|---------|--------|------------------|
| A (simple) | `/{scope}/{page}` | Pas de sections distinctes |
| B (avec channel) | `/{scope}/{channel}/{page}` | Plusieurs sections (docs/blog/support/marketing) |

❌ **Interdit**
```
/pulse-crm/pricing              # Pattern A
/docs/pulse-crm/getting-started  # Pattern B mixé
```

✅ **Correct**
```
/pulse-crm/marketing/pricing
/pulse-crm/docs/getting-started
```



## 2. Vocabulaire de routing

| Terme | Définition | Exemple |
|-------|------------|---------|
| **root** | Domaine de base | `https://example.com` |
| **scope** | Namespace principal (produit/activité) | `pulse-crm`, `dev-web` |
| **channel** | Section fonctionnelle | `docs`, `blog`, `support`, `marketing` |
| **page** | Slug final | `pricing`, `getting-started` |


## 3. Profondeur maximale

| Type | Profondeur max | Exemple |
|------|----------------|---------|
| Public | 3 segments | `/{scope}/{channel}/{page}` |
| Contenu | 4 segments | `/{scope}/{channel}/{collection}/{slug}` |

❌ **À éviter**
```
/produits/saas/pulse-crm/app/dashboard/pages/home
```

✅ **Correct**
```
/pulse-crm/app/dashboard
```


## 4. Pages système vs contenu dynamique

| Type | Caractéristique | Gestion |
|------|-----------------|---------|
| **Pages système** | Stables, contrôlées | Dictionnaire fixe |
| **Pages contenu** | Dynamiques, extensibles | Slugs via CMS/DB |

✅ **Dictionnaire système** (exemple)
```
/legal/privacy
/legal/terms
/support/contact
```


## 5. Multi-roots (path vs subdomain)

### Option A — `path` (recommandé)
```
https://example.com/{scope}/{channel}/{page}
```
- SEO centralisé
- Analytics simples
- Moins de config

### Option B — `subdomain`
```
https://app.example.com/...
https://docs.example.com/...
```
- Isolation technique
- Déploiements séparés
- Sécurité/auth différente

> **Règle** : Une fois choisi, ne jamais mixer sans migration planifiée.


## 6. Pages d'index obligatoires

Chaque scope/channel doit avoir une page d'index navigable.

```
/{scope}/           → Index du scope
/{scope}/{channel}/ → Index du channel
```


## Interdits (hard bans)

- Chemins "meta" inutiles : `/activite/page/...`
- Versions dans l'URL : `/v1/`, `-v2`, `-2026`
- Mélange pattern avec/sans channel sur même root
- Segments redondants : `/products/saas/marketing/pages/landing/pricing`
