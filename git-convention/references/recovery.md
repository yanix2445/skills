# Recovery & Corrections
> *Réparer sans tout casser.*

## 1) Amend (MEDIUM)

Pour modifier le **dernier** commit (message ou fichier oublié) **avant** de push.

### Incorrect
Faire un commit "oops correction".

### Correct
```bash
git add fichier-oublie.js
git commit --amend --no-edit
# Fusionne les modifs dans le commit précédent
```

---

## 2) Undo Local (reset/restore) (HIGH)

### Désindexer (Undo `add`)
```bash
git restore --staged <fichier>
```

### Annuler modifications (Undo changes)
⚠️ Destructif : revient à l'état du dernier commit.
```bash
git restore <fichier>
```
