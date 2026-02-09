# Workflow & Qualité
> *Processus stricts pour un historique propre et atomique.*

## 1) Commits Atomiques (HIGH)

Un commit = **UN** changement logique. Ne jamais mélanger fix, style et feature.

### Incorrect
```bash
git commit -m "Fix login, reformat css and update readme"
```

### Correct
```bash
# Exemple conceptuel (la commande réelle nécessite plusieurs lignes ou un éditeur)
git add src/auth
git commit
# [Dans l'éditeur]
# fix(auth): handle null token
#
# Added a null check to prevent the 500 error loop.
#
# Fixes #142
```

---

## 2) Conventional Commits (CRITICAL)

**Interdits formels** :
- Commits en une seule ligne (`git commit -m "dazodjz"`)
- Message sans contexte ou explication.

**Langue : FRANÇAIS uniquement.**

### Structure OBLIGATOIRE
```text
type(scope): description courte (max 50)

[Body OBLIGATOIRE] Pourquoi & Comment (max 72/ligne)

[Footer OBLIGATOIRE] Fixes #123
```

### Types
- `feat`: Nouvelle fonctionnalité
- `fix`: Correction de bug
- `docs`: Documentation
- `style`: Formatage (espaces, ;...)
- `refactor`: Ni fix ni feature
- `test`: Tests
- `chore`: Build, deps

### Exemple Correct
```text
fix(auth): gestion du token null

Ajout d'une vérification pour éviter la boucle 500 lors du refresh.
La logique précédente ne gérait pas le cas où le token expirait.

Fixes #142
```

---

## 3) Feature Branches (HIGH)

Pas de travail direct sur `main` / `master`. Toujours une branche dédiée.

### Incorrect
```bash
git checkout main
# coding...
git commit
```

### Correct
```bash
git checkout main
git pull origin main
git checkout -b feat/nouvelle-page
# ou
git checkout -b fix/auth-bug
```
