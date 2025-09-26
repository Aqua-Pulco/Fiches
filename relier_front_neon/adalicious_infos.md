## Sommaire (ordre + but, en 5 lignes)

1) Charger le menu (**GET /api/menu**) : afficher les plats depuis la base.  
2) Ajouter au panier (**localStorage**) : clic “Commander” → { dishId, qty }.  
3) Continuer d’ajouter : relire/mettre à jour le panier persistant.  
4) Garantir un client (**POST /api/clients**) : obtenir `clientId` depuis `name`.  
5) Valider la commande (**POST /api/orders**) : envoyer `{ clientId, items }`, vider le panier.

Chaîne d’exécution : **Front (navigateur)** → **Back (Express)** → **DB (Neon)** → retour JSON → **Front**.

---

## Plan validé (code minimal + explications brèves)

### Étape 1 — Afficher le menu (GET /api/menu)

**Qu’est-ce qu’on fait ?** Le front demande la liste des plats en JSON et les affiche.  
**Pourquoi ?** La base (Neon) est la source de vérité.  
**Où ça s’exécute ?** Front = navigateur. Back = Node/Express. DB = Neon.  
**Qui parle à qui ?** Front → Back (GET) → DB → Back → Front.

#### FRONT (navigateur) — `front/js/menu-page.js`
```js
const API = 'http://localhost:3000/api';

async function getMenu() {
  const res = await fetch(`${API}/menu`, { method: 'GET', headers: { 'Accept': 'application/json' } });
  if (!res.ok) { throw new Error(`HTTP ${res.status}`); }        // ! = "non", res.ok = succès 2xx
  return await res.json();                                       // JSON → objet JS
}

function renderMenu(dishes) {
  const list = document.querySelector('#menu-list');             // conteneur HTML
  list.innerHTML = '';
  for (const d of dishes) {
    const card = document.createElement('div');
    card.innerHTML = `
      <img src="${d.image_url || ''}" alt="${d.name}">
      <h3>${d.name}</h3>
      <p>${(d.price_cents/100).toFixed(2)} €</p>
      <button class="btn-order" data-dishid="${d.id}">Commander</button>
    `;
    list.appendChild(card);
  }
}

(async () => {                                                   // au chargement
  try { renderMenu(await getMenu()); }
  catch { alert('Impossible de charger le menu.'); }
})();

    Ligne par ligne (très court) : fetch envoie la requête ; res.ok vérifie 2xx ; res.json() lit le corps JSON ; on crée du HTML et on l’injecte.

BACK (serveur) — structure minimale en 3 fichiers

back/server.js

import express from 'express';
import cors from 'cors';
import menuRoutes from './routes/menu.routes.js';

const app = express();
app.use(cors({ origin: ['http://127.0.0.1:5500','http://localhost:5500'] })); // autorise le front local
app.use(express.json());
app.use('/api/menu', menuRoutes);
app.listen(3000);

back/routes/menu.routes.js

import { Router } from 'express';
import { getMenu } from '../controllers/menu.controller.js';
const r = Router();
r.get('/', getMenu);
export default r;

back/controllers/menu.controller.js

import { listDishes } from '../models/menu.model.js';
export async function getMenu(req, res) {
  try { res.status(200).json(await listDishes()); }
  catch { res.status(500).json({ message: 'Menu error' }); }
}

back/models/menu.model.js (accès DB via Neon)

import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL);
export async function listDishes() {
  return await sql`SELECT id, code, name, description, price_cents, image_url, category FROM dishes ORDER BY id ASC`;
}

Étape 2 — Ajouter au panier (localStorage)

Qu’est-ce qu’on fait ? Clic “Commander” → on stocke { dishId, qty } dans localStorage.cart.
Pourquoi ? Construire une commande persistante côté navigateur.
Où / Qui ? Front uniquement, aucune requête réseau.
FRONT — front/js/menu-page.js (complément)

function readCart() {
  const raw = localStorage.getItem('cart');            // lit la clé
  return raw ? JSON.parse(raw) : [];                   // JSON → objet
}
function writeCart(cart) {
  localStorage.setItem('cart', JSON.stringify(cart));  // objet → JSON
}
function addToCart(dishId, qty = 1) {
  const cart = readCart();
  const line = cart.find(l => l.dishId === dishId);
  if (line) line.qty += qty; else cart.push({ dishId, qty });
  writeCart(cart);
}

// Capte tous les clics sur un bouton "Commander"
document.addEventListener('click', (e) => {
  const btn = e.target.closest('.btn-order');
  if (!btn) return;
  addToCart(Number(btn.dataset.dishid), 1);
  btn.textContent = 'Ajouté ✓';
  setTimeout(() => (btn.textContent = 'Commander'), 800);
});

    Explications : localStorage stocke des chaînes. On convertit objet ↔ JSON avec JSON.stringify et JSON.parse.

Étape 3 — Garantir un client (POST /api/clients) puis valider la commande (POST /api/orders)

Qu’est-ce qu’on fait ?

    On obtient clientId à partir du name (stocké dans localStorage.clientName).

    On envoie { clientId, items } pour créer la commande.

Pourquoi ? Enregistrer la commande en base et récupérer orderId.
Où / Qui ? Front → Back (HTTP) → DB → retour JSON.
Quand ? Au clic “Valider la commande”.
FRONT — front/js/menu-page.js

const API = 'http://localhost:3000/api';

async function postJson(path, data) {
  const res = await fetch(`${API}${path}`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
    body: JSON.stringify(data)                               // payload = corps envoyé
  });
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return await res.json();
}

async function ensureClientId() {
  const name = localStorage.getItem('clientName');
  if (!name) throw new Error('Nom manquant');
  const client = await postJson('/clients', { name });       // → { id, name }
  return client.id;
}

function buildItemsFromCart() {
  return readCart().map(l => ({ dishId: l.dishId, qty: l.qty }));
}

async function submitOrder() {
  const clientId = await ensureClientId();
  const items = buildItemsFromCart();
  if (items.length === 0) { alert('Panier vide'); return; }

  const order = await postJson('/orders', { clientId, items });  // → { id }
  localStorage.removeItem('cart');
  alert(`Commande #${order.id} confirmée`);
}

