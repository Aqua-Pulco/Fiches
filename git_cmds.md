# Git — Fiche mémo expliquée (WSL/Ubuntu + GitHub/SSH)

## Sommaire
1. Préambule & idées-clés
2. Mise en place machine (1× par machine)
   - Identité
   - Clés SSH GitHub
   - Test de connexion `ssh -T git@github.com`
   - Réglages globaux : `pull.ff=only` et `push.autoSetupRemote=true`
3. Cloner un dépôt existant (propre)
4. Créer un nouveau projet local → premier push vers GitHub
5. Routine de travail au quotidien
6. Branches : créer, basculer, pousser
7. Sync & rattrapage (fast-forward vs merge, rebase simple)
8. Remotes & URLs (vérifier, ajouter, modifier)
9. Historique & “filet de sécurité”
10. Glossaire (mots qui reviennent)

---

## 1) Préambule & idées-clés

- **Toujours cloner plutôt que télécharger un ZIP.** Un ZIP n’embarque pas le dossier caché `.git` (donc pas d’historique ni de lien avec GitHub).
- **Pas de dépôts imbriqués.** Un dépôt Git (un dossier qui contient `.git`) ne doit pas vivre à l’intérieur d’un autre dépôt Git.
- **L’arbo locale peut être différente d’une machine à l’autre.** Git s’en fiche : tout se joue dans chaque **dossier de dépôt** (celui qui contient `.git`).
- **Rythme gagnant :** `pull` avant de coder → `add/commit` pendant → `push` après.

---

## 2) Mise en place machine (1× par machine)

