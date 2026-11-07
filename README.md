# Clusterisation de conteneurs : My Favorite Places

Chaque question des TD est suivie de sa capture.

| Cours | Exercices |
|---|---|
| [1 - Introduction](#1---introduction) | TD 1, TD 2 |
| [2 - Docker Swarm](#2---docker-swarm) | TD 1, prérequis, TD 2, TD 3 |
| [4 - Traefik et Portainer](#4---traefik-et-portainer) | Exercices 1 à 4 |
| [5 - Secrets et configs](#5---secrets-et-configs) | Exercice 1 |

---

## 1 - Introduction

### TD 1 : dockerisation du projet

**1. Tester le serveur avec `yarn dev` et faire une requête avec Bruno**

![](docs/screens/01-yarn-dev.png)

![](docs/screens/02-bruno-local.png)

**2. Dockeriser le serveur et retester avec Bruno**

![](docs/screens/03-docker-ps.png)

![](docs/screens/03-bruno-docker.png)

**3. `compose.yml` qui lance le serveur et une BDD PostgreSQL**

![](docs/screens/04-compose-server-bdd.png)

**4. Tester le client React, le dockeriser et l'ajouter au `compose.yml`**

![](docs/screens/05-client-cli.png)

![](docs/screens/05-client-web.png)

![](docs/screens/06-compose-complet.png)

**5. Comment communiquent la BDD et le serveur ? Le serveur et le front ?**

- BDD et serveur : par le réseau Docker de Compose, le serveur joint la base avec le nom du service, `db:5432`.
- Serveur et front : le navigateur appelle `/api` sur le front, et Nginx transmet la requête à `server:3000`.

### TD 2

**1. Démarrer le serveur une fois la BDD prête**

`healthcheck` sur `db` et `depends_on: condition: service_healthy` sur `server` : `db` passe `Healthy`, puis `server` démarre.

![](docs/screens/04-compose-server-bdd.png)

**2. Redémarrage automatique, sauf arrêt manuel**

`restart: unless-stopped` sur les 3 services de `compose.yml`.

**3. Build stages et image du serveur allégée d'environ 100 Mo**

![](docs/screens/07-tailles-images.png)

---

## 2 - Docker Swarm

### TD 1 : DinD et DooD

DinD fait tourner un daemon Docker complet dans un conteneur, DooD réutilise celui de l'hôte. Pour tester Swarm, chaque nœud est un conteneur DinD : chacun a son propre daemon, comme une machine séparée.

### Prérequis : images front et back sur un registry

![](docs/screens/08-images-ghcr.png)

### TD 2 : création du cluster

**1. `compose.yml` avec 1 manager et 3 nodes DinD, `docker ps` dans un conteneur**

![](docs/screens/09-dind-conteneurs.png)

**2. Initialiser le cluster sur le manager**

**3. Faire rejoindre les nodes**

**4. Lister les nœuds depuis le manager**

![](docs/screens/10-swarm-cluster.png)

### TD 3 : tests du cluster

**1. Stack `hello-world.compose.yml` avec 2 réplicas**

**2. Copier la stack dans `/home/manager` sur le manager**

![](docs/screens/11-stack-deploy.png)

**3. `docker service ls` et `docker ps` sur chaque nœud : qu'en déduisez-vous ?**

![](docs/screens/12-service-ls-ps.png)

Swarm répartit les conteneurs sur les nœuds. Chaque nœud ne voit que les siens, et seul le manager a la vue d'ensemble. `hello-world` s'arrête aussitôt, donc Swarm le relance en boucle.

**4. Faire varier `deploy` : manager seulement, puis global**

Fichiers `swarm/hello-world.manager.compose.yml` et `swarm/hello-world.global.compose.yml`.

---

## 4 - Traefik et Portainer

### Exercice 1 : déployer Traefik

**1. Relancer le cluster Swarm**

**2. Déployer la stack sur le manager, avec le réseau `web` en overlay**

![](docs/screens/15-traefik-stack.png)

**3. Fichier hosts : `traefik.swarm.localhost` et `whoami.swarm.localhost`**

![](docs/screens/14-hosts.png)

![](docs/screens/16-traefik-dashboard.png)

![](docs/screens/17-whoami.png)

### Exercice 2 : déployer une autre application

**1. Tester l'application avec Docker Compose**

![](docs/screens/18-vote-compose.png)

**2. La déployer sur Swarm**

![](docs/screens/19-vote-stack.png)

**3. Vote et résultats sur 2 URLs**

![](docs/screens/20-vote-url.png)

![](docs/screens/21-result-url.png)

### Exercice 3 : déployer Portainer

**1. Déployer Portainer sur Swarm**

![](docs/screens/23-portainer-stack.png)

**2. Publier Portainer sur une URL avec des labels**

**3. Mettre à jour le fichier hosts et retrouver ses stacks**

![](docs/screens/22-hosts-portainer.png)

![](docs/screens/24-portainer-environnement.png)

![](docs/screens/25-portainer-stacks.png)

### Exercice 4 : redéployer l'app de vote

**1. Supprimer la stack de vote**

```bash
docker exec swarm-manager-1 docker stack rm vote
```

**2. La republier depuis Portainer**

![](docs/screens/26-vote-portainer.png)

**3. Vérifier dans le navigateur**

![](docs/screens/27-vote-url.png)

![](docs/screens/28-result-url.png)

**4. Nouvelle stack pour l'API MFP, testée avec Bruno**

![](docs/screens/29-mfp-portainer.png)

![](docs/screens/31-mfp-bruno-me.png)

![](docs/screens/30-mfp-bruno-address.png)

---

## 5 - Secrets et configs

### Exercice 1 : déployer des logiciels

**1. Dozzle avec un utilisateur en secret (`users.yml`), sur `dozzle.swarm.localhost`**

![](docs/screens/32-hosts-dozzle-authelia.png)

![](docs/screens/33-portainer-secrets.png)

![](docs/screens/34-dozzle-stack.png)

![](docs/screens/35-dozzle-login-simple.png)

![](docs/screens/36-dozzle-simple-connecte.png)

**2. Authelia avec un compte en secret, et connexion à Dozzle en OIDC**

![](docs/screens/37-authelia-stack.png)

![](docs/screens/38-dozzle-login-oidc.png)

![](docs/screens/39-authelia-login.png)

![](docs/screens/40-dozzle-oidc-hotes.png)

![](docs/screens/41-dozzle-oidc-conteneurs.png)

**3. Bonus : Outline**

Non réalisé.
