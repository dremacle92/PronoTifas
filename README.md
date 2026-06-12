# Prono Les Tifas — Déploiement

## Étape 1 — Créer la base Supabase (5 min)

1. Va sur https://supabase.com → "Start for free" → crée un compte
2. "New project" → donne-lui un nom (ex: tifas), choisis une région proche (West EU), un mot de passe DB (à noter)
3. Attends ~2 min que le projet se crée
4. Dans l'éditeur SQL (menu gauche → SQL Editor → New query), colle et exécute :

```sql
create table players (
  id      bigint generated always as identity primary key,
  name    text    not null,
  points  integer not null default 0
);

-- Permet la lecture publique (classement visible par tous)
alter table players enable row level security;

create policy "lecture publique" on players for select using (true);

-- Permet l'écriture via la clé anon (notre "auth" = mot de passe côté client)
create policy "écriture anon" on players for all using (true) with check (true);
```

5. Va dans Settings → API → copie :
   - **Project URL** (ex: https://abcdefgh.supabase.co)
   - **anon public key** (longue chaîne JWT)

---

## Étape 2 — Configurer le site

Ouvre `config.js` et remplace les valeurs :

```js
const SUPABASE_URL  = 'https://TON_ID.supabase.co';  // colle ici
const SUPABASE_ANON_KEY = 'eyJ...';                   // colle ici
const ADMIN_PASSWORD = 'ton_mot_de_passe_admin';       // choisis
```

---

## Étape 3 — Déployer sur Vercel (2 min)

### Option A — Sans GitHub (le plus rapide)

1. Va sur https://vercel.com → crée un compte (gratuit)
2. Sur le dashboard → "Add New → Project"
3. Choisis **"Deploy without a Git repository"** (ou glisse le dossier)
4. Glisse-dépose le dossier `tifas` entier
5. Clique Deploy → ton site est en ligne en 30 secondes

### Option B — Via GitHub (recommandé pour les mises à jour)

1. Pousse le dossier sur un repo GitHub (public ou privé)
2. Sur Vercel → "Add New → Project" → importe le repo
3. Aucune config build nécessaire (site statique)
4. Deploy → c'est en ligne

---

## Utilisation

- **Classement public** : `https://ton-site.vercel.app`
- **Admin** : `https://ton-site.vercel.app/admin.html`
  → entre ton mot de passe → ajoute/modifie les joueurs et leurs points
  → le classement se met à jour en temps réel

---

## Ajouter les joueurs initiaux

Une fois déployé, va dans l'admin et ajoute les 10 joueurs un par un,
ou exécute ce SQL dans Supabase (adapte les noms) :

```sql
insert into players (name, points) values
  ('David', 0), ('Thomas', 0), ('Lucas', 0), ('Marie', 0),
  ('Pierre', 0), ('Julie', 0), ('Antoine', 0), ('Sophie', 0),
  ('Romain', 0), ('Camille', 0);
```
