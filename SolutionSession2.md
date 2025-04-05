 **Construire une vue consolidée** : commande + produits + montant total par ligne dans un seul document enrichi, incluant :
   - La commande : son identifiant, date, client, etc.
   - Les produits de cette commande : nom, prix, etc., récupérés via $lookup (puis $unwind si nécessaire).
   - Le montant total par ligne d’achat : prix * quantité pour chaque produit acheté.


## Proposition de solution

```json
db.orders.aggregate([
  { $unwind: "$items" },
  {
    $lookup: {
      from: "products",
      localField: "items.productId",
      foreignField: "_id",
      as: "product"
    }
  },
  { $unwind: "$product" },
  {
    $addFields: {
      "items.productName": "$product.name",
      "items.price": "$product.price",
      "items.lineTotal": { $multiply: ["$items.quantity", "$product.price"] }
    }
  },
  {
    $group: {
      _id: "$_id",
      customer: { $first: "$customer" },
      status: { $first: "$status" },
      createdAt: { $first: "$createdAt" },
      items: { $push: "$items" },
      totalOrderAmount: { $sum: { $multiply: ["$items.quantity", "$product.price"] } }
    }
  }
])

```
