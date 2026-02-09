---
title: Redirects et Migrations
impact: HIGH
tags: redirects, migrations, 301, 308, golden-set
---

# Redirects et Migrations

Règles pour gérer les changements d'URL sans casser le web.

---

## 1. Status codes HTTP

| Code | Usage | Préservation méthode |
|------|-------|---------------------|
| **308** | Redirect permanent | ✅ POST reste POST |
| **307** | Redirect temporaire | ✅ POST reste POST |
| **301** | Legacy permanent | ❌ Devient GET |
| **302** | Legacy temporaire | ❌ Devient GET |
| **404** | Page inexistante | - |
| **410** | Page supprimée définitivement | - |

> **Next.js** : utilise 307/308 par défaut (`permanent: true` = 308).

---

## 2. Règles de redirect

### Obligatoire

Toute URL publique modifiée → redirect permanent.

```typescript
// next.config.ts
async redirects() {
  return [
    {
      source: '/old-path/:slug',
      destination: '/new-path/:slug',
      permanent: true, // 308
    },
  ]
}
```

### Wildcard pour renamings massifs

```typescript
{
  source: '/old-scope/:path*',
  destination: '/new-scope/:path*',
  permanent: true,
}
```

---

## 3. Interdiction des chaînes

❌ **Interdit**
```
/a → /b → /c → /d
```

✅ **Correct**
```
/a → /d
/b → /d
/c → /d
```

Toujours pointer directement vers la destination finale.

---

## 4. Soft-404 interdit

Ne jamais rediriger vers une page non équivalente.

❌ **Interdit**
```
/article-supprime → / (homepage)
/product-old → /blog (autre section)
```

✅ **Correct**
```
/article-supprime → 410 Gone
/product-old → /product-new (équivalent)
```

---

## 5. Golden Set

### Définition

Liste d'URLs critiques à tester après chaque migration.

### Contenu

| URL | Status attendu | Destination | Canonical |
|-----|----------------|-------------|-----------|
| /old-pricing | 308 | /pricing | - |
| /pricing | 200 | - | /pricing |
| /deleted-page | 410 | - | - |
| /legacy/docs | 308 | /docs | - |

### Checklist migration

- [ ] Toutes les URLs du golden set testées
- [ ] Status codes corrects (200/308/410)
- [ ] Destinations exactes vérifiées
- [ ] Pas de chaînes de redirects
- [ ] Canonical correct sur pages 200
- [ ] Sitemap mis à jour
- [ ] Liens internes mis à jour

---

## 6. Sunset d'un scope

### Processus

1. **Annonce** : communication + header `Sunset`
2. **Période de transition** : redirects actifs
3. **Archivage** : contenu déplacé ou 410
4. **Nettoyage** : suppression des anciennes routes

```typescript
// Exemple redirect sunset
{
  source: '/deprecated-scope/:path*',
  destination: '/new-scope/:path*',
  permanent: true,
}
```

---

## 7. Différence redirect/rewrite/proxy

| Type | URL navigateur | Serveur | SEO |
|------|----------------|---------|-----|
| **Redirect** | Change | Nouvelle URL | Transfert de juice |
| **Rewrite** | Inchangée | URL différente | Attention duplicate |
| **Proxy** | Inchangée | Serveur différent | Transparent |

> **Règle** : redirect pour migrations SEO, rewrite pour masquage interne uniquement.

---

## Interdits (hard bans)

- Supprimer une URL sans redirect ni 410
- Chaînes de redirects
- Redirect massif vers homepage (soft-404)
- Mélanger redirect et rewrite pour même URL
- Migration sans golden set testé
