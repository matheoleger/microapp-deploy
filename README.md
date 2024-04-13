# Projet : Mise en place du CICD dans un environnement Kubernetes pour une application de microservices

## Contexte
Vous travaillez sur le déploiement d'une application composée de microservices dans un environnement Kubernetes. L'application est constituée de quatre services : le frontend React, le service de gestion des ordres, le service utilisateur interagissant avec une base de données MongoDB, et enfin, une instance MongoDB.

## Le projet

- Les microservices : [@matheoleger/cicd-project](https://github.com/matheoleger/cicd-project)
- Les fichiers K8S pour le déploiement : [@matheoleger/microapp-deploy](https://github.com/matheoleger/microapp-deploy)


> :bulb: Afin de savoir comment mettre en place le projet (ou avoir plus d'information), il faut regarder le README du repo `cicd-project`.


### À noter (plus de détails dans [@matheoleger/cicd-project](https://github.com/matheoleger/cicd-project))

#### Pour Kubernetes
- La base de donnée est bien un objet `Statefulset` avec un `PersistentVolume`
- Voici la configuration :
    - Chaques microservices possèdent un fichier `<service>-deployment.yaml` et un `<service>-service.yaml`
    - le service `frontend` est accessible en dehort du cluster via un `NodePort` et accessible au port `31000`.  
    > :bulb: Si vous utilisez `minikube`, vous pouvez récupérer son addresse avec `minikube service frontend-service --url`.
    - `order-service` et `user-service` ont des services de type `ClusterIP` et donc ne sont pas accessible en dehort du cluster.
    - Afin de permettre au `frontend` de faire des requêtes sur les 2 autres services via des requêtes clients, j'ai utilisé un **Ingress Controller** (voir l'autre repository pour la configuration à mettre en place pour le faire fonctionner en local).
    - La base de donnée à un objet service qui permet à la base de donnée d'être accessible seulement à l'intérieur du Cluster et permet donc à `user-service` de se connecter à la BDD.
#### Pour ArgoCD
- Notre application ArgoCD va écouter les changement sur le repo `microapp-deploy` et se synchroniser automatiquement selon les modifications.
#### Les différents Github Actions
- Pour ce repository :
    - Le Github Action sera déclencher lorsqu'il sera triggered par un autre workflow Github Action (donc celui de `cicd-project`)
    - Il va modifier les manifest de déploiements de chaque service afin de modifier le numéro de version des différentes et ainsi permettre de redéclencher une synchronisation pour ArgoCD qui va automatiquement modifier l'application avec les nouvelles images.