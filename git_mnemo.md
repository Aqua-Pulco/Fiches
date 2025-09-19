
## Mnémo-techniques (retenir sans forcer)

### Dépôt (repo) = “valise + carnet”
- Image : ton **projet = valise**, le **.git = carnet** à l’intérieur qui note tout.
- À retenir : si tu copies juste les vêtements (ZIP), tu perds le carnet → toujours **clone**.

### Remote (origin) = “adresse postale”
- Image : `origin` = **adresse** sur l’étiquette de ta valise (GitHub).
- Usage : `git remote -v` = “relire l’adresse avant d’envoyer le colis”.

### Branche = “voie de train”
- Image : chaque **branche** = **voie**. `main` = voie principale, `feature/...` = voie parallèle.
- Usage : `git switch -c nouvelle-voie` pour créer une voie propre.

### Commit = “photo datée”
- Image : un **commit** = **photo** + **légende** (message).
- Usage : `git add -A` (rassembler les objets), `git commit -m "legende"` (prendre la photo).

### Fast-forward = “avancer la tête de lecture”
- Image : tu **avances la cassette** jusqu’à la même piste que le serveur, **sans** mixer de nouvelles pistes.
- Usage : `git pull --ff-only` = “avance sans créer un mix (merge)”.

### Merge auto parasite = “nœud inutile”
- Image : un **merge auto** non voulu = **nœud** dans l’historique pour rien.
- Prévention : `pull.ff=only` empêche Git de faire ce nœud dans ton dos.

### Upstream = “flèche ↗ de référence”
- Image : ta branche locale a une **flèche** qui pointe vers **la branche distante qu’elle suit** (ex. `origin/main`).
- Usage : `git push -u origin ma-branche` dessine la flèche une fois → ensuite `git push/pull` **sans arguments**.

### `push.autoSetupRemote` = “flèche auto”
- Image : au **premier push d’une nouvelle branche**, Git dessine **la flèche tout seul**.
- Effet : plus besoin du `-u` la première fois.

### Rebase = “rejouer la scène au bon endroit”
- Image : tu as filmé 3 scènes pendant que le film officiel a avancé. **Rebase** = **recoller tes scènes à la fin du film**, proprement.
- Usage : `git pull --rebase origin main` quand le fast-forward refuse.

### Fetch vs Pull = “rapporter vs intégrer”
- Image : **fetch** = “je **rapporte** les infos du serveur” (mais je ne les mélange pas).  
  **pull** = fetch **+** “j’**intègre**”.
- Usage : `git fetch --all --prune` pour **voir** d’abord, `git pull --ff-only` pour **intégrer** proprement.

### Status/Index/Worktree = “atelier en 3 zones”
- Image : **worktree** (atelier) = tes fichiers qui bougent ; **index** (table d’emballage) = ce que tu vas photographier ; **HEAD** = la **dernière photo**.
- Usage : `git status` = où en est l’atelier ; `git add` = je pose sur la table ; `git commit` = je prends la photo.

### HEAD = “marque-page”
- Image : **HEAD** = **le marque-page** sur **le dernier commit** où tu te trouves.
- Usage : `git log --oneline --decorate --graph --all` pour voir où est le marque-page.

### `.gitignore` = “liste noire du bagage”
- Image : des objets que le **bagagiste** n’enregistre jamais (ex. `node_modules/`, `.env`).
- Usage : les ajouter **avant** de commit pour éviter le bruit.

### HTTPS vs SSH = “badge invité vs badge permanent”
- Image : **HTTPS** = badge invité (souvent demande un token) ; **SSH** = badge permanent (clé ajoutée une fois).
- Usage : préfère **SSH** pour cloner/pousser sans friction.

### ZIP ≠ Git = “copie à la sauvage”
- Image : copier les vêtements **sans** le carnet. Tu vois les fichiers mais **perds l’historique** et l’adresse.
- Règle : **reclone** toujours, puis copie tes modifs **dans** le repo cloné si besoin.

### Divergence = “deux lignes parallèles”
- Image : toi et le serveur avez avancé **chacun** → deux lignes.
- Symptomatique : `pull --ff-only` **refuse**.  
- Remède : `git pull --rebase origin main` (rejoue tes commits à la fin), puis `git push`.

### “Propre avant de partir” = “quatre temps”
- Image : **Pull → Code → Commit → Push**.
- Ancrage : P-C-C-P (Pull, Code, Commit, Push). Sur l’autre machine : **Pull** et tu repars.
```
