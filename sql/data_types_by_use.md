PostgreSQL

SMALLINT, INTEGER (INT), BIGINT, NUMERIC(p,s) (DECIMAL), REAL, DOUBLE PRECISION, BOOLEAN, CHAR(n), VARCHAR(n), TEXT, CITEXT, UUID, DATE, TIME, TIMESTAMP WITHOUT TIME ZONE, TIMESTAMP WITH TIME ZONE (TIMESTAMPTZ), INTERVAL, JSON, JSONB, XML, ARRAY, ENUM (type défini), BYTEA, MONEY, GENERATED … AS IDENTITY, SERIAL/BIGSERIAL, INET, CIDR, MACADDR

SQLite

INTEGER, REAL, TEXT, BLOB, NUMERIC
(+ conventions : DATE/TIME/DATETIME stockés en TEXT ISO ou en epoch INTEGER)

Neon

= PostgreSQL (Neon est un Postgres managé, donc mêmes types que ci-dessus)

DrawSQL (dialecte “PostgreSQL” recommandé)

bigint, integer (int), smallint, numeric(decimal), real, double, boolean, char, varchar, text, uuid, date, time, timestamp (with/without time zone), interval, json, jsonb, enum, bytea
(+ génériques éventuels : xml, array selon le dialecte sélectionné)