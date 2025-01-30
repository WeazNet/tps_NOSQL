# Initiation à Redis (TP1)

## Sommaire

1. [Installation de Docker](#1-installation-de-docker)
2. [Installation et lancement de Redis](#2-installation-et-lancement-de-redis)
3. [Accéder à Redis](#3-accéder-à-redis)
4. [Résoudre les problèmes de permissions Docker](#4-résoudre-les-problèmes-de-permissions-docker)
   1. [Ajouter votre utilisateur au groupe Docker](#ajouter-votre-utilisateur-au-groupe-docker)
   2. [Vérifier les permissions du socket Docker](#vérifier-les-permissions-du-socket-docker)
   3. [Utiliser `sudo` pour exécuter les commandes Docker](#utiliser-sudo-pour-exécuter-les-commandes-docker)
5. [Commandes Redis essentielles (REDIS 1)](#commandes-redis-essentielles)
   1. [Définir et récupérer des variables](#1-définir-et-récupérer-des-variables)
   2. [Supprimer une clé](#2-supprimer-une-clé)
   3. [Manipuler des entiers avec `incr` et `decr`](#3-manipuler-des-entiers-avec-incr-et-decr)
   4. [Gérer l'expiration des clés](#4-gérer-lexpiration-des-clés)
   5. [Travailler avec des listes](#5-travailler-avec-des-listes)
   6. [Différence entre les listes et les ensembles](#différence-entre-les-listes-et-les-ensembles)
   7. [Manipuler des ensembles](#6-manipuler-des-ensembles)
6. [Seconde vidéo (REDIS 2)](#seconde-vidéo-redis-2)
   1. [Ensembles ordonnés (Sorted Sets)](#ensembles-ordonnés-sorted-sets)
   2. [Hachages (Hashes)](#hachages-hashes)
7. [Troisième vidéo (REDIS 3)](#troisième-vidéo-redis-3)
   1. [Pub/Sub (Publication/Souscription)](#pubsub-publicationsouscription)
8. [Fonctionnalités avancées de Redis](#fonctionnalités-avancées-de-redis)
   1. [Modèle d'exécution mono-thread](#modèle-dexécution-mono-thread)
   2. [Pipeline de commandes](#pipeline-de-commandes)
   3. [Transactions](#transactions)
   4. [Réplication](#réplication)

# Mise en place de l'environnement de travail pour Redis

Avant de commencer, il est préférable de préparer correctement l'environnement de travail.

## 1. Installation de Docker

Pour installer Redis dans des conditions optimales, Docker est utilisé. Si vous utilisez Manjaro (basé sur Arch Linux), voici la commande pour installer Docker :
```bash
sudo pacman -S docker
```

## 2. Installation et lancement de Redis

Une fois Docker installé, téléchargez l'image Redis depuis le Docker Hub :
```bash
docker pull redis
```
Ensuite, lancez Redis dans un conteneur Docker avec la commande suivante :
```bash
docker run --name my-redis -p 6379:6379 -d redis
```
Cette commande exécute Redis en arrière-plan et expose le port 6379 pour permettre une interaction avec Redis depuis l'extérieur du conteneur.

## 3. Accéder à Redis

Pour interagir avec Redis via le terminal, utilisez la commande suivante :
```bash
docker exec -it my-redis redis-cli
```
Cela ouvre l'interface en ligne de commande de Redis.

## 4. Résoudre les problèmes de permissions Docker

Si vous rencontrez des problèmes de permissions lors de l'exécution des commandes Docker, suivez ces étapes :

### Ajouter votre utilisateur au groupe Docker

1. **Créer le groupe Docker (si ce n'est pas déjà fait)** :
   ```bash
   sudo groupadd docker
   ```

2. **Ajouter votre utilisateur au groupe Docker** :
   ```bash
   sudo usermod -aG docker $USER
   ```

3. **Déconnectez-vous et reconnectez-vous** pour que les modifications prennent effet.

### Vérifier les permissions du socket Docker

Assurez-vous que le socket Docker a les bonnes permissions :
```bash
sudo chmod 666 /var/run/docker.sock
```

### Utiliser `sudo` pour exécuter les commandes Docker

Si vous ne souhaitez pas ajouter votre utilisateur au groupe Docker, vous pouvez utiliser `sudo` pour exécuter les commandes Docker :
```bash
sudo docker exec -it my-redis redis-cli
```

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

  HMSET user:4 username "Augustin" age 5 email augustino.r@gmail.com
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
- **Récupérer la valeur d'un champ dans un hachage** :
  ```redis
  HGET user:4 age
  "9"
  ```
- **Récupérer toutes les valeurs d'un hachage** :
  ```redis
  HVALS user:4
  1) "Augustin"
  2) "9"
  3) "augustino.r@gmail.com"
  ```

## Troisième vidéo (REDIS 3)

La troisième vidéo parle des Pub/Sub (Publication/Souscription), qui sont très utilisés avec Redis.

### Pub/Sub (Publication/Souscription)

Le modèle Pub/Sub permet à un client de publier des messages sur un canal et à d'autres clients de s'abonner à ce canal pour recevoir les messages.

#### Exemple de Pub/Sub

**Terminal 1 : S'abonner à des canaux**

```bash
docker exec -it my-redis redis-cli
```

```redis
SUBSCRIBE mescours user:1
1) "subscribe"
2) "mescours"
3) (integer) 1
1) "subscribe"
2) "user:1"
3) (integer) 2
```

**Terminal 2 : Publier des messages**

```bash
docker exec -it my-redis redis-cli
```

```redis
PUBLISH mescours "Nouveau cours sur MongoDB"
(integer) 1
PUBLISH user:1 "Bonjour user 1 !"
(integer) 1
PUBLISH mesNotes "Nouvelle note est arrivée"
(integer) 1
PUBLISH notes "Nouvelle note est arrivée mais pas reçue par le canal"
(integer) 0
```

**Résultats obtenus dans le Terminal 1**

Après avoir publié les messages dans le Terminal 2, le Terminal 1 recevra les messages suivants :

```redis
1) "message"
2) "mescours"
3) "Nouveau cours sur MongoDB"
1) "message"
2) "user:1"
3) "Bonjour user 1 !"
```

**S'abonner à un motif de canal**

```redis
PSUBSCRIBE mes*
1) "psubscribe"
2) "mes*"
3) (integer) 1
```

**Résultats obtenus dans le Terminal 1**

Après avoir publié un message sur `mesNotes` dans le Terminal 2, le Terminal 1 recevra le message suivant :

```redis
1) "pmessage"
2) "mes*"
3) "mesNotes"
4) "Nouvelle note est arrivée"
```

### Attention en cas de panne

En cas de panne, il est important de vérifier les bases de données disponibles et de s'assurer que les données sont correctement réparties. Redis met à disposition 16 bases de données par défaut, ce qui permet de segmenter les données et de faciliter la gestion en cas de panne.

- **Sélectionner une base de données** :
  ```redis
  SELECT 1
  OK
  ```

- **Lister les clés dans une base de données** :
  ```redis
  KEYS *
  (empty array)
  ```

- **Revenir à la base de données par défaut** :
  ```redis
  SELECT 0
  OK
  ```

- **Lister les clés dans la base de données par défaut** :
  ```redis
  KEYS *
  1) "mesCours"
  2) "demo"
  3) "otherUsers"
  4) "score4"
  5) "user:4"
  6) "user:1234"
  7) "users"
  8) "23janv"
  9) "user:11"
  ```
## Fonctionnalités avancées de Redis

### Modèle d'exécution mono-thread

Redis utilise un modèle d'exécution mono-thread, ce qui signifie qu'il exécute toutes les commandes dans un seul thread. Cela simplifie le modèle de programmation et évite les problèmes de concurrence. Grâce à son architecture non bloquante, Redis peut gérer des milliers de requêtes par seconde avec une latence très faible.

### Pipeline de commandes

Le pipeline de commandes permet d'envoyer plusieurs commandes à Redis en une seule fois, réduisant ainsi la latence réseau. Cela est particulièrement utile pour les opérations en masse où de nombreuses commandes doivent être exécutées rapidement.

### Transactions

Redis supporte les transactions via la commande `MULTI/EXEC`. Les transactions permettent de grouper plusieurs commandes en une seule opération atomique, garantissant ainsi que toutes les commandes sont exécutées sans interruption.

### Réplication

Redis supporte la réplication maître-esclave, permettant de créer des copies de la base de données principale pour la redondance et la haute disponibilité. La réplication peut être configurée pour être synchrone ou asynchrone, selon les besoins de l'application.

---

Ce guide vous permet de configurer un environnement fonctionnel pour Redis, ainsi que d’apprendre les commandes essentielles pour commencer à interagir avec Redis.
