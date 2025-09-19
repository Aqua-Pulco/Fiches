# Bash / Zsh — Fiche mémo expliquée (WSL/Ubuntu + Windows)

## Sommaire

1. Qu’est-ce qu’un terminal Bash (et contexte WSL/Windows)
2. Commandes de tous les jours (navigation, fichiers, VS Code)
3. Focus sur `cd` (raccourcis, tab, chemins)
4. Commandes de “liste” (`ls`, variantes, options)
5. Filtres et pipes (cat, grep, head, …)
6. Exécutables Windows dans WSL (`.exe`)
7. Gestion des paquets (sudo, apt)
8. Recettes combinées récurrentes
   - Voir tout de suite si on est dans un dépôt Git (prompt)
   - Trouver ma clé SSH rapidement
   - Ouvrir un projet WSL côté Windows
9. Personnaliser son invite (prompt) — afficher la branche Git
10. Glossaire essentiel
11. Mnémo-techniques

---

## 1) Qu’est-ce qu’un terminal Bash (et contexte WSL/Windows)

- **Bash** = interpréteur de commandes historique (très répandu sur Linux).  
- **Zsh** = shell “confort” (complétions avancées, prompt personnalisable).  
- **WSL (Windows Subsystem for Linux)** = Ubuntu qui tourne **dans** Windows.  
- Tu peux utiliser PowerShell/cmd (Windows), Git Bash (mini-Bash livré avec Git), ou **WSL Ubuntu** (ton cas principal).  

---

## 2) Commandes de tous les jours (navigation, fichiers, VS Code)

```bash
pwd                     # Où je suis (chemin courant)
cd ~/ada                # Entrer dans le dossier "ada" (depuis le HOME ~)
ls -la                  # Lister tout (y compris fichiers cachés) avec détails

mkdir projet            # Créer un dossier simple
mkdir -p src/utils      # Créer un dossier (et ses parents si manquants)

→ -p évite les erreurs si le parent n’existe pas, crée toute l’arbo en une fois.

touch README.md
touch index.html style.css script.js

→ Créer un fichier vide (ou mettre à jour sa date).
Utile pour préparer vite les fichiers de départ.

cat fichier.txt                   # Afficher le contenu d’un fichier
cat fichier1.txt fichier2.txt     # Afficher plusieurs fichiers à la suite
cat > nouveau.txt                 # Créer un fichier et taper du texte jusqu’à Ctrl+D

→ cat = concaténer/afficher. Avec cat >, tu écris directement dans un nouveau fichier.

mv ancien.txt nouveau.txt         # Renommer un fichier
mv notes.txt ~/ada/               # Déplacer un fichier dans un dossier

rm fichier.txt                    # Supprimer un fichier
rm -r dossier                     # Supprimer un dossier récursivement
rm -rf dossier                    # Supprimer un dossier sans confirmation (⚠ dangereux)

code .                            # Ouvrir le dossier courant dans VS Code
code fichier.txt                   # Ouvrir un fichier précis dans VS Code
# (si 'code' ne répond pas, vérifier VS Code + extension WSL installés)

3) Focus sur cd (raccourcis, tab, chemins)

    cd .. → remonter d’un dossier

    cd - → revenir au précédent

    cd / → aller à la racine du système

    cd ~ → aller dans ton HOME

    Tab → complète automatiquement les noms

    Chemin absolu : /home/iris/ada ; relatif : ../autre_dossier

4) Commandes de “liste” (ls, variantes, options)

ls          # liste simple
ls -l       # détails (taille, droits, dates…)
ls -a       # inclut fichiers cachés
ls -la | head   # n’affiche que le début (utile pour voir .git)

5) Filtres et pipes (cat, grep, head, …)

cat fichier.txt             # afficher un contenu
grep "mot" fichier.txt      # filtrer sur "mot"
ls -l ~/.ssh | grep id      # combiner liste + filtre (ex: clé SSH)

    Pipe | = relier commandes (sortie → entrée).

    grep = filtrer, head = montrer seulement le début.

    cat > fichier.txt = créer un fichier et écrire directement dedans.

6) Exécutables Windows dans WSL (.exe)

explorer.exe .                       # ouvrir le dossier courant côté Windows
cat ~/.ssh/id_ed25519.pub | clip.exe # copier un contenu dans le presse-papier Windows >>> contenu | cmd.exe

7) Gestion des paquets (sudo, apt)

sudo apt update         # rafraîchir le catalogue des paquets
sudo apt upgrade        # mettre à jour ce qui est déjà installé
sudo apt install <nom>  # installer un paquet (ex. git, curl)

8) Recettes combinées récurrentes
A) Voir tout de suite si on est dans un dépôt Git
    → Configure le prompt pour afficher la branche Git (section 9).
    Résultat attendu :

        iris@DESKTOP:ada main$

B) Trouver ma clé SSH rapidement

ls -l ~/.ssh | grep id

    explication:

        Décomposition mot à mot :
        ls = list → lister le contenu d’un dossier.
        -l = long format → afficher avec détails (droits, propriétaire, taille, date).
        ~ = HOME → ton dossier personnel (/home/iris sous WSL).
        .ssh = le dossier caché .ssh dans ton HOME, qui contient les clés SSH.
        | = pipe → “tuyau” qui envoie la sortie de la première commande vers la suivante.
        grep = global regular expression print → filtre, garde seulement les lignes qui contiennent un mot donné.
        id = le mot-clé recherché : ici on cherche les fichiers dont le nom contient “id” (ex. id_ed25519, id_rsa).

        La phrase complète :
        “Liste en format détaillé le contenu du dossier ~/.ssh, puis filtre le résultat pour ne garder que les fichiers dont le nom contient 'id'.”

        Résultat attendu : 
        tu vois apparaître tes fichiers de clés SSH (id_ed25519, id_ed25519.pub, etc.) avec leurs permissions et leur date de création.

C) Ouvrir un projet WSL côté Windows :
    explorer.exe .

## 9) Personnaliser son invite (prompt) — afficher la branche Git

### 9.1 Qu’est-ce que le *prompt* ?
Le *prompt* est le texte **avant ta saisie** dans le terminal.

Exemple par défaut (Bash sous WSL) :

iris@DESKTOP-981QV5L:~/ada$

- `iris` = utilisateur
- `DESKTOP-981QV5L` = machine
- `~/ada` = dossier courant
- `$` = prêt à exécuter

**Objectif de la personnalisation :** voir **sans taper de commande** si tu es dans un dépôt Git, et **sur quelle branche** (ex. `main`).

---

### 9.2 Version Bash (classique)

#### Code à coller (dans `~/.bashrc`), puis activer
```bash
# --- Afficher la branche Git dans le prompt ---
parse_git_branch() {
  git branch 2>/dev/null | grep '^*' | colrm 1 2
}
PS1="\u@\h:\W \[\033[32m\]\$(parse_git_branch)\[\033[00m\]$ "
# --- fin ---

