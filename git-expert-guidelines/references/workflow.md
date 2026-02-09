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
git add src/auth
git commit -m "fix(auth): handle null token"
git add src/styles
git commit -m "style: reformat css"
```

---

## 2) Conventional Commits (MEDIUM)

Format obligatoire : `type(scope): description`
**Langue : FRANÇAIS uniquement.**

### Types
- `feat`: Nouvelle fonctionnalité
- `fix`: Correction de bug
- `docs`: Documentation
- `style`: Formatage (espaces, ;...)
- `refactor`: Ni fix ni feature
- `test`: Tests
- `chore`: Build, deps

### Structure
```text
type(scope): description courte (max 50)

[Body optionnel] Pourquoi & Comment (max 72/ligne)

[Footer optionnel] Fixes #123
```

### Exemple Correct
```text
fix(auth): gestion du token null

Ajout d'une vérification pour éviter la boucle 500.

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
