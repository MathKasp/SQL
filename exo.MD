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
        CHECK (contenu >= 1)
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

```
ALTER TABLE tags ADD COLUMN description TEXT;
ALTER TABLE tags ADD COLUMN compteur_utilisation INTEGER DEFAULT 0;
ALTER TABLE tags ADD CONSTRAINT chk_compteur_utilisation_positif CHECK (compteur_utilisation >= 0);
ALTER TABLE tags ADD CONSTRAINT chk_nom_plus_2_car CHECK (LENGTH(nom) >= 2);
ALTER TABLE tags RENAME COLUMN cree_le TO date_creation;
```