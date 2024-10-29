# Kubernetes InitContainer Example

## Description

Ce projet démontre comment utiliser des `initContainers` dans Kubernetes pour gérer les tâches d'initialisation avant le démarrage des conteneurs principaux. Les `initContainers` permettent de préparer l'environnement de l'application avant son exécution, garantissant que les dépendances et les conditions préalables sont satisfaites.

## Objectif

L'objectif de cet exercice est de créer un pod avec :

- Un `initContainer` qui crée un fichier texte contenant un message.
- Un conteneur principal qui lit ce fichier et affiche son contenu.

## Prérequis

- Avoir un cluster Kubernetes en cours d'exécution (minikube, GKE, AKS, etc.).
- Avoir `kubectl` installé et configuré pour interagir avec ton cluster.

## Architecture

Le pod contient :

- **InitContainer** : Utilise l'image `busybox` ou `alpine` pour exécuter une commande qui crée un fichier `/tmp/init.txt` avec le contenu suivant : `THIS IS INIT CONTAINER`.
  
- **Conteneur Principal** : Utilise également l'image `busybox` ou `alpine` pour exécuter une commande qui lit le fichier `/tmp/init.txt` et affiche son contenu, puis se met en pause pendant 100 secondes.

## Fichier de Configuration

### pod.yaml

Voici le fichier de configuration Kubernetes `pod.yaml` :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-container-pod
spec:
  restartPolicy: Never  # Ne pas redémarrer automatiquement
  volumes:
    - name: shared-data
      emptyDir: {}  # Volume partagé entre les conteneurs
  initContainers:
    - name: init-container
      image: alpine  # Image pour l'initContainer
      command: ["sh", "-c", "echo 'THIS IS INIT CONTAINER' > /tmp/init.txt"]
      volumeMounts:
        - name: shared-data
          mountPath: /tmp  # Montage du volume dans le conteneur
  containers:
    - name: main-container
      image: alpine  # Image pour le conteneur principal
      command: ["sh", "-c", "echo 'THIS IS RUNNING APP'; cat /tmp/init.txt; sleep 100"]
      volumeMounts:
        - name: shared-data
          mountPath: /tmp  # Utilisation du même volume
