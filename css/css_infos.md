# CSS — Glossaire → Cheat sheet → Cours

## 0) Glossaire express (avant tout)

### Box model (modèle de boîte)
- **content box** : le contenu (texte, image…).
- **padding** : espace **à l’intérieur** de la boîte (entre content et border).
- **border** : la **limite** visible de la boîte.
- **margin** : espace **à l’extérieur** de la boîte (autour de la boîte).

> Règle utile : `box-sizing: border-box;` → la largeur/hauteur **incluent** padding+border.

### Display (comment la boîte se comporte)
- **block** : prend toute la largeur disponible, va à la ligne (ex. `div`, `section`).
- **inline** : dans le flux du texte, **ne** prend **pas** toute la ligne (ex. `span`, `a`).
- **inline-block** : inline mais dimensionnable (width/height).
- **flex** : le parent devient une **étagère** flexible pour ses **enfants directs**.
- **grid** : le parent devient une **grille** bidimensionnelle.

### Flux normal / empilement
- **flux normal** : l’ordre HTML place les boîtes de haut en bas.
- **chevauchement** : quand tu **déplaces** une boîte (marge négative, `position`, etc.) ou la **sors** du flux.

### Position (déplacements)
- `static` (défaut) : pas de décalage possible.
- `relative` : garde sa place, peut être **légèrement décalé** (`top/left…`).
- `absolute` : **hors flux**, positionné par rapport au **contenant positionné**.
- `sticky` : garde sa place puis **colle** au bord (`top`) en scroll.
- `fixed` : **hors flux**, collé à l’**écran**.

### Z-index / “couches” (stacking context)
- **nouvelle couche** quand : `transform`, `opacity<1`, `position` + `z-index` numérique, `fixed`, etc.
- Le `z-index` **ne gagne** que **dans sa propre couche**.

### Unités
- **px** : pixel CSS.
- **em** : relatif à la **font-size de l’élément**.
- **rem** : relatif à la **font-size racine** (`:root`) → **stable** globalement.
- (**fr** en grid) : **fraction de l’espace disponible** dans la grille.

### Flex (axes)
- **axe principal** : direction de la rangée (`row` g→d, `column` h→b).
- **axe secondaire** : perpendiculaire au principal.
- `justify-content` : aligne **la rangée** sur l’axe **principal**.
- `align-items` : aligne **les items** sur l’axe **secondaire**.
- `gap` : espace **entre** enfants.

### Grid (mots de base)
- **track** : une piste (colonne/ligne).
- **grid lines** : séparations entre tracks (nb = tracks + 1).
- **cell** : une case (intersection).
- **span** : occuper plusieurs tracks (`grid-row: span 2;`).

---

## 1) Cheat sheet (avant le cours)

- **margin** = espace **extérieur**.  
- **padding** = espace **intérieur**.  
- **border** = **limite** de la boîte.  
- **Centrer une boîte** : `max-width` + `margin: 0 auto;` (élément **block**, largeur **< 100%**).  
- **Centrer le contenu** : `display: flex; justify-content: center; align-items: center;`.  
- **Écarter des items** : `gap: 1rem;` (flex/grid).  
- **Petit décalage “safe”** : `position: relative; top: -Xpx;` (pas de nouvelle couche).  
- **Évite** `transform` juste pour décaler → ça crée une **couche** inutile.  
- **Grid basique** : `display:grid; grid-template-columns: repeat(4,1fr); gap:1rem;`.  
- **Hauteurs grid style “masonry”** : `grid-auto-rows: 12rem;` + `grid-row: span N;`.  
- **Images en case** : `object-fit: cover; width:100%; height:100%; display:block;`.

---

## 2) Cours

### 2.1 Margin — centrer une boîte

**Image mentale :** le **carton** sur la **table**. `margin` = l’**air autour**.

#### Shorthand

margin: A; /* haut, droite, bas, gauche = A /
margin: A B; / (haut+bas)=A ; (gauche+droite)=B /
margin: A B C; / haut=A ; droite+gauche=B ; bas=C /
margin: A B C D; / haut, droite, bas, gauche (sens aiguilles) */


#### `margin: 0 auto;` (centrage horizontal)
- `0` haut/bas ; `auto` gauche/droite.  
- Le navigateur calcule l’espace restant et le **répartit** → boîte **centrée**.

**Pré-requis**
- L’élément est **display: block** (ex. `section`, `div`).  
- `width` ou `max-width` **< 100%** du parent.  
- Exemple valide : `max-width: 40rem;` — invalide : `width: 100%;`.

> On reste en **flux normal** (pas de positionnement spécial qui casserait ce centrage).

---

### 2.2 Unités `em` / `rem`