Active immédiatement sans fermer le terminal :

source ~/.bashrc

Explication (ce que ça fait + résultat attendu)

    Ajoute une fonction qui récupère le nom de la branche Git si tu es dans un dépôt.
    Modifie la variable PS1 pour afficher : utilisateur@machine:dossier branche$, avec la branche en vert.
    Dans un dépôt, tu verras par ex. :

iris@DESKTOP:exercices_branches main$

    Hors dépôt Git, la partie branche sera vide (rien ne s’affiche, normal).

Décryptage du code (mot à mot)

parse_git_branch :

    git branch → liste les branches du repo courant.
    2>/dev/null → cache l’erreur si tu n’es pas dans un repo.
    | grep '^*' → garde la branche active (la ligne qui commence par *).
    | colrm 1 2 → retire les 2 premiers caractères (* ) → ne laisse que main, dev, etc.

PS1 :

    \u → user (ex. iris)
    \h → host (ex. DESKTOP)
    \W → dossier courant (ex. ada)
    \$(parse_git_branch) → exécute la fonction, insère le nom de branche
    \[\033[32m\] ... \[\033[00m\] → couleur verte pour la branche
    $ → symbole final du prompt

9.3 Version Zsh (plus simple avec un thème)
Code à régler (dans ~/.zshrc), puis activer

ZSH_THEME="robbyrussell"   # ou "agnoster", etc.

Active immédiatement :

    source ~/.zshrc

Explication (ce que ça fait + résultat attendu):

    Zsh sait afficher la branche Git via le thème, sans écrire de fonction.
    Avec robbyrussell, tu verras un prompt du style :

        ➜  exercices_branches git:(main)



Décryptage (mot à mot / idées-clés)

    ZSH_THEME="robbyrussell" → choisit un thème qui inclut la branche Git.
    source ~/.zshrc → recharge la config sans redémarrer.
    Le morceau git:(main) est ajouté automatiquement quand tu es dans un repo.

9.4 Comparaison rapide & tips

    Bash : tu bricoles PS1 + petite fonction → ultra simple et lisible.
    Zsh : tu choisis un thème → clé en main (branche affichée d’office).
    Tip : si tu passes souvent Bash ↔ Zsh, garde la même logique visuelle (user@host:dir + branche) pour ne pas te perdre.

10) Glossaire essentiel

    Shell : programme qui interprète tes commandes (bash, zsh, gitbash).
    Terminal : la fenêtre où tourne le shell.
    Prompt : le texte avant ta saisie (iris@DESKTOP:~$).
    PS1 (Bash) / PROMPT (Zsh) : modèle de l’invite.
    Chemin absolu/relatif : depuis / vs depuis ta position.
    Pipe | : chaîne deux commandes.
    apt / sudo : installer/mettre à jour, avec droits admin.
    code . : ouvre le dossier courant dans VS Code (intégré WSL).

11) Mnémo-techniques

    pwd = “où sont mes chaussures ?” → où je suis.
    cd = “changer de pièce” → se déplacer.
    ls = “regarde autour” → voir ce qu’il y a.
    mkdir = “ajouter un tiroir” ; touch = “poser une feuille vierge”.
    cat = “faire miauler le fichier” ; cat > = “écrire une note à la volée”.
    mv = “déplacer/renommer un carton” ; rm -rf = “dynamite”.
    Tab = “GPS” → complète le chemin.
    code . = “ouvre l’atelier” (VS Code au bon endroit).
    explorer.exe = “porte vers Windows” ; clip.exe = “mettre dans la poche”.
    Prompt avec branche Git = “badge de salle” → tu vois dépôt + branche d’un coup d’œil.
