## Mise en place de l'environnement de travail pour Redis
Avant de commencer, il est préférable de préparer correctement l'environnement de travail.

---

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

Ce guide vous permet de configurer un environnement fonctionnel pour Redis, ainsi que d’apprendre les commandes essentielles pour commencer à interagir avec Redis.