- **`rem`** pour les **espacements globaux** (stable, facile à maintenir).  
- **`em`** pour des paddings **dépendants de la typo locale** du composant.

Exemple :
```css
:root { font-size: 16px; }
.card  { padding: 1.25em; }     /* lié à la police de .card */
.page  { padding: 2rem; }       /* stable sur toute la page */

2.3 Flex — b.a.-ba (vraiment)

Parent : display:flex; → agit sur ses enfants directs seulement.
Ne centre pas la boîte parente (ça, c’est margin: 0 auto;).

.container {
  display: flex;
  flex-direction: row;            /* défaut : rangée g→d (row) ; ou column */
  justify-content: center;        /* place la rangée sur l’axe principal */
  align-items: center;            /* aligne les items sur l’axe secondaire */
  gap: 1rem;                      /* espace entre items */
}
.item.push-right { margin-left: auto; } /* pousse cet item au bord droit (row) */
.item.one-off    { align-self: flex-start; } /* override local */

Mémos

    row = gauche/droite ; column = haut/bas.

    justify-content = je bouge la rangée.

    align-items = j’aligne les chaises.

    gap > marges bricolées (plus propre).

2.4 Z-index & Position — règles utiles
Ordre de peinture “simple”

Sans z-index ni “couche” spéciale → l’ordre du HTML décide (ce qui vient après passe au-dessus s’il chevauche).
Quand z-index fonctionne

    L’élément doit participer à l’empilement :
    position: relative/absolute/sticky/fixed ou transform/opacity<1/etc.

    z-index: auto → suit l’ordre normal.

    z-index: 0/1/… → prend un rang dans sa couche.

Position — pense en 4 questions

“Garde sa place ? / peut être décalé ? / sort du flux ? / crée une couche ?”

    relative : oui / oui / non / non → petit décalage safe.

    absolute : non / oui / oui / (couche si z-index) → pour bulles/badges, pas pour structurer.

    sticky : oui / oui (collage) / non / non → headers qui restent visibles.

    fixed : non / oui / oui / oui → barre collée à l’écran.

Conseil clé : pour “faire mordre” un bloc vers le haut →
évite transform ; préfère :

.searchbar { position: relative; top: -24px; }
/* ou */
.searchbar { margin-top: -24px; }

Si tu gardes transform et que ton header est sticky, donne-lui un rang clair :

.siteHeader { position: sticky; top: 0; z-index: 100; }

2.5 Grid — vocabulaire & paramètres
Activer la grille + colonnes

.grille {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 1rem; /* espace horizontal + vertical */
}

    repeat(5, 1fr) = 5 colonnes égales ; fr = parts de l’espace dispo.

Hauteur de base (effet “masonry simple”)

.grille { grid-auto-rows: 12rem; }  /* brique verticale */
.item.courte { grid-row: span 1; }
.item.haute  { grid-row: span 2; }  /* ≈ 2 × 12rem (moins les gaps) */

Boucher les trous

.grille { grid-auto-flow: dense; }

Images qui remplissent sans se déformer

.grille img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

2.6 Grid — auto-placement & “bug visuel” (explication)

Par défaut : grid-auto-flow: row → le navigateur remplit ligne par ligne.
Si un item span 2 colonnes/hauteurs, il occupe aussi la case sous lui.
Quand on arrive à la ligne suivante, cette case est prise → l’algorithme saute et place l’item à la colonne suivante.
→ L’ordre visuel peut différer de l’ordre DOM : c’est normal.
2.7 Grid — solution “Touché-Coulé” (coords ciblées)

Tu cibles les items “gros”, tu donnes des coordonnées, tu laisses l’auto-placement ranger le reste.

/* Item 1 : colonne 1, ligne 1 ; hauteur = 2 rangées */
.grid-item1 {
  grid-column: 1;
  grid-row: 1 / span 2;
}

/* Item 2 : colonne 2, ligne 1 ; hauteur par défaut */
.grid-item2 {
  grid-column: 2;
  grid-row: 1;
}

Option : grid-auto-flow: row dense; pour aider à combler les trous.
3) Récap & mnémo

    Boîte : margin dehors / border limite / padding dedans.

    Centrer une boîte : max-width + margin: 0 auto; (élément block, largeur < 100%).

    Centrer le contenu : Flex (justify-content + align-items) + gap.

    Déplacer sans drame : position: relative; top:-X; (pas de nouvelle couche).

    Évite transform pour un simple décalage → sinon pense z-index du header sticky.

    Grid = display:grid + grid-template-columns + gap + grid-auto-rows + grid-row: span N.

    Images en case : object-fit: cover; width/height:100%; display:block;.

    Unités : rem pour global, em pour local à la typo.