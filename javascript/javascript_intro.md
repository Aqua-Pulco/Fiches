# Qu’est-ce que JavaScript (fiche d’intro claire)

## 1) JavaScript = le langage qui fait **vivre le web**

HTML = le **squelette** (structure),  
CSS = les **vêtements** (style),  
JavaScript = les **muscles + nerfs** (interaction, mouvement).

Exemple simple :
```html
<p id="txt">Bonjour</p>
<button id="btn">Changer</button>
<script>
  const txt = document.querySelector("#txt");
  document.querySelector("#btn").addEventListener("click", () => {
    txt.textContent = "Clic reçu !";
  });
</script>

→ Ici JS écoute le clic, puis change le texte dans la page.
2) DOM — Document Object Model

Le DOM = une représentation en arbre de ta page HTML.
Chaque balise est un nœud que JS peut manipuler : lire, modifier, ajouter, supprimer.

Image : imagine une maquette en Lego → JS peut ajouter des briques, enlever des briques, changer la couleur d’une brique en direct.
3) Multi-paradigme — plusieurs façons de penser le code

Un paradigme = une façon d’écrire et d’organiser son code.
JS est rare car il accepte plusieurs styles :

    Impératif → tu donnes les étapes comme une recette.

let total = 0;
for (let i = 0; i < 3; i++) {
  total = total + i;
}
console.log(total); // 3 (0+1+2)

Fonctionnel → tu transformes les données avec des fonctions.

const total = [0,1,2].reduce((a,b) => a+b, 0);
console.log(total); // 3

Objet → tu regroupes données + comportements.

    const panier = {
      items: [],
      add(item){ this.items.push(item); },
      total(){ return this.items.reduce((a,i) => a+i, 0); }
    };
    panier.add(10);
    panier.add(5);
    console.log(panier.total()); // 15

👉 Dans la vraie vie, tu mélanges : impératif pour orchestrer, fonctionnel pour transformer des listes, objet pour structurer.
4) Dynamique — variables souples

JS est dynamique : une variable peut changer de type en cours de route.

let x = 42;     // number
x = "texte";    // string (autorisé)

⚠ Ça peut surprendre et causer des bugs.
Bonne pratique moderne :

    Toujours const par défaut (évite les réassignations inutiles).

    Utiliser let seulement si la valeur doit changer.

Image : une boîte sans étiquette → tu peux mettre un jouet ou un fruit, mais ça peut troubler si tu attends un jouet et que tu trouves une pomme.
5) Mono-thread + Async

JS tourne dans un seul fil (mono-thread) → il ne fait qu’une chose à la fois.
Mais… il a un superpouvoir : l’asynchrone.
→ Quand une tâche est longue (réseau, timer), JS ne bloque pas. Il continue, et reviendra plus tard avec la réponse.

Exemple :

console.log("Avant");
setTimeout(() => console.log("Après 2s"), 2000);
console.log("Après");

Résultat :

Avant
Après
...2s...
Après 2s

→ Pendant qu’on attend les 2s, JS peut faire autre chose (afficher “Après”).

Usages dev :

    Requêtes API (fetch des données),

    Attendre un temps (timers),

    Réagir aux événements (clic, clavier).

Image : un serveur de resto qui prend une commande, va en cuisine, mais sert d’autres tables en attendant. Quand le plat est prêt, il revient.
6) Pourquoi tout ça est puissant ensemble ?

    DOM → permet de manipuler la page comme une maquette vivante.

    Multi-paradigme → liberté d’écrire ton code selon le besoin.

    Dynamique → rapide pour expérimenter (prototyper).

    Mono-thread + async → simplicité mentale + fluidité pour l’utilisateur.

→ C’est pour ça que JS est devenu le langage universel du front-end, et qu’on le retrouve aussi côté serveur (Node.js).