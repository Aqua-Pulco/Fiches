Voici la **version ultra-courte**.

# .gitignore — mémo minimal

## 1) Créer le fichier (à la racine du projet)

```bash
# au choix
touch .gitignore              # crée le fichier vide
# ou ouvre-le dans VS Code et colle le contenu ci-dessous
```

**Contenu minimal à coller :**

```
# secrets
.env

# dépendances
node_modules/

# builds (si tu en as)
dist/
build/
```

## 2) Enregistrer et committer

```bash
git add .gitignore
git commit -m "chore: add minimal .gitignore"
git push
```

---

## Si tu as DÉJÀ pushé des trucs (ex: node\_modules, .env)

```bash
git rm -r --cached node_modules   # retire node_modules de l'index (pas du disque)
git rm --cached .env              # retire .env de l'index
git commit -m "chore: stop tracking node_modules and .env"
git push
```

👉 Si `.env` a été poussé une fois, **change** les mots de passe/tokens exposés (ex: reset sur Neon).
