
**Session 1 : Introduction et Manipulation des Données (3h)**

**1. Présentation de MongoDB**

**Pourquoi MongoDB ?**

MongoDB est une base de données NoSQL orientée documents, offrant une grande flexibilité et une scalabilité horizontale.

-   **Schema-less** : Contrairement aux bases SQL, MongoDB n'impose pas de structure fixe.
-   **JSON-like** : Utilise BSON (Binary JSON) pour stocker les données.
-   **Scalabilité** : Gestion facile du sharding pour répartir la charge.
-   **Haute disponibilité** : Grâce aux Replica Sets.

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

**Exercices pratiques**

💻 **Exercice 1 : Installation et Connexion**

-   Installer MongoDB et MongoDB Compass.
-   Créer une base de données et une collection.

💻 **Exercice 2 : Manipulation CRUD**

-   Insérer 5 documents.
-   Lire tous les documents.
-   Trouver un document avec un filtre.
-   Mettre à jour un document.
-   Supprimer un document.

💻 **Exercice 3 : Indexation**

-   Créer un index sur un champ.
-   Observer les performances avec et sans index
