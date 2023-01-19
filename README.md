# Kubernetes Ingress

Antoine Zachariades

Roy Sarkis

### 1 Installer Kind et créer votre premier cluster
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

Creation du cluster
```bash=
cat <<EOF | kind create cluster --name ynov-cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```
![](https://i.imgur.com/qTFSTzU.png)
### 2 Installer le Nginx ingress Controller

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

### 3 Compléter le schéma suivant avec des objets Kubernetes

![](https://i.imgur.com/aOAlF6d.png)

### 4 Builder et publier (à partir de l’image nginx) sur le DockerHub, une image docker pour chacun des sites web présent sur le schéma précédent. Vous devez avoir 3 images (une par magasin tacos, pizzas et burgers)

Création des images : 

```bash
docker build -t nginx:pizza .
docker build -t nginx:tacos .
docker build -t nginx:burger .
```

Push des images vers docker hub
```bash
docker tag 103e27f8f172 imtoinou/ynov_tp:tacos
docker tag eaaa1854c05b imtoinou/ynov_tp:pizza
docker tag 17654a5edbc5 imtoinou/ynov_tp:burger

docker push imtoinou/ynov_tp:burger
docker push imtoinou/ynov_tp:pizza
docker push imtoinou/ynov_tp:tacos
```

### 5 Ecrire les fichiers yaml vous permettant de déployer sur votre cluster kind installé enlocal les composants décrits sur le schéma de la question 3 et les images crées à la question 4.


modification du fichier de conf /etc/hosts
```
127.0.0.1	mypizza.eatsout.com
127.0.0.1	burgerandtacos.eatsout.com
```

### 6 Votre magasin de tacos devient très populaire (il va avoir 3 fois plus de commandes). Il va vous falloir gérer une charge importante sur le Service de commande des tacos.Comment gérez-vous cela ? Comment vérifier que la charge est bien répartie (avec quelle commande kubectl ?) ?

Avec réplicaSet qui est à 3, nous avons dispaché les charges sur le service de commande des tacos. Nous pouvons vérifier que la charge est bien répartie avec la commance : `kubectl top` elle affiche les ressources utilisées par les différents noeuds et les différents pods dans le cluster.


