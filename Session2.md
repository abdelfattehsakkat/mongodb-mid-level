# 🎯 Session 2 : Modélisation et Agrégation (3h)

## 🔹 1. Modélisation des données

### 🆚 Approche document vs relationnelle

| Aspect                        | Relationnel (SQL)                         | Document (MongoDB)                          |
|------------------------------|-------------------------------------------|--------------------------------------------|
| Unité de stockage            | Ligne dans une table                      | Document dans une collection               |
| Représentation des relations | Clés étrangères, jointures                | Documents imbriqués ou référencés          |
| Schéma                       | Fixe et contraint                         | Flexible (schema-less)                     |
| Mise à jour                  | Plus coûteuse (join, contraintes)         | Rapide si documents bien modélisés         |
| Scalabilité                  | Verticale principalement                  | Horizontale (facilement distribuable)      |

### 🧩 Structuration des documents : Embedded vs References

#### 📌 Embedded Documents (Imbrication)
Quand les données sont souvent accédées ensemble.

```json
{
  "title": "Article 1",
  "author": {
    "name": "Alice",
    "email": "alice@example.com"
  },
  "tags": ["mongodb", "nosql"]
}
```

**Avantages** :
- Lecture rapide (un seul document à lire)
- Cohérence locale

**Inconvénients** :
- Taille du document limitée (16 Mo)
- Risque de duplication si les données sont partagées

#### 🔗 References (Références entre documents)
Quand les données sont partagées ou accédées indépendamment.

```json
// Article
{
  "title": "Article 1",
  "authorId": ObjectId("...")
}
```

```json
// Author
{
  "_id": ObjectId("..."),
  "name": "Alice",
  "email": "alice@example.com"
}
```

**Avantages** :
- Évite la duplication
- Plus souple pour les données fortement liées

**Inconvénients** :
- Besoin de plusieurs requêtes ou d’un $lookup

### 🔄 Relations entre documents avec `$lookup`

Permet d’effectuer une jointure entre collections, semblable à un `LEFT JOIN`.

```js
db.articles.aggregate([
  {
    $lookup: {
      from: "authors",
      localField: "authorId",
      foreignField: "_id",
      as: "author"
    }
  }
])
```

## ⚠️ Bonnes pratiques & erreurs de modélisation

### ✅ Best practices
- Favoriser les **documents imbriqués** lorsque les données sont toujours accédées ensemble.
- Préférer les **références** lorsque les sous-documents deviennent trop volumineux ou sont partagés.
- Utiliser des **index adaptés** aux requêtes fréquentes.
- Bien nommer les collections et champs (clairs, en camelCase).
- Limiter la taille des documents (< 16 Mo).
- Documenter la structure attendue avec des exemples ou des JSON Schema.

### ❌ Anti-patterns courants
- ❌ **Trop de relations simulées** : copier le modèle SQL avec des $lookup partout.
- ❌ **Sous-documents trop imbriqués** : structure trop complexe à manipuler.
- ❌ **Modèle unique pour tout** : vouloir une seule collection pour différents usages.
- ❌ **Données volatiles dans des documents partagés** : provoque des conflits de mise à jour.
- ❌ **Manque d’indexation** sur les champs filtrés ou triés souvent.

---

## 🔹 2. Agrégation et requêtes avancées

### 🔄 Pipeline d’agrégation

L'agrégation dans MongoDB repose sur un pipeline de transformation de données. Chaque **stage** prend les résultats du précédent et les modifie.

Principaux stages :

| Stage     | Rôle                                                                 |
|-----------|----------------------------------------------------------------------|
| `$match`  | Filtrer les documents (équivalent du `WHERE`)                        |
| `$project`| Sélectionner / transformer les champs                                |
| `$group`  | Grouper les documents par une clé, appliquer des opérations (somme…) |
| `$sort`   | Trier les documents                                                   |
| `$limit`  |                                                                       |
|`$skip`    | Pagination                                                            |

#### Exemple de pipeline
```js
db.orders.aggregate([
  { $match: { status: "paid" } },
  { $group: { _id: "$customerId", totalSpent: { $sum: "$amount" } } },
  { $sort: { totalSpent: -1 } }
])
```

