# Modéliser les tables SQL— fiche 

## 0) Vocabulaire

* **Table** : (penser tableau excel) un “type d’objet” (Clients, Commandes, Lignes de commande, Plats).
* **Colonne** : une propriété d’un objet (client\_name, price, quantity…).
* **Type de colonne (datatype)** : la “forme” de la donnée (TEXT, INT, DECIMAL, DATE/TIMESTAMP…).
* **Clé primaire (PK)** : identifiant unique d’une ligne dans **sa propre** table (ex. `orders.id`).
* **Clé étrangère (FK)** : colonne qui **pointe** la PK d’une **autre** table (ex. `order_lines.order_id` pointe `orders.id`).
* **Parent / Enfant** : dans une relation, le **parent** est le côté **1**, l’**enfant** est le côté **N**.
  Règle de base : **qui porte la FK est l’enfant** ; l’autre est le parent.

---

## 1) 1\:N — qui porte quoi, et comment “voir” les enfants ?

### L’idée clé

**Idée clé : dans un 1\:N, c’est toujours le côté N qui porte la clé étrangère. Le côté 1 ne liste rien. Il n’y a pas de “colonne tableau” dans `order` qui contiendrait toutes les lignes. L’accumulation est implicite : plusieurs lignes dans `order_line` portent la même valeur `order_id`. C’est ce regroupement qui matérialise “toutes les lignes de cette commande”.**


**Comment, alors, “voir” tous les enfants d’un parent ?**
On **regarde** dans la **table enfant** **toutes les lignes** qui ont la **même FK** → on **filtre par FK**.

### Image Excel (concrète et visuelle)

* Onglet **Orders** : 1 ligne = 1 commande (101, 102, 103…).
* Onglet **Order\_lines** : plein de lignes, avec une colonne **order\_id**.

> “Quelles sont *les lignes* de la commande 101 ?”
> Va dans **Order\_lines** et **filtre** `order_id = 101`.
> Tu obtiens toutes les lignes.
> **Pas besoin** d’une colonne `order.order_line_ids`. L’**accumulation** se fait **dans `order_lines`** : plusieurs lignes **partagent** la même FK.

**Pourquoi c’est bien ?**
Parce que l’**enfant “dit” à quel parent il appartient** avec sa FK. Le parent reste simple (1 parent = 1 ligne). Et toi, tu recomposes “la famille” par **filtre**, pas par une liste encodée dans le parent.

**Anti-modèle** : mettre une liste d’IDs d’enfants dans le parent (`orders.order_line_ids = [7,8,9]`). Ça casse l’intégrité, complique les requêtes et finit mal. Garde la **FK chez l’enfant**.

---

## 2) N\:N — pourquoi 3 tables, et que met-on dans la jonction ?

### Reconnaître un N\:N

Pose la question **dans les deux sens** :
– “Pour **un A**, combien de **B** ?” → plusieurs.
– “Pour **un B**, combien de **A** ?” → plusieurs.
Si **les deux** réponses sont “plusieurs”, c’est un **N\:N**.

### Comment on le modélise proprement

Un SGBD relationnel ne relie pas “directement” N\:N. On **décompose** en **deux relations 1\:N** via une **table de jonction** (la “troisième table”) :

```
A (parent) 1 — N  A_B (enfant des deux)  N — 1  B (parent)
```

* La table de jonction **porte deux FKs** : une vers **A**, une vers **B**.
* C’est **elle** qui “fait le lien”.
* Elle peut porter la **payload du lien** (ex. quantité, prix figé à l’instant T, options…).

### Exemple dans ton projet resto

* `orders` ↔ `plates` est **N\:N** (une commande a plusieurs plats, un plat est dans plusieurs commandes).
* La jonction s’appelle **`order_lines`** :
  – `order_lines.order_id` → FK vers `orders.id` (dit “j’appartiens à la commande 101”),
  – `order_lines.plate_id` → FK vers `plates.id` (dit “je parle du plat 3”),
  – + **payload** : `quantity`, le **prix figé** de la ligne, éventuellement **statut cuisine** au niveau ligne.

**Clé de la jonction (anti-doublon)**

* **Clé composite** `(order_id, plate_id)` si tu **fusionnes** les doublons (même plat → tu augmentes `quantity`).
* Ou un **id technique** + contrainte `UNIQUE(order_id, plate_id)` si tu veux garder la possibilité de doublonner (rare dans ton cas).

---

## 3) 1:1 — rare et simple

Si pour 1 X il n’y a **qu’un** Y et réciproquement, c’est **1:1**.
On met une FK **unique** d’un côté (ou on **fusionne** les tables si c’est vraiment la même entité logique).

---

(Parent = côté 1, Enfant = côté N ; je précise où vit la FK)*

## 4) La checklist qui évite 90 % des erreurs

* Trouve la **FK** → tu sais qui est **l’enfant (N)** et qui est **le parent (1)**.
* **Le parent ne liste jamais ses enfants** : on **filtre** la table enfant sur la **FK**.
* Si c’est **N\:N**, crée la **troisième table** (la **jonction**) avec **deux FKs** et mets-y la **payload** du lien.
* **FK/PK mêmes types**.
* Évite les **listes d’IDs** dans une colonne (anti-modèle).
* Fige les **prix** au moment de l’achat (dans `order_lines`) pour que l’historique ne bouge jamais quand le catalogue change.

---

## 5) Lecture “pas à pas” d’un lien (pour s’auto-vérifier)

1. Dis la phrase : “Pour **un** X, **combien** de Y ?”
2. Si “plusieurs” → **1\:N**, la **FK** est chez **Y** (l’enfant).
3. Si “plusieurs et plusieurs” → **N\:N** → **table de jonction** avec **deux FKs**.
4. Vérifie que les **types** FK = PK.

---

## 6) Ton schéma, vérifié avec la fiche pour traduction (cf adalicious ds repo : exercices_branche)

* `clients 1 — N orders` ✅ FK `orders.client_id`.
* `orders 1 — N order_lines` ✅ FK `order_lines.order_id`.
* `plates 1 — N order_lines` ✅ FK `order_lines.plate_id`.
* `orders 1 — N order_history` ✅ FK `order_history.order_id`.
* `orders ↔ plates` = **N\:N** via **`order_lines`** ✅ (payload : `quantity`, `line_price` figé).
* `kitchen` : choisis **commande** (*FK `order_id`*) **ou** **ligne** (*FK `order_line_id`*), pas les deux.

---

