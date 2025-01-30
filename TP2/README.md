# Initiation à MongoDB (TP2)

## Sommaire
1. [Installation de MongoDB avec Docker](#installation-de-mongodb-avec-docker)
2. [Importation des données](#importation-des-données)
3. [Vérification des données](#vérification-des-données)
4. [Requêtes MongoDB](#requêtes-mongodb)
    - [Afficher un exemple de document](#afficher-un-exemple-de-document)
    - [Lister les films d'action](#lister-les-films-daction)
    - [Compter les films d'action](#compter-les-films-daction)
    - [Filtrer par pays et année](#filtrer-par-pays-et-année)
    - [Exclure certains champs](#exclure-certains-champs)
    - [Afficher les films selon des critères avancés](#afficher-les-films-selon-des-critères-avancés)
    - [Lister les différents genres et grades](#lister-les-différents-genres-et-grades)
    - [Requêtes sur les acteurs](#requêtes-sur-les-acteurs)
5. [Conclusion](#conclusion)

---

## Installation de MongoDB avec Docker

Pour installer MongoDB via Docker, suivez ces étapes :

1. **Téléchargez et installez Docker** :
   ```sh
   docker pull mongodb/mongodb-community-server:latest
   ```
2. **Lancez un conteneur MongoDB avec Docker** :
   ```sh
   docker run --name mongodb -d -p 27017:27017 mongo
   ```
3. **Vérifiez que le conteneur est bien en cours d’exécution** :
   ```sh
   docker ps
   ```

---

## Importation des données

4. **Téléchargez et installez le fichier `films.json`** :
   - [Télécharger films.json ici](./films.json)

5. **Importez les données depuis `films.json`** :
   ```sh
   docker cp films.json mongodb:/films.json
   docker exec -it mongodb mongoimport --db lesfilms --collection films --file /films.json --jsonArray
   ```
   Si tout se passe bien, vous devriez voir :
   ```sh
   2025-01-30T07:59:00.422+0000	connected to: mongodb://localhost/
   2025-01-30T07:59:00.454+0000	278 document(s) imported successfully. 0 document(s) failed to import.
   ```
6. **Accédez au shell MongoDB** :
   ```sh
   docker exec -it mongodb mongosh
   ```
7. **Sélectionnez la base de données `lesfilms`** :
   ```sh
   use lesfilms
   ```

---

## Vérification des données

### 1. Vérifier que les données ont été importées :
```sh
db.films.countDocuments()
```

### 2. Afficher un exemple de document :
```sh
db.films.findOne()
```

---

## Requêtes MongoDB

### Lister les films d'action :
```sh
db.films.find({ genre: "Action" })
```

### Compter les films d'action :
```sh
db.films.countDocuments({ genre: "Action" })
```

### Filtrer par pays et année :
- Films d'action produits en France :
  ```sh
  db.films.find({ genre: "Action", country: "FR" })
  ```
- Films d'action produits en France en 1963 :
  ```sh
  db.films.find({ genre: "Action", country: "FR", year: 1963 })
  ```

### Exclure certains champs :
- Films d'action réalisés en France sans les grades :
  ```sh
  db.films.find({ genre: "Action", country: "FR" }, { grades: 0 })
  ```
- Ne pas afficher les identifiants (_id) :
  ```sh
  db.films.find({ genre: "Action", country: "FR" }, { _id: 0, grades: 0 })
  ```
- Afficher les titres et grades des films d'action réalisés en France :
  ```sh
  db.films.find({ genre: "Action", country: "FR" }, { _id: 0, title: 1, grades: 1 })
  ```

### Afficher les films selon des critères avancés :
- Films d'action en France avec une note supérieure à 10 :
  ```sh
  db.films.find({ genre: "Action", country: "FR", "grades.note": { $gt: 10 } }, { _id: 0, title: 1, grades: 1 })
  ```
- Films d'action en France ayant uniquement des notes supérieures à 10 :
  ```sh
  db.films.find({ 
      genre: "Action", 
      country: "FR", 
      grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } 
  }, { _id: 0, title: 1, grades: 1 })
  ```

### Lister les différents genres et grades :
- Différents genres de films :
  ```sh
  db.films.distinct("genre")
  ```
- Différents grades attribués :
  ```sh
  db.films.distinct("grades.grade")
  ```

### Requêtes sur les acteurs :
- Films avec un des artistes `["artist:4","artist:18","artist:11"]` :
  ```sh
  db.films.find({ "actors._id": { $in: ["artist:4", "artist:18", "artist:11"] } })
  ```
- Films sans résumé :
  ```sh
  db.films.find({ summary: { $exists: false } })
  ```
- Films avec Leonardo DiCaprio en 1997 :
  ```sh
  db.films.find({ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo", year: 1997 })
  ```
- Films avec Leonardo DiCaprio **ou** sortis en 1997 :
  ```sh
  db.films.find({ $or: [{ "actors.last_name": "DiCaprio", "actors.first_name": "Leonardo" }, { year: 1997 }] })
  ```

---

## Conclusion

Ce guide vous a permis de :
- Installer MongoDB avec Docker.
- Importer une base de films JSON.
- Effectuer des requêtes de base et avancées sur MongoDB.
