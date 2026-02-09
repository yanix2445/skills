---
title: Gouvernance des slugs et anti-collisions
impact: HIGH
tags: slugs, naming, normalization, collisions, governance
---

# Gouvernance des slugs

Règles de normalisation, mots réservés et gestion des collisions.

---

## 1. Normalisation des slugs

### Règles obligatoires

| Règle | Exemple ❌ | Exemple ✅ |
|-------|-----------|-----------|
| Kebab-case | `DevWeb`, `dev_web` | `dev-web` |
| Pas d'accents | `développement` | `developpement` |
| Pas d'underscore | `pulse_crm` | `pulse-crm` |
| Minuscules | `Pricing` | `pricing` |
| Pas de caractères spéciaux | `hello@world` | `hello-world` |

### Trailing slash

Choisir **une seule** convention et l'appliquer partout :
- Sans slash (recommandé) : `/pulse-crm/docs`
- Avec slash : `/pulse-crm/docs/`

> Variante non canonique → redirect permanent.

---

## 2. Channels autorisés

### Liste whitelist (exemple)

| Channel | Usage |
|---------|-------|
| `marketing` | Pages produit, landing |
| `docs` | Documentation |
| `blog` | Articles |
| `support` | Help center |
| `legal` | Mentions, CGV, privacy |
| `app` | Dashboard (privé) |
| `api` | Endpoints API |

❌ **Interdit** : synonymes (`documentation`, `help`, `guide`)

---

## 3. Mots réservés

### Réservés globaux (jamais comme slug de contenu)

```
api, app, admin, auth, login, logout, signup, register,
dashboard, settings, account, profile, billing,
preview, draft, staging, test, dev,
static, assets, public, files, uploads,
health, status, robots, sitemap, favicon
```

### Collision policy

**Système > Contenu** : un slug système gagne toujours sur un slug de contenu.

```
/pulse-crm/docs/api      → ❌ Si "api" est réservé
/pulse-crm/docs/api-guide → ✅ Alternative
```

---

## 4. URL Builder centralisé

Tous les liens internes doivent passer par une fonction unique.

❌ **Interdit**
```typescript
const url = "/" + scope + "/docs/" + page
```

✅ **Correct**
```typescript
const url = buildUrl({ scope, channel: 'docs', page })
```

### Avantages
- Normalisation automatique
- Pas de variantes accidentelles
- Un seul endroit à modifier

---

## 5. Ressources : ID vs slug

### Stratégie recommandée : `{id}-{slug}`

```
/pulse-crm/blog/posts/123-mon-article
```

| Avantage | Détail |
|----------|--------|
| Stabilité | ID ne change jamais |
| SEO | Slug lisible |
| Renommage | Redirect propre si slug change |

---

## 6. Liste vs détail

| Type | Pattern | Exemple |
|------|---------|---------|
| Liste | `/{scope}/{channel}` | `/pulse-crm/blog` |
| Détail | `/{scope}/{channel}/{slug}` | `/pulse-crm/blog/mon-article` |

❌ **Éviter** : `/blog/list`, `/blog/all`, `/blog/index`

---

## Interdits (hard bans)

- Inventer un casing différent par channel
- Underscores dans les URLs publiques
- Mélanger slugs réservés et contenu
- Concaténer les URLs à la main dans le code