### 2.1 Identité
```bash
git config --global user.name "Ton Nom"
git config --global user.email "ton.email@exemple.com"

Ce que ça fait. Dit à Git qui signe les commits sur cette machine.
Pourquoi. Les commits sont horodatés et signés (auteur·rice, email).
Quand. Une seule fois après l’installation de Git (ou si tu changes d’email).
2.2 Clés SSH GitHub

ssh-keygen -t ed25519 -C "ton.email@exemple.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub | clip.exe
# → GitHub > Settings > SSH and GPG keys > New SSH key > coller la clé

Ce que ça fait.

    ssh-keygen … ed25519 crée une paire de clés (privée + publique) moderne, courte et sûre.

    ssh-agent démarre le gestionnaire de clés en mémoire.

    ssh-add y charge ta clé privée (évite d’avoir à la retaper).

    clip.exe copie ta clé publique dans le presse-papier Windows pour la coller dans GitHub.

Pourquoi. L’authentification SSH évite les mots de passe/tokens à répétition, surtout pour push.
Quand. Une fois par machine (ou si tu régénères une clé).
2.3 Tester l’accès SSH à GitHub

ssh -T git@github.com

Ce que ça fait. Ouvre une session SSH “à blanc” vers GitHub pour vérifier que ta clé est reconnue.
Attendu. « Hi TON_PSEUDO! You’ve successfully authenticated, but GitHub does not provide shell access. »
Pourquoi. Tu sais avant de cloner/pusher que l’auth est OK.
Mnémo. -T = “no TTY” (test silencieux).
2.4 Réglages globaux utiles (posés une fois)

git config --global pull.ff only
git config --global push.autoSetupRemote true

pull.ff only — explication didactique

    Ce que ça fait. Oblige git pull à avancer la branche uniquement si l’opération peut se faire en fast-forward (FF) sans créer un commit de merge automatique.

    Pourquoi c’est utile.

        Évite les commits parasites du style “Merge branch 'origin/main'…” créés par surprise lors d’un pull.

        Si ta branche a divergé (toi + quelqu’un d’autre avez committé chacun de votre côté), Git refuse de fusionner tout seul et te le dit clairement.

    Ce que ça implique. En cas de divergence, tu choisis consciemment la stratégie (souvent git pull --rebase), au lieu de subir un merge automatique.

    Image mentale. Fast-forward = rembobiner la tête de lecture vers l’avant, sans brancher d’autre piste. Pas de “nœud” dans l’historique.

push.autoSetupRemote true — explication didactique

    Ce que ça fait. Au premier git push d’une nouvelle branche locale, Git configure automatiquement l’upstream (le lien “cette branche locale suit telle branche distante”).

    Pourquoi c’est utile. Tu évites le message « no upstream branch » et la corvée de faire git push -u origin ma-branche la première fois.

    Définition d’upstream. La branche distante de référence que suit ta branche locale (ex. main suit origin/main). Ce lien permet à git pull/git push sans arguments de savoir où tirer/pousser.

    Effet concret. Tu peux faire git push tout de suite après avoir créé une nouvelle branche locale, sans te poser de question.

Vérifier (optionnel).

git config --global -l | grep -E 'pull.ff|push.autoSetupRemote'
# attendu : pull.ff=only  et  push.autoSetupRemote=true

3) Cloner un dépôt existant (propre)

cd ~/ada
git clone git@github.com:Aqua-Pulco/NOM_DU_REPO.git
cd NOM_DU_REPO
git remote -v
git status
git branch -vv

Pourquoi. Le clone ramène le dossier .git (historique + lien GitHub). Un ZIP ne le fait pas.
À vérifier. remote -v (URL SSH), status (propre), branch -vv (suivi origin/... OK).
4) Créer un nouveau projet local → premier push vers GitHub

cd /chemin/vers/mon-projet
git init
git add -A
git commit -m "init"
git branch -M main
git remote add origin git@github.com:Aqua-Pulco/NOM_DU_REPO.git
git remote -v
git push -u origin main

Ce que fait chaque ligne.

    git init : crée le dépôt local (le dossier .git).

    git add/commit : enregistre un premier snapshot.

    git branch -M main : nomme la branche principale “main” (uniformise).

    git remote add origin … : relie au dépôt GitHub vide.

    git push -u origin main : envoie la branche et établit l’upstream (utile si push.autoSetupRemote n’était pas actif).

5) Routine de travail au quotidien

git pull --ff-only
# ... tu codes ...
git add -A
git commit -m "message clair"
git push

Pourquoi cet ordre.

    pull --ff-only avant de coder : tu repars d’une base à jour, sans merge auto.

    add/commit : tu crées un point propre dans l’historique.

    push : tu partages immédiatement (et l’autre machine n’aura plus qu’à pull).

6) Branches : créer, basculer, pousser

git switch -c feature/ma-fonction   # créer + basculer
git branch -vv                      # vérifier le suivi
git push                            # premier push : upstream auto si réglé globalement
# sinon explicitement :
# git push -u origin feature/ma-fonction
git switch main                     # revenir sur la branche principale

Pourquoi des branches. Travailler une idée isolément (sécurité mentale), puis ouvrir une PR/merge quand c’est prêt.
Astuce nommage. feature/…, fix/…, chore/… : indique l’intention.
7) Sync & rattrapage (fast-forward vs merge, rebase simple)

    Fast-forward : la branche locale peut “avancer en ligne droite” jusqu’au dernier commit distant → pull sans créer de commit de merge.

    Merge automatique parasite : si Git fusionne tout seul pendant un pull, tu obtiens un commit “Merge …” qui brouille l’historique. pull.ff=only l’empêche.

    Divergence : toi et quelqu’un d’autre avez committé chacun de votre côté → le fast-forward n’est pas possible.

Recette de rattrapage simple (rebase) :

git fetch --all --prune
git pull --ff-only                  # réessaie; s'il refuse :
git pull --rebase origin main       # rejoue tes commits au-dessus d'origin/main
# s'il y a des conflits :
#   - édite les fichiers
#   - git add <fichier>
git rebase --continue
git push

Pourquoi le rebase. Historique linéaire (facile à lire), commits groupés par logique.
Quand éviter. Sur des branches déjà partagées, préviens ton équipe (mais en solo / projet perso, c’est parfait).
8) Remotes & URLs (vérifier, ajouter, modifier)

git remote -v
git remote add origin git@github.com:Aqua-Pulco/NOM.git
git remote set-url origin git@github.com:Aqua-Pulco/NOM.git

Idée. origin = nom standard du remote vers GitHub.
Cas d’usage. Passer de HTTPS à SSH, ajouter un remote manquant, corriger une URL.
9) Historique & “filet de sécurité”

git log --oneline --decorate --graph --all  # lecture compacte + graphe
git reset                                   # enlève du "stage" sans perdre le travail
git restore --source=HEAD --worktree --staged .  # écrase le travail non committé (prudence)
echo "node_modules/" >> .gitignore          # ignorer un dossier bavard

Traductions rapides.

    log : visualiser l’histoire.

    reset (sans args) : “dé-stagé” (tu gardes tes modifs dans les fichiers).

    restore … : revenir tel que c’était au dernier commit (dangereux si tu n’as pas sauvegardé).

    .gitignore : liste ce que Git doit ignorer (ex. node_modules/, .env).

10) Glossaire

    Dépôt (repo). Dossier versionné par Git (contient .git).

    Remote. Alias d’URL du dépôt distant (ex. origin → GitHub).

    Branche. Ligne de développement (ex. main, feature/x).

    Commit. Photo instantanée des fichiers + message → crée un point dans l’historique.

    Fast-forward. Avancer la branche sans créer de commit de merge.

    Upstream. Branche distante de référence suivie par ta branche locale (ex. origin/main).

    Rebase. Rejouer tes commits par-dessus une base plus récente pour garder un fil droit.