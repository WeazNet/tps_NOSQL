# Initiation à CouchDB (TP3)

## Introduction
Ce rapport vise à expliquer les concepts de MapReduce en mode centralisé, en utilisant CouchDB. Il présente également une introduction à CouchDB et propose une modélisation sous forme de documents structurés pour représenter une matrice de liens entre pages web. Enfin, nous détaillons la mise en place des traitements MapReduce pour le calcul de la norme des vecteurs et du produit matriciel avec un vecteur.

## 1. Introduction à CouchDB
CouchDB est une base de données NoSQL orientée documents qui utilise le format JSON pour le stockage des données. Elle repose sur un moteur de requêtes basé sur MapReduce et supporte le protocole HTTP pour l'interaction avec les données. Contrairement aux bases relationnelles, CouchDB permet une réplication facile et une grande scalabilité grâce à son modèle distribué.

### Principales caractéristiques de CouchDB :
- Stockage des données sous forme de documents JSON.
- Accès aux données via des requêtes HTTP RESTful.
- Système de vues indexées basé sur MapReduce.
- Réplication et synchronisation facile des bases.

## 2. Exercice 1 : Modélisation et traitement MapReduce

1. Proposer un modèle, sous forme de documents structurés, pour représenter une telle matrice (s'inspirer du cas Page Rank du moteur de recherche Google, vu en cours). Soit C la collection ainsi obtenue.
2. La ligne i peut être vue comme un vecteur à N dimensions décrivant la page Pi. Spécifiez le traitement MapReduce qui calcule la norme de ces vecteurs à partir des documents de la collection C. La norme d'un vecteur V(v1,v2,...,vN) est le scalaire \[ ||V|| = \sqrt{v_1^2 + v_2^2 + ... + v_N^2} \]
3. Nous voulons calculer le produit de la matrice M avec un vecteur de dimension N, W(w1,w2,...,wN). Le résultat est un vecteur \[ \phi_i = \sum_{j=1}^{N} M_{ij} \times w_j \] On suppose que le vecteur W tient en mémoire RAM et est accessible comme variable statique par toutes les fonctions de Map ou de Reduce. Spécifiez le traitement MapReduce qui implante ce calcul.

## 3. Représentation de la matrice M sous forme de documents

Nous devons modéliser une matrice M de dimension N×N, où chaque ligne représente une page web avec des liens vers d'autres pages, associés à un poids. Chaque ligne i de M est un vecteur à N dimensions correspondant à la page Pi.

Nous pouvons représenter chaque ligne comme un document JSON stocké dans CouchDB :

```json
{
  "_id": "page_i",
  "links": [
    { "target": "page_j", "weight": 0.5 },
    { "target": "page_k", "weight": 0.3 }
  ]
}
```

Où :
- `_id` identifie la page web.
- `links` contient une liste d'objets représentant les liens vers d'autres pages et leurs poids.

## 4. Traitement MapReduce pour le calcul de la norme des vecteurs

La norme d'un vecteur V(v1,v2,...,vN) est définie par :

\[ ||V|| = \sqrt{v_1^2 + v_2^2 + ... + v_N^2} \]

Nous utilisons MapReduce pour calculer cette norme :

### Fonction Map
Cette fonction extrait les poids des liens et élève leurs valeurs au carré.

```javascript
function (doc) {
  var sumSquares = 0;
  for (var i = 0; i < doc.links.length; i++) {
    sumSquares += Math.pow(doc.links[i].weight, 2);
  }
  emit(doc._id, sumSquares);
}
```

### Fonction Reduce
Cette fonction additionne toutes les valeurs partielles et calcule la racine carrée du résultat final.

```javascript
function (keys, values, rereduce) {
  var sum = 0;
  for (var i = 0; i < values.length; i++) {
    sum += values[i];
  }
  return Math.sqrt(sum);
}
```

## 5. Traitement MapReduce pour le produit matriciel M * W

Nous souhaitons calculer le produit matriciel suivant :

\[ \phi_i = \sum_{j=1}^{N} M_{ij} \times w_j \]

Nous supposons que le vecteur W est accessible en tant que variable statique.

### Fonction Map
Chaque élément de la matrice est multiplié par la valeur correspondante du vecteur W.

```javascript
var W = { "page_1": 0.2, "page_2": 0.4, "page_3": 0.1 };  // Exemple de vecteur statique

function (doc) {
  var result = 0;
  for (var i = 0; i < doc.links.length; i++) {
    var target = doc.links[i].target;
    if (W[target] !== undefined) {
      result += doc.links[i].weight * W[target];
    }
  }
  emit(doc._id, result);
}
```

### Fonction Reduce
Cette fonction additionne les résultats pour obtenir le vecteur final \( \phi \).

```javascript
function (keys, values, rereduce) {
  return sum(values);
}
```

