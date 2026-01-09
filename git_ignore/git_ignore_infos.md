
Si `.env` a été poussé une fois, **change** les mots de passe/tokens exposés (ex: reset sur Neon).

Un motif simple comme
`node_modules`
veut dire : ignore **tout fichier ou dossier nommé `node_modules`**, n’importe où dans le projet.

Si tu écris
`node_modules/`
tu dis explicitement : ignore **le dossier** `node_modules` **et tout ce qu’il contient**. Les sous-dossiers et fichiers sont automatiquement couverts. Tu n’as rien à préciser de plus.

Un chemin avec un slash au début, par exemple
`/dist/`
signifie : ignore **le dossier `dist` uniquement à la racine du projet**, pas un `dist` dans un sous-dossier.

Un joker `*` signifie “n’importe quelle suite de caractères”.
Par exemple
`*.log`
ignore **tous les fichiers qui finissent par `.log`**, peu importe le dossier.

`**` traverse les dossiers récursivement.
Par exemple
`**/cache/`
ignore **tous les dossiers nommés `cache`, partout, à n’importe quelle profondeur**.

On peut aussi faire l’inverse : ré-inclure quelque chose avec `!`.
Par exemple :
`dist/`
`!dist/README.md`
→ tout `dist` est ignoré **sauf** `README.md`.

**`.gitignore` n’agit que sur les fichiers qui ne sont pas encore suivis par Git.**
Si un fichier est déjà “tracké”, Git continuera de le suivre même si tu l’ajoutes au `.gitignore`. Il faut alors le retirer de l’index avec `git rm --cached`.

`git rm` = enlever le fichier de Git et du disque.
`git rm --cached` = enlever le fichier de Git seulement, mais le laisser sur ton disque.

**Contenu minimal à coller :**

```
# secrets
.env

# dépendances
node_modules/

# builds (si ils existent)
dist/
build/
```
## Si tu as DÉJÀ pushé des trucs (ex: node\_modules, .env)

```bash
git rm -r --cached node_modules   # retire node_modules de l'index (pas du disque)
git rm --cached .env              # retire .env de l'index
git commit -m "chore: stop tracking node_modules and .env"
git push
```

