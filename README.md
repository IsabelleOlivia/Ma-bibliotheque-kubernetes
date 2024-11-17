

# Bibliothèque App

## Description

L'application Bibliothèque App est une application Flask permettant de gérer une bibliothèque (emprunt, retour de livres, etc.). Cette application est orchestrée avec Docker et déployée dans un cluster Kubernetes pour permettre une gestion scalable et résiliente. Elle se connecte à une base de données PostgreSQL pour gérer les données des livres et des utilisateurs.


### Explication des processus de déploiement :

1. **Base de données PostgreSQL** : 
   - Le déploiement de PostgreSQL utilise un seul pod avec les configurations et secrets pour la base de données.
   - La `NetworkPolicy` restreint l'accès aux autres services pour qu'uniquement les pods du backend puissent accéder à la base de données.

2. **Application Flask (backend)** : 
   - Le déploiement de l'application Flask est configuré avec un **init container** qui attend que la base de données PostgreSQL soit prête avant de démarrer l'application.
   - Une fois l'application lancée, elle se connecte à la base de données PostgreSQL en utilisant les informations de connexion contenues dans le `ConfigMap` et le `Secret`.

3. **Services Kubernetes** :
   - Le service `bibliotheque-backend` expose l'application Flask sur le port `30000` pour qu'elle soit accessible en dehors du cluster Kubernetes.
   - Le service `postgres` expose la base de données PostgreSQL uniquement aux autres pods du même réseau.

4. **Mise à l'échelle** :
   - L'application Flask est mise à l'échelle avec 3 réplicas pour garantir une haute disponibilité.
   - L'anti-affinité empêche que toutes les réplicas de l'application Flask soient placées sur le même nœud, ce qui contribue à améliorer la résilience du service.

---

### Déploiement dans Kubernetes

1. **Appliquez les fichiers de configuration** :
   Exécutez les commandes suivantes pour déployer les ressources Kubernetes :

   ```bash
   kubectl apply -f backend-deployment.yaml
   kubectl apply -f backend-service.yaml
   kubectl apply -f backend-configmap.yaml
   kubectl apply -f backend-secret.yaml
   kubectl apply -f database-deployment.yaml
   kubectl apply -f database-service.yaml
   kubectl apply -f network-policy.yaml
   ```

2. **Vérifiez le déploiement** :
   Après avoir appliqué les fichiers, vous pouvez vérifier l'état des ressources avec ces commandes :

   ```bash
   kubectl get pods
   kubectl get services
   kubectl get deployments
   kubectl get networkpolicies
   ```

3. **Accédez à l'application** :
   Vous pouvez accéder à l'application Flask en utilisant l'URL suivante (remplacez `<your-node-ip>` par l'adresse IP de votre nœud Kubernetes) :
   
   ```
   http://<your-node-ip>:30000
   ```

