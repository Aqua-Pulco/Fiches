# Dictionnaire express — JavaScript

## Variable
**C’est quoi ?** Un nom qui référence une valeur (qui peut changer ou non).
**Syntaxe**
```js
let age = 20;      // réassignable
const PI = 3.14;   // non réassignable (préférer par défaut)
age = 21;          // OK
// PI = 3.15;      // ❌ TypeError

Tableau (Array)

C’est quoi ? Une liste ordonnée de valeurs (indexées à partir de 0).
Syntaxe

const nums = [10, 20, 30];   // littéral
nums[0];        // 10
nums.length;    // 3
nums.push(40);  // [10,20,30,40]
nums.map(n => n*2);          // transforme
nums.filter(n => n>10);      // garde
nums.reduce((a,b)=>a+b,0);   // agrège

Objet

C’est quoi ? Un paquet de paires clé → valeur + méthodes (fonctions attachées).
Syntaxe

const user = { name: "Iris", city: "Paris" };  // littéral
user.city = "Lyon";           // accès/écriture par point
user["name"];                 // accès par clé dynamique
user.rename = n => user.name = n; // méthode

Fonction

C’est quoi ? Un bloc réutilisable qui prend des entrées et retourne une sortie.
Syntaxe

function add(a,b){ return a+b; }   // déclaration (hoistée)
const sub = function(a,b){ return a-b; }; // expression
const mul = (a,b) => a*b;          // fléchée (courte)
add(2,3); // 5

Condition

C’est quoi ? Exécuter selon un test vrai/faux.
Syntaxe

if (age >= 18) { /* ... */ } else { /* ... */ }
const label = age >= 18 ? "majeur" : "mineur"; // ternaire

Boucle

C’est quoi ? Répéter une action sur une plage ou une collection.
Syntaxe

for (let i=0; i<3; i++) { /* ... */ } // index
for (const n of [1,2,3]) { /* ... */ } // valeurs d'un array

Chaîne (Template literal)

C’est quoi ? String avec interpolation et multilignes.
Syntaxe

const name = "Iris";
const msg = `Hello ${name} !
Ligne 2.`;

Module (ESM)

C’est quoi ? Fichiers séparés avec export/import.
Syntaxe

// utils.js
export function sum(a,b){ return a+b; }
export default function square(n){ return n*n; }

// main.js
import square, { sum } from "./utils.js";

DOM — Sélection & lecture/écriture

C’est quoi ? L’arbre de la page HTML que JS peut manipuler.
Syntaxe

const el = document.querySelector("#title"); // 1er qui matche
const all = document.querySelectorAll(".item"); // tous (NodeList)

el.textContent = "Texte sûr";   // texte brut (sécurisé)
el.innerHTML = "<b>HTML</b>";   // insère du HTML (⚠ source fiable)
el.classList.add("active");     // classes CSS

DOM — Création & insertion

C’est quoi ? Fabriquer des nœuds et les injecter dans la page.
Syntaxe

const p = document.createElement("p");
p.textContent = "Bonjour";
document.querySelector("#app").appendChild(p);   // à la fin
// variantes : parent.prepend(p), parent.before(p), insertAdjacentHTML(...)

Événement

C’est quoi ? Une action (clic, saisie) qui déclenche une fonction.
Syntaxe

const btn = document.querySelector("#go");
btn.addEventListener("click", (e) => { /* ... */ });

Fetch (réseau) & JSON

C’est quoi ? Récupérer des données (souvent JSON) depuis une URL.
Syntaxe

fetch("/data.json")
  .then(r => r.json())       // parse JSON → objet JS
  .then(data => console.log(data))
  .catch(err => console.error(err));

Promise

C’est quoi ? Un résultat futur (réussite/échec) d’une opération asynchrone.
Syntaxe

fetch("/data.json") // retourne une Promise
  .then(/* succès */)
  .catch(/* échec */);

Async/Await

C’est quoi ? Écrire l’asynchrone comme du code “linéaire”.
Syntaxe

async function load() {
  try {
    const res = await fetch("/data.json"); // attend la Promise
    const data = await res.json();
    return data;
  } catch (e) {
    console.error(e);
    return null;
  }
}

Opérateurs clés

C’est quoi ? Les comparaisons & outils usuels.
Syntaxe

1 === "1";   // false  (égalité stricte — À PRIVILÉGIER)
1 == "1";    // true   (égalité lâche — À ÉVITER)
const copy = [...nums];            // spread (copie superficielle)
const { name, city } = user;       // destructuring objet

Sélecteurs CSS (rappel)

C’est quoi ? La “grammaire” de querySelector.
Syntaxe

"#id"            // par id
".classe"        // par classe
"section p.btn"  // descendant + tag + classe
"ul > li.item"   // enfant direct