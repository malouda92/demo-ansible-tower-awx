# Installation du serveur AWX

### Kustomization.yaml
Appliquer le contenu du fichier **kustomization.yaml** dans le repertoire **_awx-op/_** avec Kustomize:
```
kubectl apply -k .
```
Cette commande va :

-Créer le namespace awx (puisqu’il est mentionné dans namespace dans kustomization.yaml)

-appliquer les manifests par défaut de l’opérateur pour la version 2.19.1

-créer les CRD nécessaires (CustomResourceDefinition)

-déployer l’opérateur

### Vérifier que l'opérateur fonctionne
Surveiller les pods dans le namespace awx:
```
kubectl get pods -n awx -w
```
On devrait voir un pod **awx-operator-controller-manager-...** passer en **Running**.

### awx-instance.yaml
Appliquer l'instance AWX en lançant la commande suivante:
```
kubectl apply -f awx-instance.yaml
```
Cette commande a besoin du fichier awx--instance.yaml qui se trouve dans le repertoire **_awx-op/_**

### Surveiller la création des composantes awx
```
kubectl get pods -n awx -w
```
On devrait voir:

-awx-postgres-0

-awx-redis-...

-awx-...-web-...

-awx-...-task-...

On regarde les services
```
kubectl get svc -n awx
```

### Récupérer l'url
````
minikube service awx-service -n awx --url
```

### Récupérer le mot de passe admin
```
kubectl get secret awx-admin-password -n awx -o jsonpath="{.data.password}" | base64 --decode
```

> [!NOTE]
> Le nom d'utilisateur par défaut est admin.


# Installation du serveur Linux
La création d'un serveur Linux (Ubuntu) se fait depuis un docker-compose, dans le répertoire **_serveur linux/_** 

## Lancement du conteneur
```
docker-compose up
```
### Préparation du conteneur
Par défaut, Ubuntu Docker ne contient pas de SSH, il faut donc l'installer. Il faut se connecter au conteneur en exécutant la ligne suivante:
```
docker exec -it demo-linux-server bash
```
À l'intérieur du conteneur, installer openssh-server:
```
apt update && apt install -y openssh-server python3 sudo curl
service ssh start
```
Créer un utilisateur pour ansible
```
useradd -m ansible
echo "ansible:ansible" | chpasswd
usermod -aG sudo ansible
```
Autoriser la connexion SSH:
```
mkdir -p /home/ansible/.ssh
chmod 700 /home/ansible/.ssh
```
### Tester la connexion au serveur en SSH, en dehors du serveur 
> [!NOTE]
> terminal local ou depuis un autre conteneur.

Exécuter la ligne de commande suivante:
```
ssh -i [chemin_vers__clé_privée] ansible@[adresse_ip] -p 2222
```
