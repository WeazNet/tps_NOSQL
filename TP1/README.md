
---

## Mise en place de l'environnement de travail pour Redis

Avant de commencer, il est préférable de préparer correctement l'environnement de travail.

### 1. Installation de Docker

Pour installer Redis dans des conditions optimales, Docker est utilisé. Si vous utilisez Manjaro (basé sur Arch Linux), voici la commande pour installer Docker :
```bash
sudo pacman -S docker
```

### 2. Installation et lancement de Redis

Une fois Docker installé, téléchargez l'image Redis depuis le Docker Hub :
```bash
docker pull redis
```
Ensuite, lancez Redis dans un conteneur Docker avec la commande suivante :
```bash
docker run --name my-redis -p 6379:6379 -d redis
```
Cette commande exécute Redis en arrière-plan et expose le port 6379 pour permettre une interaction avec Redis depuis l'extérieur du conteneur.

### 3. Accéder à Redis

Pour interagir avec Redis via le terminal, utilisez la commande suivante :
```bash
docker exec -it my-redis redis-cli
```
Cela ouvre l'interface en ligne de commande de Redis.

---

## Commandes Redis essentielles

Voici quelques commandes de base pour interagir avec les données stockées dans Redis.

### 1. Définir et récupérer des variables

- **Définir une clé** :
  ```redis
  set demo "Bonjour"
  OK
  ```
- **Récupérer une clé** :
  ```redis
  get demo
  "Bonjour"
  ```

### 2. Supprimer une clé

- **Supprimer une clé existante** :
  ```redis
  del demo
  (integer) 1
  ```

### 3. Manipuler des entiers avec `incr` et `decr`

- **Incrémenter une valeur** :
  ```redis
  incr counter
  (integer) 1
  ```
- **Décrémenter une valeur** :
  ```redis
  decr counter
  (integer) 0
  ```

### 4. Gérer l'expiration des clés

- **Vérifier l'expiration d'une clé** :
  ```redis
  ttl mykey
  (integer) -1
  ```
- **Définir une expiration pour une clé** :
  ```redis
  expire mykey 120
  (integer) 1
  ```

### 5. Travailler avec des listes

- **Ajouter des éléments à une liste** :
  ```redis
  RPUSH mesCours "BDA"
  LPUSH mesCours "Systèmes distribués"
  ```
- **Afficher les éléments d'une liste** :
  ```redis
  LRANGE mesCours 0 -1
  ```
- **Retirer un élément d'une liste** :
  ```redis
  LPOP mesCours
  "Systèmes distribués"
  ```

### Différence entre les listes et les ensembles

Les listes et les ensembles sont deux structures de données différentes dans Redis :

- **Listes** : Les listes peuvent contenir des éléments dupliqués et l'ordre des éléments est important.
- **Ensembles** : Les ensembles ne peuvent pas contenir des éléments dupliqués et l'ordre des éléments n'est pas garanti.

### 6. Manipuler des ensembles

- **Ajouter des éléments à un ensemble** :
  ```redis
  SADD users "admin"
  ```
- **Afficher les membres d'un ensemble** :
  ```redis
  SMEMBERS users
  ```
- **Supprimer un élément d'un ensemble** :
  ```redis
  SREM users "admin"
  ```
- **Union de plusieurs ensembles** :
  ```redis
  SUNION users otherUsers
  ```

---

## Seconde vidéo (REDIS 2)

La seconde vidéo introduit des concepts et des commandes plus avancés, notamment les ensembles ordonnés (sorted sets) et les hachages (hashes).

### Ensembles ordonnés (Sorted Sets)

Les ensembles ordonnés permettent de stocker des éléments avec un score associé, ce qui permet de les trier.

- **Ajouter des éléments à un ensemble ordonné** :
  ```redis
  ZADD score4 19 "admin"
  ZADD score4 18 "user2"
  ZADD score4 8 "any"
  ```
- **Afficher les éléments d'un ensemble ordonné par ordre croissant** :
  ```redis
  ZRANGE score4 0 -1
  1) "any"
  2) "user2"
  3) "admin"
  ```
- **Afficher les éléments d'un ensemble ordonné par ordre décroissant** :
  ```redis
  ZREVRANGE score4 0 -1
  1) "admin"
  2) "user2"
  3) "any"
  ```
- **Obtenir le rang d'un élément** :
  ```redis
  ZRANK score4 "user2"
  (integer) 1
  ```

### Hachages (Hashes)

Les hachages permettent de stocker des objets avec plusieurs champs.

- **Définir des champs dans un hachage** :
  ```redis
  HSET user:11 username "tanasov"
  HSET user:11 age 22
  HSET user:11 email vladalexandru.tanasov@edu.univ-paris13.fr
  ```
- **Récupérer tous les champs et valeurs d'un hachage** :
  ```redis
  HGETALL user:11
  1) "username"
  2) "tanasov"
  3) "age"
  4) "22"
  5) "email"
  6) "vladalexandru.tanasov@edu.univ-paris13.fr"
  ```
- **Incrémenter une valeur dans un hachage** :
  ```redis
  HINCRBY user:4 age 4
  (integer) 9
  ```
- **Récuperer la valeur d'un champ** :
  ```redis
  HGET user:4 age
  "9"
  ```
---

Ce guide vous permet de configurer un environnement fonctionnel pour Redis, ainsi que d’apprendre les commandes essentielles pour commencer à interagir avec Redis.