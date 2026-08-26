## Exercice 2.3 : Créer une Table Simple
Tâche : Créer une table 
tags
 pour BlogApp.
Exigences :
tag_id
 : Clé primaire auto-incrémentée
nom
 : Nom de tag unique (max 50 caractères)
slug
 : Version URL-friendly (max 50 caractères, unique)
cree_le
 : Horodatage avec fuseau, par défaut maintenant
``` sql
 CREATE TABLE auteurs (
    tag_id SERIAL PRIMARY KEY,
    nom VARCHAR(50)
    slug VARCHAR(50) UNIQUE
    cree_le  TIMESTAMPTZ DEFAULT NOW(),
 )
```

## Exercice 2.4 : Créer une Table avec Contraintes
Tâche : Créer une table 
commentaires
 avec tous les types de contraintes.
Exigences :
comment_id
 : Clé primaire auto-incrémentée
post_id
 : Clé étrangère vers posts (cascade en suppression)
user_id
 : Clé étrangère vers utilisateurs (restrict en suppression)
contenu
 : Text, requis, minimum 1 caractère
cree_le
, 
modifie_le
 : Horodatages avec fuseau
Contrainte check : longueur contenu >= 1

``` sql
 CREATE TABLE commentaires (
    comment_id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL,
    user_id INTEGER NOT NULL,
    contenu VARCHAR(200) NOT NULL,
    cree_le TIMESTAMPTZ DEFAULT NOW(),
    modifie_le TIMESTAMPTZ DEFAULT NOW(),


    CONSTRAINT fk_commentaires_posts 
        FOREIGN KEY (post_id)
        REFERENCES posts(post_id)
        ON DELETE CASCADE

    CONSTRAINT fk_commentaires_utilisateurs
        FOREIGN KEY (user_id)
        REFERENCES utilisateurs(user_id)
        ON DELETE RESTRICT

    CONSTRAINT chk_longueur_contenu
        CHECK (contenu >= 1);
 )
```

## Exercice 2.5 : Modifier une Table Existante
Tâche : Modifier la table 
tags
 que vous avez créée précédemment.
Exigences :
1. Ajouter une colonne 
description
 (TEXT, nullable)
2. Ajouter une colonne 
compteur_utilisation
 (INTEGER, défaut 0, pas négatif)
3. Ajouter une contrainte CHECK pour s'assurer que 
nom
 fait au moins 2 caractères
4. Renommer 
cree_le
 en 
date_creation

```sql
ALTER TABLE tags ADD COLUMN description TEXT;
ALTER TABLE tags ADD COLUMN compteur_utilisation INTEGER DEFAULT 0;
ALTER TABLE tags ADD CONSTRAINT chk_compteur_utilisation_positif CHECK (compteur_utilisation >= 0);
ALTER TABLE tags ADD CONSTRAINT chk_nom_plus_2_car CHECK (LENGTH(nom) >= 2);
ALTER TABLE tags RENAME COLUMN cree_le TO date_creation;
```

## Exercice 2.6 : Pratique des Opérations INSERT
Utilisation de la base de données Pagila :
Tâche 1 : Insérer un nouveau client
```sql
INSERT INTO customer (store_id, firstname, last_name, address_id)
VAlUES (1,'basile','COCOTE',1)
RETURNING customer_id;

```
Tâche 2 : Insérer plusieurs films
```sql
INSERT INTO film (title, language_id, fulltext)
VAlUES ('filmkifépeur', 1, 'filmkifétrépeur')
RETURNING *;

```

## Exercice 2.7 : Pratique des Opérations UPDATE
Utilisation de Pagila :
Tâche 1 : Mettre à jour l'email d'un client

```sql
UPDATE INTO customer (email)
SET email = 'nouveaumaiiiil@maiiiiiiiiiiiiiiiil.com'
WHERE customer_id = 1
RETURNING email;

```

Tâche 2 : Appliquer une réduction de 10% à tous les films pour les clients du magasin 1
```sql
UPDATE film f
SET rental_rate = rental_rate % 0.9
FROM inventory i, rental r, customer c
WHERE f.film_id = i.film_id
AND i.inventory_id = r.inventory_id
AND r.customer_id = c.customer_id
AND c.store_id = 1;

```

## Exercice 2.8 : Pratique des Opérations DELETE
Utilisation de Pagila :
Tâche 1 : Supprimer un client test

``` sql
DELETE * from costumer WHERE store_id = 1 AND address_id = 1;

```

Tâche 2 : Archiver les anciennes locations
```sql
CREATE TABLE rental_archive (LIKE rental INCLUDING ALL);

WITH rental_tooOld AS (
DELETE FROM rental
WHERE rental_date < CURRENT_DATE - INTERVAL '2 years'
RETURNING *
)

INSERT INTO rental_archive
SELECT * FROM rental_tooOld;
```