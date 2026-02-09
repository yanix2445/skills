# Security & Integrity
> *Garde-fous critiques pour protéger le dépôt et l'historique.*

## 1) Vérification avant Action (CRITICAL)

Ne jamais ajouter "tout" (`git add .`) aveuglément sans vérifier le statut.

### Incorrect
```bash
# Risque d'ajouter .env, build, tmp
git add .
git commit -m "update"
```

### Correct
```bash
git status
# Analyser. Si OK :
git add .
# Ou mieux :
git add src/
```

---

## 2) Pas de Force Push (CRITICAL)

Interdit sur branches protégées (`main`, `master`, `develop`, `prod`).

### Incorrect
```bash
git push -f origin main
```

### Correct
Si absolument nécessaire sur **votre** branche de feature (ex: après un rebase/amend) :
```bash
git push --force-with-lease origin feat/ma-branche
```
