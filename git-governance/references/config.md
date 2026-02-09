# Config & Environnement
> *Configuration locale pour éviter les fuites et erreurs d'attribution.*

## 1) Identité Utilisateur (HIGH)

Vérifier `user.name` et `user.email` localement pour chaque projet.

### Vérification
```bash
git config user.name
git config user.email
```

### Configuration Locale
```bash
git config user.name "Votre NOM"
git config user.email "votre.email@pro.com"
```

---

## 2) Gitignore Exhaustif (HIGH)

Ne jamais committer :
- Secrets (`.env`, clées)
- Dépendances (`node_modules/`, `vendor/`)
- Artefacts (`dist/`, `build/`, `.DS_Store`)

### Exemple .gitignore
```gitignore
node_modules/
dist/
.env
.DS_Store
.vscode/
coverage/
```
