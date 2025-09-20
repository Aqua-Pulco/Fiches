Types SQL — sommaire clair et numéroté
Sommaire

Numériques entiers

Numériques à virgule

Booléen

Texte & identifiants

Dates & temps

Données structurées (JSON, XML, ARRAY)

Énumérations & ensembles

Binaire & fichiers

Réseaux & géo (spécifiques)

Argent dédié (spécifique)

Identité / auto-incrément

Particularités SQLite (affinités)

Rappels de bonnes pratiques

Tableau “choisir le type selon l’usage”

1) Numériques entiers

1.1 SMALLINT
 • Petit entier (±32 767).
 • Compteurs limités, flags numériques.

1.2 INT / INTEGER
 • Entier standard (≈ ±2 milliards).
 • Id, quantités, centimes (si choix “centimes”).

1.3 BIGINT
 • Très grand entier (≈ ±9×10¹⁸).
 • Id longue durée, gros compteurs.

Note moteurs : MySQL a aussi TINYINT, MEDIUMINT. SQLite regroupe tout sous l’affinité INTEGER.

2) Numériques à virgule

2.1 DECIMAL(p,s) / NUMERIC(p,s) (décimal exact)
 • Exact en base 10 → idéal pour monnaie/TVA.
 • p = nb total de chiffres ; s = nb après la virgule.
 • Ex. DECIMAL(10,2) → 99 999 999,99 max.

2.2 FLOAT / REAL / DOUBLE (flottant binaire)
 • Rapide mais imprécis (0,1+0,2 ≠ 0,3 pile).
 • Mesures physiques, stats. Éviter pour l’argent.

Règle argent : centimes en INT ou DECIMAL(10,2). Les deux sont exacts, à l’inverse de FLOAT.

3) Booléen

3.1 BOOLEAN / BOOL
 • Vrai/faux.
 • MySQL : mappé en TINYINT(1).
 • SQLite : pas natif, on utilise 0/1.

4) Texte & identifiants

4.1 CHAR(n)
 • Longueur fixe. Rare en app (codes normalisés).

4.2 VARCHAR(n)
 • Longueur bornée. Contrainte métier (ex. code ≤ 20).

4.3 TEXT
 • Taille libre. Descriptions, URLs, emojis.

4.4 UUID
 • Identifiant universel (PostgreSQL natif ; MySQL via fonctions/extension).

4.5 CITEXT (PostgreSQL)
 • Texte insensible à la casse (recherches plus simples).

5) Dates & temps

5.1 DATE
 • Jour civil seulement (sans heure). Ex. “menu du jour”.

5.2 TIME / TIME WITH TIME ZONE
 • Heure seule (peu utilisé sans date).

5.3 TIMESTAMP WITHOUT TIME ZONE
 • Date+heure sans fuseau (étiquette locale).

5.4 TIMESTAMP WITH TIME ZONE (timestamptz, PostgreSQL)
 • Instant absolu (stocké/normalisé en UTC). Recommandé pour événements.

5.5 DATETIME (MySQL)
 • Date+heure sans fuseau (similaire à 5.3).

5.6 INTERVAL (PostgreSQL)
 • Durée (ex. “2 hours 30 minutes”).

SQLite : pas de types temps natifs. On stocke ISO TEXT (YYYY-MM-DD, YYYY-MM-DD HH:MM:SS) ou epoch INTEGER, et on manipule avec date()/datetime()/strftime().

6) Données structurées

6.1 JSON (MySQL/PostgreSQL/SQLite*)
 • Objets/tableaux JSON.
 • PostgreSQL JSONB : indexable, efficace pour requêtes.
 • *SQLite : via extension ; sinon TEXT + validations côté app.

6.2 XML (PostgreSQL)
 • Plus rare aujourd’hui.

6.3 ARRAY (PostgreSQL)
 • Tableau typé. Puissant, à manier avec parcimonie en relationnel pur.

7) Énumérations & ensembles

7.1 ENUM
 • Liste fermée de valeurs (ex. draft/published).
 • PostgreSQL : solide (type custom).
 • MySQL : pratique mais rigide à faire évoluer.
 • Alternative portable : table de référence + FK.

7.2 SET (MySQL)
 • Ensemble combinable de valeurs prédéfinies. Peu portable.

8) Binaire & fichiers

8.1 BYTEA (PostgreSQL), BLOB/LONGBLOB (MySQL), BLOB (SQLite)
 • Octets bruts (images/PDF). Gonfle la base.
 • Souvent mieux : stocker URL en TEXT + fichier hors base.

8.2 VARBINARY(n) (MySQL)
 • Binaire borné (petits tokens, empreintes).

9) Réseaux & géo (spécifiques)

9.1 INET / CIDR / MACADDR (PostgreSQL)
 • IP, réseaux, adresses MAC.

9.2 POINT / LINESTRING / GEOGRAPHY (PostGIS)
 • Géospatial (distances, cartes).

10) Argent dédié (spécifique)

10.1 MONEY (PostgreSQL)
 • Type monnaie formaté. Souvent délaissé au profit de DECIMAL(10,2) pour garder la main sur les formats/calculs.

11) Identité / auto-incrément

11.1 SERIAL / BIGSERIAL (PostgreSQL “legacy”)
 • Entier + séquence auto. Aujourd’hui, on préfère 11.2.

11.2 GENERATED … AS IDENTITY (PostgreSQL, standard SQL)
 • Auto-incrément moderne.

11.3 AUTO_INCREMENT (MySQL)
 • Auto-incrément natif.

11.4 SQLite
 • INTEGER PRIMARY KEY = rowid auto.
 • AUTOINCREMENT existe mais rarement nécessaire.

12) Particularités SQLite (affinités)

12.1 Affinités de type : INTEGER, REAL, TEXT, BLOB, NUMERIC.
 • SQLite accepte VARCHAR(255) ou DECIMAL(10,2), mais les range sous l’affinité correspondante.
 • Pour monnaie exacte : stocker centimes en INTEGER est très robuste.

13) Rappels de bonnes pratiques

13.1 FK ↔ PK : mêmes types (ex. client_id BIGINT ↔ clients.id BIGINT).
13.2 Argent : DECIMAL(10,2) ou centimes INT ; éviter FLOAT.
13.3 Timestamps : stocker en UTC ; convertir à l’affichage.
13.4 Images : TEXT (URL/emoji) ; BLOB uniquement si binaire en base.
13.5 ENUM : ok si peu de changements ; sinon table de ref + FK.
13.6 Nommage : snake_case, pas d’espaces ni tirets, cohérence sur tout le schéma.

14) Choisir le type selon l’usage (mémo rapide)

Identifiant technique → BIGINT (ou UUID si besoin global).

Quantité → INT.

Prix unitaire / total → DECIMAL(10,2) ou INT en centimes (choix unique).

Nom / description / URL / emoji → TEXT.

Date du jour → DATE.

Instant événement (créé, payé, livré) → TIMESTAMP WITH TIME ZONE (PostgreSQL) / DATETIME stocké en UTC (MySQL) / TEXT ISO (SQLite).

Fichier image stocké en base → BLOB/BYTEA (sinon URL TEXT).

Statut métier fermé → ENUM ou table status + FK.