// Bouton “Valider la commande”
document.querySelector('#btn-checkout')?.addEventListener('click', () => {
  submitOrder().catch(() => alert('Erreur commande'));
});

BACK — routes + contrôleurs (ultra-essentiel)

back/routes/clients.routes.js

import { Router } from 'express';
import { postClient } from '../controllers/clients.controller.js';
const r = Router();
r.post('/', postClient);      // POST /api/clients
export default r;

back/controllers/clients.controller.js

import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL);

export async function postClient(req, res) {
  try {
    const { name } = req.body || {};
    if (!name) return res.status(400).json({ message: 'name required' });
    const ins = await sql`INSERT INTO clients (name) VALUES (${name}) ON CONFLICT DO NOTHING RETURNING id, name`;
    if (ins.length) return res.status(201).json(ins[0]);
    const ex = await sql`SELECT id, name FROM clients WHERE name = ${name} LIMIT 1`;
    return res.status(201).json(ex[0] || { id: null, name });
  } catch { res.status(500).json({ message: 'client error' }); }
}

back/routes/orders.routes.js

import { Router } from 'express';
import { postOrder } from '../controllers/orders.controller.js';
const r = Router();
r.post('/', postOrder);       // POST /api/orders
export default r;

back/controllers/orders.controller.js

import { neon } from '@neondatabase/serverless';
const sql = neon(process.env.DATABASE_URL);

export async function postOrder(req, res) {
  try {
    const { clientId, items } = req.body || {};
    if (!clientId || !Array.isArray(items) || items.length === 0) {
      return res.status(400).json({ message: 'clientId and items required' });
    }
    await sql`BEGIN`;
    const created = await sql`INSERT INTO orders (client_id) VALUES (${clientId}) RETURNING id`;
    const orderId = created[0].id;
    for (const it of items) {
      await sql`INSERT INTO order_items (order_id, dish_id, qty) VALUES (${orderId}, ${it.dishId}, ${it.qty})`;
    }
    await sql`COMMIT`;
    res.status(201).json({ id: orderId });
  } catch {
    await sql`ROLLBACK`;
    res.status(500).json({ message: 'order error' });
  }
}

back/server.js (avec toutes les routes)

import express from 'express';
import cors from 'cors';
import menuRoutes from './routes/menu.routes.js';
import clientsRoutes from './routes/clients.routes.js';
import ordersRoutes from './routes/orders.routes.js';

const app = express();
app.use(cors({ origin: ['http://127.0.0.1:5500','http://localhost:5500'] }));
app.use(express.json());
app.use('/api/menu', menuRoutes);
app.use('/api/clients', clientsRoutes);
app.use('/api/orders', ordersRoutes);
app.listen(3000);

Dictionnaire (mini-glossaire)

    payload : corps de la requête HTTP (ex. { name: "Iris" }).

    endpoint : URL d’API (ex. /api/orders).

    fetch : fonction du navigateur pour envoyer des requêtes HTTP.

    response.ok : true si le code HTTP est 2xx (succès).

    ! : négation logique (inverse un booléen).

    response.status : code HTTP (200, 201, 400, 500…).

    response.json() : lire et décoder le JSON reçu.

    JSON.stringify(obj) : encoder un objet JS en texte JSON.

    localStorage : stockage persistant clé/valeur dans le navigateur.

    CORS : autorisations inter-origines (front ↔ back).

    controller : code back qui traite une requête.

    model / SQL : code back qui parle à la base.

Erreurs typiques & diagnostics (basique)

    Front : TypeError: Failed to fetch
    Problème réseau ou serveur éteint. Vérifie server.js et l’URL API.

    Front : HTTP 400 (Bad Request)
    Input manquant côté POST (ex. name ou items). Vérifie le payload.

    Front : HTTP 500 (Internal Server Error)
    Erreur serveur/SQL. Regarde la console du back (Node) pour le message.

    CORS : blocage
    Ajoute l’origine du front dans cors({ origin: [...] }) côté back.

    JSON vide / parsing
    Vérifie headers: { 'Content-Type': 'application/json' } et JSON.stringify(...).

    Panier qui “disparaît”
    Tu as peut-être fait localStorage.removeItem('cart') trop tôt. Garde l’ordre : build → POST → vider.