### 🧠 Expressions et opérateurs
MongoDB permet des opérations complexes :
- `$sum`, `$avg`, `$min`, `$max`
- `$concat`, `$toUpper`, `$dateToString`
- `$cond`, `$ifNull`, `$switch`

### ⚡️ Pourquoi préférer les requêtes complexes côté MongoDB ?

Plutôt que de transférer de grandes quantités de données vers l’application pour les filtrer, transformer ou agréger en JavaScript, il est bien plus performant et scalable d’exécuter ces opérations directement côté MongoDB. Cela permet :

- Une réduction significative du volume de données transférées sur le réseau.
- Une centralisation de la logique métier d’analyse (calculs, statistiques) dans la base de données.
- Une meilleure utilisation des ressources MongoDB, notamment via les index et l’optimisation native du moteur d’agrégation.
- Un code backend plus simple, car les résultats sont déjà transformés et prêts à être affichés ou utilisés.

**Exemple** : inutile de ramener toutes les commandes pour calculer le chiffre d’affaires mensuel côté backend. Un ``$group`` avec ``$dateToString`` fera le travail directement dans la base.

---

## 💻 Lab 2 : Modélisation et Agrégation

### 🎯 Objectif
Manipuler la modélisation des documents et pratiquer les agrégations avec un scénario réaliste (e-commerce).

### ⚙️ Scénario
Vous travaillez sur une base de données e-commerce avec deux collections :
- `products` : liste des produits
- `orders` : commandes contenant des références produit et client

```js
// products
{
  _id: ObjectId("..."),
  name: "Laptop",
  category: "electronics",
  price: 1000
}

// orders
{
  _id: ObjectId("..."),
  customer: "Alice",
  items: [
    { productId: ObjectId("..."), quantity: 2 },
    { productId: ObjectId("..."), quantity: 1 }
  ],
  status: "paid",
  createdAt: ISODate("2024-01-01T10:00:00Z")
}
```

### ✅ Étapes
#### Etape 1
1. **Créer les collections `products` et `orders`** avec quelques documents.
2. **Écrire des requêtes d’agrégation** pour :
   - Total des ventes par produit (`$unwind`, `$group`)
   - Panier moyen par client
   - Commandes par mois (`$group` + `$dateToString`)
3. **Utiliser `$lookup`** pour enrichir les commandes avec les détails des produits.
4. **Construire une vue consolidée** : commande + produits + montant total par ligne dans un seul document enrichi, incluant :
   - La commande : son identifiant, date, client, etc.
   - Les produits de cette commande : nom, prix, etc., récupérés via $lookup (puis $unwind si nécessaire).
   - Le montant total par ligne d’achat : prix * quantité pour chaque produit acheté.

📦 Exemple d’objectif :
Prenons une commande comme celle-ci :

```json
{
  "_id": ObjectId("..."),
  "customer": "Alice",
  "items": [
    { "productId": ObjectId("p1"), "quantity": 2 },
    { "productId": ObjectId("p2"), "quantity": 1 }
  ]
}
```
Et deux produits :

```json
{ "_id": ObjectId("p1"), "name": "Laptop", "price": 1000 }
{ "_id": ObjectId("p2"), "name": "Mouse", "price": 50 }
```
La sortie doit ressembler à ce document :

```json
{
  "_id": ObjectId("..."),
  "customer": "Alice",
  "items": [
    {
      "productId": ObjectId("p1"),
      "productName": "Laptop",
      "price": 1000,
      "quantity": 2,
      "lineTotal": 2000
    },
    {
      "productId": ObjectId("p2"),
      "productName": "Mouse",
      "price": 50,
      "quantity": 1,
      "lineTotal": 50
    }
  ],
  "totalOrderAmount": 2050
}
```
#### 🔧 Hints
 - $unwind sur items
 - $lookup vers products en utilisant items.productId
 - $addFields pour calculer lineTotal
 - $group pour regrouper les lignes et reconstituer la commande avec items enrichis
 - $addFields pour calculer totalOrderAmount (optionnel)

### 🌟 Bonus
Créer une version avec documents imbriqués, comparer avec la version référencée :
- Impact sur la taille
- Simplicité des requêtes

