Parfait, voici ta **recette réutilisable** pour vérifier “c’est un nombre entier fini (≥ 0)” — en étapes simples que tu peux répéter sans réfléchir.

### La checklist “CFEB”

**C**onvertir → **F**ini → **E**ntier → **B**ornes.

1. **Convertir (C)**
   Prends l’entrée telle qu’elle vient (string, nombre, etc.) et fais une **conversion explicite** :
   « `n = Number(h)` ».
   Garde `h` pour les messages d’erreur, utilise `n` pour la logique.

2. **Fini (F)**
   Vérifie que `n` est un **nombre fini** (ni `NaN`, ni `Infinity`) :
   « `Number.isFinite(n)` doit être vrai ».
   Si c’est faux → **invalide**.

3. **Entier (E)**
   Si tu veux un **entier**, exige-le :
   « `Number.isInteger(n)` doit être vrai ».
   Si c’est faux → **invalide**.
   (Si tu acceptes les décimaux, **saute** cette étape.)

4. **Bornes (B)**
   Applique tes limites de domaine :

   * non négatif : `n >= 0`
   * strictement positif : `n > 0`
   * intervalle : `min ≤ n ≤ max`
     Si la borne échoue → **invalide**.

5. **Valide**
   Si C, F, (E), B sont **tous** vrais → tu peux utiliser `n` en confiance. Sinon, message d’erreur avec la valeur originale `h`.

---
Number(h) : c’est une fonction de conversion. Elle prend quelque chose (string, booléen, etc.) et renvoie une valeur numérique (par ex. 42, 0, ou NaN). Point final. Elle ne “valide” rien, elle transforme.

Number.isFinite(n) : c’est une fonction de test (un prédicat). Elle regarde la valeur numérique que tu as maintenant dans n et répond true/false selon qu’elle est finie (pas NaN, pas Infinity, pas -Infinity). Elle n’effectue aucune conversion supplémentaire.

---


### Versions compactes (à coller partout)

* **Entier ≥ 0 :**

```js
const n = Number(h);
if (Number.isFinite(n) && Number.isInteger(n) && n >= 0) {
  // OK → utiliser n
} else {
  // Erreur → utiliser h pour le message
}
```

* **Décimal ≥ 0 (pas besoin d’entier) :**

```js
const n = Number(h);
if (Number.isFinite(n) && n >= 0) {
  // OK
} else {
  // Erreur
}
```

* **Dans un intervalle (ex. 1 à 100, entier) :**

```js
const n = Number(h);
if (Number.isFinite(n) && Number.isInteger(n) && n >= 1 && n <= 100) {
  // OK
} else {
  // Erreur
}
```

---

### Mémo ultra-court à retenir

> **CFEB** : *Convertir* → *Fini* → *(Entier ?)* → *Bornes*.
> `Number(...)`, puis `Number.isFinite`, `Number.isInteger` (si besoin), et les comparaisons.

Garde ce cadre et tu seras carrée sur toutes tes validations numériques (hauteur, largeur, index, limites d’API, etc.).
