# MapReduce avec CouchDB (TP4)

## Sommaire
1. [Système de gestion de base de données documentaires : CouchDB](#syst%C3%A8me-de-gestion-de-base-de-donn%C3%A9es-documentaires--couchdb)
   - [Introduction](#introduction)
   - [Installation](#installation)
2. [Manipulation de CouchDB](#manipulation-de-couchdb)
   - [Tester le serveur CouchDB](#tester-le-serveur-couchdb)
   - [Créer une base de données "films"](#cr%C3%A9er-une-base-de-donn%C3%A9es-films)
   - [Insérer un document dans "films"](#ins%C3%A9rer-un-document-dans-films)
   - [Récupérer un document](#r%C3%A9cup%C3%A9rer-un-document)
   - [Insérer plusieurs documents en une seule requête](#ins%C3%A9rer-plusieurs-documents-en-une-seule-requ%C3%AAte)
3. [Manipulation : MapReduce](#manipulation--mapreduce)
   - [Exemple de fonction Map](#exemple-de-fonction-map)
   - [Exemple de fonction Reduce](#exemple-de-fonction-reduce)
4. [Exercice 1](#exercice-1)
   - [Modélisation d'une matrice de liens entre pages web](#mod%C3%A9lisation-dune-matrice-de-liens-entre-pages-web)
   - [Partie 1 : Calcul de la norme d'un vecteur](#partie-1--calcul-de-la-norme-dun-vecteur)
   - [Partie 2 : Produit de la matrice `M` avec un vecteur `W`](#partie-2--produit-de-la-matrice-m-avec-un-vecteur-w)
5. [Conclusion](#conclusion)
6. [Références](#r%C3%A9f%C3%A9rences)

## Système de gestion de base de données documentaires : CouchDB

### Introduction
Apache CouchDB est un système de gestion de base de données documentaires open-source, simple à installer et à utiliser. Il est particulièrement adapté aux applications utilisant des API REST et facilite la compréhension des concepts des bases de données NoSQL.

CouchDB expose des fonctionnalités sous forme d'API REST :
  - **GET** : Récupérer la représentation d'une ressource.
  - **PUT** : Créer ou mettre à jour une ressource.
  - **POST** : Envoyer des données à une ressource.
  - **DELETE** : Supprimer une ressource.

### Installation
CouchDB peut être installé de deux manières :

1. **Installation via le site officiel** : [CouchDB Apache](https://couchdb.apache.org/)
2. **Installation avec Docker** :
   ```bash
   docker run -d --name couchdbdemo -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=admin -p 5984:5984 couchdb
   ```

CouchDB propose une interface graphique accessible via : `http://localhost:5984/_utils`.

---

## Manipulation de CouchDB

### Tester le serveur CouchDB
```bash
curl -X GET http://admin:admin@localhost:5984
```

### Créer une base de données "films"
```bash
curl -X PUT http://admin:admin@localhost:5984/films
```

### Insérer un document dans "films"
```bash
curl -X POST http://admin:admin@localhost:5984/films -d '{"title":"Inception", "year":2010, "director":"Christopher Nolan"}' -H "Content-Type: application/json"
```

### Récupérer un document
```bash
curl -X GET http://admin:admin@localhost:5984/films/{doc_id}
```

### Insérer plusieurs documents en une seule requête
```bash
curl -X POST http://admin:admin@localhost:5984/films/_bulk_docs -d @films_couchdb.json -H "Content-Type: application/json"
```

---

## Manipulation : MapReduce
CouchDB utilise MapReduce pour exécuter des requêtes complexes sur les documents de la base.

### Exemple de fonction Map
```javascript
function (doc) {
  if (doc.year && doc.title) {
    emit(doc.year, doc.title);
  }
}
```

### Exemple de fonction Reduce
```javascript
function (keys, values, rereduce) {
  return values.length;
}
```

---

## Exercice 1

### Modélisation d'une matrice de liens entre pages web
Représentation d'une matrice `M` de dimension `N x N` où chaque ligne `i` correspond aux liens et poids d'une page `P_i`.

### Partie 1 : Calcul de la norme d'un vecteur
**Fonction Map**
```javascript
function (doc) {
    var sum = 0;
    for (var key in doc) {
        if (!isNaN(doc[key])) {
            sum += Math.pow(doc[key], 2);
        }
    }
    emit(doc._id, sum);
}
```
**Fonction Reduce**
```javascript
function (keys, values, rereduce) {
    return Math.sqrt(values.reduce((a, b) => a + b, 0));
}
```

### Partie 2 : Produit de la matrice `M` avec un vecteur `W`
**Fonction Map**
```javascript
function (doc) {
    var result = 0;
    for (var key in doc.values) {
        result += doc.values[key] * W[key];
    }
    emit(doc._id, result);
}
```
**Fonction Reduce**
```javascript
function (keys, values, rereduce) {
    return values.reduce((a, b) => a + b, 0);
}
```

---

## Conclusion
CouchDB est un excellent outil pour comprendre les bases de données documentaires et les architectures REST. Son système MapReduce permet d'effectuer des traitements efficaces et scalables.

---

## Références
- [Documentation officielle CouchDB](https://docs.couchdb.org/en/stable/)

