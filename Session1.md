
**Session 1 : Introduction et Manipulation des Données (3h)**

**1. Présentation de MongoDB**


## 1. Présentation de MongoDB

### Pourquoi MongoDB ?

MongoDB est une base de données NoSQL orientée documents, offrant une grande flexibilité et une scalabilité horizontale.

#### Caractéristiques principales de MongoDB

##### 1. **Schema-less**

Contrairement aux bases SQL qui nécessitent une structure rigide avec des tables et des colonnes définies à l'avance, MongoDB permet de stocker des documents sans schéma prédéfini. Cela signifie que :

-   Les documents d'une même collection peuvent avoir des champs différents.
    
-   Il est facile d'ajouter ou de supprimer des champs sans migration complexe.
    
-   Cette flexibilité est particulièrement utile pour les applications en évolution rapide.
    

##### 2. **JSON-like et BSON**

MongoDB stocke les données sous forme de documents **BSON (Binary JSON)**, un format binaire optimisé basé sur JSON, qui offre plusieurs avantages :

-   **Facilité de manipulation** : BSON est proche du JSON, couramment utilisé dans les applications web modernes.
    
-   **Types de données enrichis** : BSON prend en charge des types supplémentaires comme les dates et les objets imbriqués.
    
-   **Performance** : Format binaire optimisé pour la lecture et l'écriture rapide.
    

##### 3. **Scalabilité**

MongoDB supporte la **scalabilité horizontale** grâce au **sharding**, qui permet de répartir les données sur plusieurs serveurs. Avantages :

-   **Gestion efficace des grandes quantités de données** : En divisant les collections en fragments (shards) stockés sur différents nœuds.
    
-   **Équilibrage de charge** : Chaque serveur ne gère qu'une fraction des requêtes, améliorant la rapidité d'accès.
    
-   **Répartition automatique** : MongoDB distribue les données en fonction d'une clé de sharding.
    

##### 4. **Haute disponibilité avec Replica Sets**

MongoDB garantit une disponibilité continue des données grâce aux **Replica Sets**, qui sont des ensembles de nœuds répliquant les données en temps réel :

-   **Failover automatique** : Si le serveur principal (primary) tombe, un secondaire (secondary) prend automatiquement le relais.
    
-   **Lecture évolutive** : Possibilité de lire depuis les serveurs secondaires pour alléger la charge sur le serveur principal.
    
-   **Sécurité des données** : Chaque réplique contient une copie complète des données, réduisant le risque de perte.
-   
**Différences avec les bases SQL**

|**Caractéristique**| **SQL (ex: MySQL, PostgreSQL)**|**MongoDB**|
|  --------  |  -------  |  --------  | 
|Structure| Tables, lignes, colonnes| Collections, documents|
| Schéma|Fixe (défini à l'avance)| Flexible| 
| Langage de requêtes| SQL| API JSON-like|
| Transactions| ACID (fortes garanties)| ACID pour les transactions multi-documents| 
| Scalabilité| Verticale (augmentation des ressources serveur)| Horizontale (sharding)| 

**2. Architecture et composants clés**

**Éléments fondamentaux de MongoDB**

-   **Base de données** : Ensemble de collections.
-   **Collection** : Équivalent des tables en SQL.
-   **Document** : Équivalent des lignes, stocké en BSON.
-   **BSON** : Format binaire optimisé pour MongoDB (similaire à JSON, mais avec des types de données supplémentaires).

**Installation et outils**

-   **MongoDB Server** : Service principal de base de données.
-   **MongoDB Compass** : Interface graphique pour manipuler les bases.
-   **MongoDB Shell** : Interface en ligne de commande pour exécuter des requêtes.
-   **Drivers** : Bibliothèques pour interagir avec MongoDB depuis divers langages (Node.js, Python, Java, etc.).

**3. Opérations CRUD**

**a) Create (Insertion de documents)**

**Insérer un document unique**

```js
db.users.insertOne({ name: "Alice", age: 30, email: "alice@example.com" });

```


**Insérer plusieurs documents**

```js
db.users.insertMany([

{ name: "Bob", age: 25 },

{ name: "Charlie", age: 35 }

]);
```


**b) Read (Lecture des données)**

**Trouver tous les documents**

```js

db.users.find();
```
**Filtrage**

```js
db.users.find({ age: { $gte: 30 } });
```

**Projection (sélectionner uniquement certains champs)**

```js
db.users.find({}, { name: 1, _id: 0 });
```

**c) Update (Mise à jour de documents)**

**Mettre à jour un document unique**

```js
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } });
```

**Mettre à jour plusieurs documents**

```js
db.users.updateMany({ age: { $lt: 30 } }, { $inc: { age: 1 } });
```

**d) Delete (Suppression de documents)**

**Supprimer un document**

```js
db.users.deleteOne({ name: "Alice" });
```

**Supprimer plusieurs documents**

```js
db.users.deleteMany({ age: { $gt: 35 } });
```

**4. Indexation de base**

**Importance des index**

Les index améliorent les performances des requêtes en évitant les scans complets des collections.

**Création d’un index simple**

```js
db.users.createIndex({ name: 1 });
```

**Suppression d’un index**

```js
db.users.dropIndex("name_1");
```


## 💻 Lab 1 : Premiers Pas avec MongoDB (1h)

### 1. Installation et Configuration

-   Installer MongoDB Server et MongoDB Compass.
    
-   Vérifier l’installation avec `mongod --version` et `mongo --version`.
    
-   Se connecter à la base avec MongoDB Compass et créer une première base de données.
    

### 2. Manipulation des documents avec MongoDB Shell

-   Créer une collection `users`.
    
-   Insérer plusieurs documents avec différentes structures.
    
-   Tester des requêtes `find()` avec filtres et projections.
    

### 3. Import et Export de données JSON

-   Créer un fichier `users.json` contenant une liste d’utilisateurs.
    
-   Importer ces données avec la commande MongoDB `mongoimport`.
    
-   Exporter les données en JSON avec `mongoexport`.
    

### 4. Exercices CRUD sur une base de test

#### Objectif : Appliquer les opérations CRUD dans un scénario réel.

1.  **Insertion** : Ajouter 10 utilisateurs avec des données variées (nom, âge, email, ville, téléphone).
    
2.  **Lecture** :
    
    -   Récupérer tous les utilisateurs.
        
    -   Trouver ceux qui ont plus de 25 ans.
        
    -   Sélectionner uniquement les noms et emails.
        
3.  **Mise à jour** :
    
    -   Augmenter de 2 ans l’âge de tous les utilisateurs de plus de 30 ans.
        
    -   Ajouter un champ `status: "active"` aux utilisateurs qui n'en ont pas.
        
4.  **Suppression** :
    
    -   Supprimer un utilisateur spécifique.
        
    -   Supprimer tous les utilisateurs qui n’ont pas d’email.
        
5.  **Indexation** :
    
    -   Créer un index sur le champ `email`.
        
    -   Vérifier l’impact sur les performances avec `explain()`.
