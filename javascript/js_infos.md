# JavaScript — Fiche mémo expliquée (navigateur + ESM)

## Sommaire

2. Variables & types (`let`, `const`, `string/number/boolean/null/undefined`)
3. Objets & tableaux (obj literals, arrays, méthodes usuelles)
4. Fonctions (déclaration, expression, fléchée)
5. Contrôle de flux (`if`, `switch`, boucles)
6. DOM de base (`querySelector`, `textContent`, `classList`)
6 bis. DOM **dynamique** (créer/insérer/modifier à la volée)
7. Événements (`addEventListener`)
8. Réseau & données (`fetch`, JSON)
9. Async/await & erreurs (`try/catch`, `await`)
10. Modules ESM (`import/export`)
11. Recettes combinées récurrentes
12. Glossaire essentiel
13. Mnémo-techniques

---

## 2) Variables & types

```js
let age = 20;           // réassignable
const PI = 3.14159;     // constante (préférer const)
let name = "Iris";      // string
let ok = true;          // boolean
let nothing = null;     // valeur vide intentionnelle
let notDefined;         // undefined = pas encore défini

Pourquoi préférer const

    Intention claire, anti-bug (pas de réassignation accidentelle).

    let si, et seulement si, tu sais que la variable va changer (compteur, état).

    Objets/arrays en const → référence verrouillée, contenu modifiable :

    const user = { name: "Iris" };
    user.name = "Ada";   // OK (mutation)
    // user = {}         // ❌ réassignation interdite

3) Objets & tableaux

const user = { name: "Iris", city: "Paris" };
user.city = "Lyon";

const nums = [1, 2, 3];
nums.push(4);                                  // [1,2,3,4]
const doubled = nums.map(n => n * 2);          // [2,4,6,8]
const evens = nums.filter(n => n % 2 === 0);   // [2,4]
const sum = nums.reduce((acc, n) => acc + n, 0); // 10

4) Fonctions

function add(a, b) { return a + b; }            // déclaration
const sub = function(a, b){ return a - b; };     // expression
const mul = (a, b) => a * b;                    // fléchée

    Déclaration = hoistée (utilisable avant).

    Fléchée = syntaxe compacte, this prévisible.

5) Contrôle de flux

if (age > 18) { /* ... */ } else { /* ... */ }

switch (name) {
  case "Iris": /* ... */ break;
  default:     /* ... */
}

for (let i = 0; i < 3; i++) { /* ... */ }
for (const n of nums) { /* ... */ } // valeurs d’un array

6) DOM de base

<h1 id="title">Hello</h1>
<p class="msg"></p>
<script type="module" src="./main.js"></script>

const title = document.querySelector("#title");
const msg = document.querySelector(".msg");

title.textContent = "Bonjour Iris";  // texte sûr
msg.classList.add("highlight");      // ajouter une classe CSS

6 bis) DOM dynamique (créer, insérer, modifier du HTML à la volée)
Sélectionner et stocker

const app = document.querySelector("#app");          // #id
const items = document.querySelectorAll(".item");    // .classe (NodeList)

Créer → remplir → insérer

const p = document.createElement("p");  // créer <p>
p.textContent = "Bonjour Iris";         // texte brut (sécurisé)
app.appendChild(p);                     // insérer à la fin de #app

textContent vs innerText vs innerHTML

el.textContent = "<b>Texte</b>"; // littéral → affiche les chevrons
el.innerText = "Texte visible";  // tient compte du rendu (plus lent)
el.innerHTML = "<b>Texte</b>";   // interprète HTML (⚠ source FIABLE seulement)

Insérer à différents endroits

app.prepend(p);                                // au début
app.append(" — fin");                          // à la fin (accepte chaîne)
app.before(document.createElement("hr"));      // avant #app
app.after(document.createElement("hr"));       // après #app
app.insertAdjacentHTML("afterbegin", "<h2>Titre</h2>");

Classes, attributs, dataset

p.classList.add("card", "highlight");
p.classList.toggle("hidden");
p.id = "intro";
p.setAttribute("role", "note");
p.dataset.userId = "42";   // data-user-id="42"

Supprimer / remplacer

p.remove();
app.replaceChildren();      // vide
app.replaceChildren(p);     // remplace par p

Construire une liste depuis des données

const data = ["Ada", "Katas", "SQL"];
const ul = document.createElement("ul");
for (const label of data) {
  const li = document.createElement("li");
  li.textContent = label;
  ul.appendChild(li);
}
app.replaceChildren(ul);

(Variante rapide si les chaînes sont FIABLES)

app.innerHTML = `<ul>${data.map(x => `<li>${x}</li>`).join("")}</ul>`;

Délégation d’événements (liste dynamique)

ul.addEventListener("click", (e) => {
  const li = e.target.closest("li");
  if (!li) return;
  li.classList.toggle("selected");
});

Performance douce : DocumentFragment

const frag = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  const li = document.createElement("li");
  li.textContent = "Item " + i;
  frag.appendChild(li);
}
ul.appendChild(frag); // une seule insertion → moins de reflows

7) Événements

const btn = document.querySelector("#btn");
btn.addEventListener("click", () => {
  console.log("clic !");
});

8) Réseau & données (fetch, JSON)

fetch("https://api.example.com/data.json")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error("Erreur:", err));

9) Async/await & erreurs

async function loadData() {
  try {
    const res = await fetch("https://api.example.com/data.json");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    const data = await res.json();
    return data;
  } catch (e) {
    console.error("Erreur:", e.message);
    return null;
  }
}

10) Modules ESM (import/export)

// utils.js
export function sum(a, b) { return a + b; }
export const PI = 3.14;
export default function square(n){ return n*n; }

// main.js
import square, { sum, PI } from "./utils.js";

11) Recettes combinées récurrentes
A) Lire/écrire du texte dans la page

<h2 id="info"></h2>
<script type="module">
  const info = document.querySelector("#info");
  info.textContent = "Prête à coder 👍";
</script>

B) Réagir à un clic

<button id="go">Go</button>
<p class="msg"></p>
<script type="module">
  const btn = document.querySelector("#go");
  const msg = document.querySelector(".msg");
  btn.addEventListener("click", () => {
    msg.textContent = "Clic reçu !";
  });
</script>

C) Charger des données JSON et les afficher

<ul id="list"></ul>
<script type="module">
  const list = document.querySelector("#list");

  async function render() {
    try {
      const res = await fetch("./data.json");
      if (!res.ok) throw new Error("HTTP " + res.status);
      const items = await res.json(); // ex: ["Ada", "Katas"]
      list.innerHTML = items.map(x => `<li>${x}</li>`).join("");
    } catch {
      list.textContent = "Impossible de charger les données.";
    }
  }
  render();
</script>

12) Glossaire essentiel

    DOM : représentation de la page (nœuds).
    Objet : paquet {clé: valeur} avec méthodes éventuelles.
    Paradigme : style d’organisation du code (impératif/fonctionnel/objet).
    Event : signal utilisateur (clic, input…).
    Promise : résultat futur (ex. fetch).
    JSON : format texte d’échange de données.
    ESM : modules modernes (import/export).
    Hoisting : élévation des déclarations (fonctions déclarées dispo avant).

13) Mnémo-techniques

    const par défaut : cadenas mental ; let si ça doit changer.
    Objet = sac à poches ; Array = file indienne.
    map = transforme, filter = garde, reduce = agrège.
    DOM : « trouve → crée/configure → insère → écoute ».
    textContent par défaut (sécurité), innerHTML seulement si source fiable.
    fetch → .json() : “ramène + dépaquette”.
    async/await : “attends le facteur” ; try/catch = “filet de sécurité”.
    ESM : “boîte à outils” → export ce que tu offres, import ce que tu utilises.

