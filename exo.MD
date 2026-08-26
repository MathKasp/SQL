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


## Exercice 3.1 : Pratique de SELECT de base
Dataset : Pagila
Tâche 1 : Sélectionner tous les films en affichant uniquement le titre, l'année de sortie et le tarif de
location

```sql
SELECT release_year, title, rental_rate FROM film;

```

Tâche 2 : Sélectionner les clients en affichant leur nom complet (prénom et nom combinés) et email en
majuscules

```sql
SELECT last_name, first_name, UPPER(email) FROM customer;

```

Tâche 3 : Obtenir toutes les classifications de films uniques dans la base de données
```sql
SELECT DISTINCT rating FROM film;

```

## Exercice 3.2 : Pratique de la clause WHERE
Dataset : Pagila
Tâche 1 : Trouver tous les films classés 'R' avec un tarif de location supérieur à 3.00
```sql
SELECT * from film WHERE rating = 'R' AND rental_rate > 3;

```

Tâche 2 : Trouver les clients dont le nom de famille commence par 'S' (insensible à la casse)

```sql
SELECT * FROM customer WHERE last_name LIKE 'S%' OR last_name LIKE 's%';

```
Tâche 3 : Trouver les films entre 100 et 120 minutes, classés 'PG-13' ou 'R'
```sql
SELECT * FROM film WHERE length_hours BETWEEN 1.4 AND 2 AND rating IN ('PG-13','R');

```

## Exercice 3.3 : Tri et pagination
Dataset : Pagila
Tâche 1 : Obtenir les 10 films les plus chers à louer
```sql
SELECT title, rental_rate FROM film ORDER BY rental_rate DESC LIMIT 10;

```

Tâche 2 : Obtenir la page 3 des clients (20 par page), triés par nom de famille
```sql
SELECT * FROM customer ORDER BY last_name LIMIT 20 OFFSET 40;

```

Tâche 3 : Obtenir les 5 films les plus longs, mais exclure le plus long absolu (ignorer 1, obtenir 5)
```sql
SELECT * FROM film ORDER BY length_hours LIMIT 5 OFFSET 1;

```

## Exercice 3.4 : Pratique d'INNER JOIN
Dataset : Pagila
Tâche 1 : Lister tous les films avec leurs noms de catégorie
```sql
SELECT f.title, c.name 
FROM film f
JOIN film_category fc ON f.film_id = fc.film_id
JOIN category c ON fc.category_id = c.category_id;

```

Tâche 2 : Trouver tous les paiements effectués par le client 'Mary Smith'
```sql
SELECT p.payment_date, p.amount, c.first_name, c.last_name
FROM customer c
JOIN payment p ON c.customer_id = p.customer_id;

```

Tâche 3 : Lister les films, leurs acteurs et catégories (jointure 3-voies via tables de jonction)
```sql
SELECT f.title, a.last_name, c.name
FROM film f
JOIN film_category fc ON f.film_id = fc.film_id
JOIN category c ON fc.category_id = c.category_id
JOIN film_actor fa ON f.film_id = fa.film_id
JOIN actor a ON fa.actor_id = a.actor_id;

```

## Exercice 3.5 : Pratique des JOINs externes
Dataset : Pagila
Tâche 1 : Trouver tous les acteurs qui n'ont jamais joué dans un film
```sql
SELECT a.first_name, a.last_name
FROM actor a
LEFT JOIN film_actor fa ON a.actor_id = fa.actor_id
WHERE fa.film_id = NULL;

```
Tâche 2 : Lister toutes les catégories avec leur nombre de films (y compris les catégories avec 0 film)
```sql
SELECT c.name, COUNT(fc.film_id)
FROM category c
JOIN film_category fc ON c.category_id = fc.category_id
GROUP BY c.name;

```