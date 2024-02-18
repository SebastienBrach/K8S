# K8S

Cours effectué avec l'aide de ChatGPT (uniquement)

## Étape 1 : Comprendre les concepts fondamentaux de Kubernetes

### Introduction à Kubernetes

Kubernetes est un système open-source permettant de déployer, de mettre à l'échelle et de gérer des applications conteneurisées. Il automatisera le déploiement, la mise à l'échelle et la gestion des applications conteneurisées, ce qui facilitera considérablement votre travail en tant que développeur web.

### Concepts clés à comprendre

- **Cluster Kubernetes :** Un ensemble de machines (physiques ou virtuelles) qui exécutent Kubernetes et qui sont utilisées pour exécuter les applications conteneurisées.
- **Pods :** La plus petite unité déployable dans Kubernetes, qui contient un ou plusieurs conteneurs.
- **Services :** Un moyen d'exposer une application fonctionnant sur un ensemble de pods sous forme d'un service réseau stable.
- **Deployments :** Une ressource Kubernetes qui gère un ensemble de pods en garantissant que le nombre souhaité d'instances du pod est toujours en cours d'exécution.
- **Kubectl :** L'outil en ligne de commande utilisé pour interagir avec les clusters Kubernetes.

### Installation de Kubernetes (docker desktop)

- Ouvrez **Docker Desktop** sur votre machine macOS.
- Dans la barre de menus, cliquez sur l'icône Docker Desktop (généralement située en haut à droite de l'écran).
- Sélectionnez **"Préférences"** dans le menu déroulant.
- Dans la fenêtre des préférences, cliquez sur l'onglet **"Kubernetes"**.
- Cochez la case **"Activer Kubernetes"** pour démarrer l'installation.

Une fois l'installation terminée, vous verrez une icône de statut dans la barre de menus de Docker Desktop, indiquant que Kubernetes est prêt à être utilisé. Assurez-vous que l'icône affiche "Kubernetes est en cours d'exécution". 2. Vous pouvez également vérifier l'état de Kubernetes en utilisant la ligne de commande. Ouvrez un terminal et exécutez la commande suivante :

```
kubectl version --client
```

## Étape 2 : Déployer des applications sur Kubernetes

Dans cette étape, nous allons apprendre à déployer une application sur votre cluster Kubernetes local. Nous commencerons par créer un simple déploiement pour exécuter une application conteneurisée. Ensuite, nous explorerons la création d'un service pour exposer cette application et permettre son accès depuis l'extérieur.

### 1. Créer un déploiement Kubernetes :

Pour commencer, nous allons créer un déploiement pour exécuter une application conteneurisée. Nous utiliserons kubectl pour définir et créer ce déploiement.

1. Créez un fichier YAML nommé `nginx-deployment.yaml` avec le contenu suivant :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

2. Appliquez ce fichier YAML en utilisant la commande `kubectl apply -f nginx-deployment.yaml`.
3. Vérifiez que le déploiement a été créé en utilisant la commande `kubectl get deployments`.
4. Vérifiez que les pods associés au déploiement sont en cours d'exécution avec la commande `kubectl get pods`.

Explication :
Un déploiement dans Kubernetes est une ressource qui gère le déploiement et la mise à l'échelle d'applications conteneurisées. Il définit l'état désiré de l'application et assure que le nombre spécifié de répliques (ou instances) de l'application est toujours en cours d'exécution.

Dans notre exercice, nous avons créé un déploiement pour l'application NGINX. Voici une explication de chaque partie du fichier YAML `nginx-deployment.yaml` que nous avons utilisé :

- `apiVersion` : La version de l'API Kubernetes utilisée pour définir ce déploiement.
- `kind` : Le type de ressource, qui est un déploiement dans ce cas.
- `metadata` : Les métadonnées associées au déploiement, comme son nom.
- `spec` : La spécification du déploiement, qui comprend :
  - `replicas` : Le nombre de répliques de l'application que nous voulons exécuter (dans notre cas, 3 répliques).
  - `selector` : Les labels utilisés pour identifier les pods que ce déploiement gère.
  - `template` : Le modèle utilisé pour créer les pods gérés par ce déploiement, qui comprend :
    - `metadata` : Les labels associés aux pods.
    - `spec` : La spécification du conteneur à exécuter dans les pods, y compris l'image Docker à utiliser et les ports exposés.

#### 2. Créer un service Kubernetes :

Maintenant que notre déploiement est opérationnel, nous allons créer un service pour exposer notre application et permettre son accès depuis l'extérieur.

1. Créez un fichier YAML nommé `nginx-service.yaml` avec le contenu suivant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

2. Appliquez ce fichier YAML en utilisant la commande `kubectl apply -f nginx-service.yaml`.
3. Vérifiez que le service a été créé en utilisant la commande `kubectl get services`.
   `nginx-service   NodePort    10.102.38.144   <none>        80:32548/TCP   20s`
   Le service est accessible via le port 32548 sur l'adresse IP de votre machine hôte.
4. Notez le port NodePort attribué au service pour accéder à l'application.

Un service dans Kubernetes est une ressource qui définit un ensemble de pods et une politique d'accès à ces pods. Il fournit une adresse IP et un nom DNS stable pour accéder aux pods du déploiement, ainsi qu'une fonctionnalité de load balancing si plusieurs pods sont disponibles.

Dans notre exercice, nous avons créé un service pour exposer notre déploiement NGINX à l'extérieur du cluster Kubernetes. Voici une explication de chaque partie du fichier YAML `nginx-service.yaml` :

- `apiVersion` : La version de l'API Kubernetes utilisée pour définir ce service.
- `kind` : Le type de ressource, qui est un service dans ce cas.
- `metadata` : Les métadonnées associées au service, comme son nom.
- `spec` : La spécification du service, qui comprend :
  - `selector` : Les labels utilisés pour sélectionner les pods auxquels le service fait référence.
  - `ports` : Les ports exposés par le service, avec le port cible des pods et le port exposé par le service lui-même.
  - `type` : Le type de service. Dans notre cas, nous avons utilisé `NodePort`, ce qui expose le service sur un port spécifique de chaque nœud dans le cluster.

En résumé, un déploiement gère le déploiement et la mise à l'échelle des applications, tandis qu'un service permet d'accéder à ces applications en fournissant une adresse IP et une politique d'accès stable. Le service permet également de faire du load balancing si nécessaire.

### Étape 3 : Gestion avancée de Kubernetes

**AutoScaling** :
Le scaling automatique dans Kubernetes permet d'ajuster dynamiquement le nombre de répliques de vos applications en fonction de la charge de travail. Cela garantit que vos applications ont suffisamment de ressources pour gérer le trafic entrant, tout en optimisant l'utilisation des ressources disponibles.

1.  Créez un fichier YAML nommé `nginx-autoscale.yaml` avec le contenu suivant :

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-autoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```

2. Appliquez ce fichier YAML en utilisant la commande `kubectl apply -f nginx-autoscale.yaml`.
3. Vérifiez que l'autoscaler a été créé en utilisant la commande `kubectl get hpa`.

Dans ce fichier YAML, nous avons configuré un HorizontalPodAutoscaler (HPA) qui surveille la charge CPU des pods de notre déploiement NGINX et ajuste dynamiquement le nombre de répliques pour maintenir la charge CPU en dessous de 50%. Voici ce que chaque partie du fichier YAML signifie :

- `apiVersion` : La version de l'API Kubernetes utilisée pour définir l'HPA.
- `kind` : Le type de ressource, qui est un HorizontalPodAutoscaler dans ce cas.
- `metadata` : Les métadonnées associées à l'HPA, comme son nom.
- `spec` : La spécification de l'HPA, qui comprend :
  - `scaleTargetRef` : La référence au déploiement que nous voulons scaler automatiquement.
  - `minReplicas` : Le nombre minimal de répliques de pods à maintenir (dans notre cas, 1).
  - `maxReplicas` : Le nombre maximal de répliques de pods à autoriser (dans notre cas, 10).
  - `targetCPUUtilizationPercentage` : Le niveau cible d'utilisation CPU pour chaque pod (dans notre cas, 50%).

Avec cette configuration, Kubernetes ajustera automatiquement le nombre de répliques de votre déploiement NGINX en fonction de la charge CPU, garantissant ainsi que vos applications ont suffisamment de ressources pour gérer le trafic entrant, tout en optimisant l'utilisation des ressources disponibles.

Vérification de l'Autoscaling :

1. Surveiller les événements de l'Autoscaler :
   Utilisez la commande `kubectl describe hpa nginx-autoscaler` pour afficher les détails de l'Autoscaler, y compris les événements récents. Cela vous permettra de voir si l'Autoscaler a effectué des ajustements en réponse à la charge de travail.

**Mise à Jour Rolling** :

La mise à jour rolling dans Kubernetes permet de mettre à jour vos applications sans temps d'arrêt en remplaçant progressivement les anciennes répliques par les nouvelles. Cela garantit que votre application reste disponible pendant le processus de mise à jour.

Voici comment vous pouvez effectuer une mise à jour rolling de votre déploiement NGINX :

1. Modifiez votre fichier YAML de déploiement (`nginx-deployment.yaml`) en mettant à jour la version de l'image Docker utilisée pour NGINX. Par exemple, si vous souhaitez mettre à jour vers la version `nginx:1.19`, modifiez la ligne `image: nginx:latest` en `image: nginx:1.19`.

2. Appliquez la mise à jour du déploiement en utilisant la commande `kubectl apply -f nginx-deployment.yaml`.

Kubernetes gérera automatiquement le processus de mise à jour rolling en remplaçant progressivement les anciennes répliques par les nouvelles, tout en garantissant que votre application reste disponible tout au long du processus.

La mise à jour rolling dans Kubernetes est une fonctionnalité native qui est indépendante de l'Autoscaler horizontal (HPA). Elle est gérée par le contrôleur de déploiement (Deployment Controller) de Kubernetes.

Lorsque vous effectuez une mise à jour du déploiement en modifiant la spécification du déploiement (par exemple, en mettant à jour la version de l'image Docker), Kubernetes déclenche automatiquement le processus de mise à jour rolling. Le contrôleur de déploiement compare la spécification actuelle du déploiement avec la nouvelle spécification et met en œuvre les modifications nécessaires pour atteindre l'état désiré.

Voici comment fonctionne le processus de mise à jour rolling :

1. Kubernetes crée de nouveaux pods avec la nouvelle spécification du déploiement (par exemple, la nouvelle version de l'image Docker).
2. Le contrôleur de déploiement augmente progressivement le nombre de nouveaux pods tout en réduisant le nombre de pods de l'ancienne spécification. Il s'assure que le nombre total de répliques reste constant pendant tout le processus.
3. Une fois que tous les nouveaux pods sont en cours d'exécution et que les anciens pods ont été supprimés, le processus de mise à jour rolling est considéré comme terminé.

Ce processus garantit que votre application reste disponible pendant toute la durée de la mise à jour, car Kubernetes maintient toujours un nombre suffisant de répliques en cours d'exécution pour gérer le trafic entrant.

L'Autoscaler horizontal (HPA) est utilisé pour ajuster automatiquement le nombre de répliques en fonction de la charge de travail, mais il n'intervient pas dans le processus de mise à jour rolling en lui-même. C'est le contrôleur de déploiement de Kubernetes qui gère ce processus de manière autonome.

2. **Configuration et Sécrets** :
   - Comprendre la gestion des configurations et des secrets dans Kubernetes.
   - Exercice : Utilisez les secrets Kubernetes pour stocker des informations sensibles comme les mots de passe ou les clés d'API.

----------------------------------------------------- À TRAITER ------------------------------------------

User
Donne moi le code pour létape sur les configurations
ChatGPT
Bien sûr ! Voici comment vous pouvez créer une configuration Kubernetes pour stocker des paramètres de configuration pour votre application NGINX :

### Configuration Kubernetes pour les Paramètres de Configuration :

1. Créez un fichier YAML nommé `nginx-configmap.yaml` avec le contenu suivant :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  nginx.conf: |
    user nginx;
    worker_processes auto;
    error_log /var/log/nginx/error.log;
    pid /var/run/nginx.pid;
    events {
        worker_connections 1024;
    }
    http {
        include /etc/nginx/mime.types;
        default_type application/octet-stream;
        log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';
        access_log /var/log/nginx/access.log main;
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
    }
```

2. Appliquez ce fichier YAML en utilisant la commande `kubectl apply -f nginx-configmap.yaml`.

Avec cette configuration, vous avez créé un ConfigMap nommé `nginx-config` qui contient un fichier `nginx.conf`. Vous pouvez ajouter d'autres paramètres de configuration au ConfigMap au besoin. Une fois que le ConfigMap est créé, vous pouvez le monter dans vos pods NGINX en tant que volume pour utiliser les paramètres de configuration dans votre application.

Si vous avez d'autres questions ou si vous avez besoin d'aide supplémentaire, n'hésitez pas à demander !
User
Explique moi comment le monter en tant que volume
ChatGPT
Pour monter le ConfigMap en tant que volume dans vos pods NGINX, vous devez ajouter une section `volume` dans la spécification de votre pod, puis référencer ce volume dans la spécification des conteneurs du pod. Voici comment vous pouvez le faire :

1. Modifiez votre fichier YAML de déploiement (`nginx-deployment.yaml`) pour inclure la définition d'un volume et la référence à ce volume dans la spécification des conteneurs :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nginx-config-volume
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
      volumes:
        - name: nginx-config-volume
          configMap:
            name: nginx-config
```

2. Appliquez cette nouvelle configuration du déploiement en utilisant la commande `kubectl apply -f nginx-deployment.yaml`.

Dans cette configuration :

- Nous avons défini un volume nommé `nginx-config-volume` qui utilise le ConfigMap `nginx-config`.
- Nous avons référencé ce volume dans la spécification du conteneur NGINX avec `volumeMounts`. Nous montons le fichier `nginx.conf` du ConfigMap dans le chemin `/etc/nginx/nginx.conf` dans le conteneur.

Avec cette configuration, Kubernetes montera automatiquement les données du ConfigMap en tant que fichier dans le conteneur NGINX. Ainsi, votre application NGINX utilisera les paramètres de configuration spécifiés dans le ConfigMap sans avoir besoin de les incorporer directement dans l'image du conteneur. Cela facilite la gestion des paramètres de configuration et permet de les modifier facilement sans avoir à redéployer l'application.
User
Trsè bien, passons aux secret maintenant, donne moi la solutions
ChatGPT
Bien sûr ! Voici comment vous pouvez créer un Secret Kubernetes pour stocker des informations sensibles, telles que les identifiants d'accès à une base de données, pour votre application NGINX :

### Création d'un Secret Kubernetes :

1. Créez un fichier YAML nommé `nginx-secret.yaml` avec le contenu suivant :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: nginx-secret
type: Opaque
data:
  username: <base64_encoded_username>
  password: <base64_encoded_password>
```

Assurez-vous de remplacer `<base64_encoded_username>` et `<base64_encoded_password>` par les valeurs encodées en base64 de votre nom d'utilisateur et de votre mot de passe respectivement. Vous pouvez encoder en base64 vos valeurs en utilisant des outils comme `echo -n 'your_username' | base64` pour le nom d'utilisateur et `echo -n 'your_password' | base64` pour le mot de passe.

2. Appliquez ce fichier YAML en utilisant la commande `kubectl apply -f nginx-secret.yaml`.

Avec cette configuration, vous avez créé un Secret Kubernetes nommé `nginx-secret` qui contient les identifiants d'accès à votre base de données. Ces informations sont encodées en base64 pour des raisons de sécurité, mais elles ne sont pas cryptées.

Vous pouvez maintenant référencer ce Secret dans vos pods NGINX pour accéder aux identifiants d'accès à la base de données de manière sécurisée.

Si vous avez d'autres questions ou si vous avez besoin d'aide supplémentaire, n'hésitez pas à demander !
User
Comment les référencés dans mes pods ?
ChatGPT
Pour référencer le Secret dans vos pods NGINX, vous devez également spécifier un volume dans la spécification de votre pod et monter ce volume dans le conteneur NGINX. Voici comment vous pouvez le faire :

1. Modifiez votre fichier YAML de déploiement (`nginx-deployment.yaml`) pour inclure la définition d'un volume et la référence à ce volume dans la spécification des conteneurs :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nginx-secret-volume
              mountPath: /etc/nginx/secret
      volumes:
        - name: nginx-secret-volume
          secret:
            secretName: nginx-secret
```

2. Appliquez cette nouvelle configuration du déploiement en utilisant la commande `kubectl apply -f nginx-deployment.yaml`.

Dans cette configuration :

- Nous avons défini un volume nommé `nginx-secret-volume` qui utilise le Secret `nginx-secret`.
- Nous avons référencé ce volume dans la spécification du conteneur NGINX avec `volumeMounts`. Nous montons les données du Secret dans le chemin `/etc/nginx/secret` dans le conteneur.

Avec cette configuration, Kubernetes montera automatiquement les données du Secret en tant que fichier dans le conteneur NGINX. Ainsi, votre application NGINX pourra accéder aux identifiants d'accès à la base de données de manière sécurisée sans avoir à incorporer directement ces informations dans l'image du conteneur.

Assurez-vous d'ajuster le chemin de montage (`mountPath`) selon les besoins de votre application pour que les fichiers du Secret soient accessibles depuis votre conteneur NGINX.

Si vous avez d'autres questions ou si vous avez besoin d'aide supplémentaire, n'hésitez pas à demander !
User
inclut le dans ce fichier:
apiVersion: apps/v1
kind: Deployment
metadata:
name: nginx-deployment
spec:
replicas: 1
selector:
matchLabels:
app: nginx
template:
metadata:
labels:
app: nginx
spec:
containers: - name: nginx
image: nginx:latest
ports: - containerPort: 80
volumeMounts: - name: nginx-config-volume
mountPath: /etc/nginx/nginx.conf
subPath: nginx.conf
volumes: - name: nginx-config-volume
configMap:
name: nginx-config
ChatGPT
Voici le fichier YAML modifié pour inclure également le volume et le montage du ConfigMap :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nginx-config-volume
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
            - name: nginx-secret-volume
              mountPath: /etc/nginx/secret
      volumes:
        - name: nginx-config-volume
          configMap:
            name: nginx-config
        - name: nginx-secret-volume
          secret:
            secretName: nginx-secret
```

Avec cette modification, le fichier YAML inclut à la fois la référence au volume et au montage du ConfigMap ainsi que la référence au volume et au montage du Secret. Cela permettra au pod NGINX d'accéder aux paramètres de configuration depuis le ConfigMap et aux identifiants d'accès à la base de données depuis le Secret.

Assurez-vous de remplacer `<base64_encoded_username>` et `<base64_encoded_password>` par les valeurs encodées en base64 de votre nom d'utilisateur et de votre mot de passe respectivement dans le fichier de secret. Et assurez-vous que le chemin de montage (`mountPath`) est correctement configuré selon les besoins de votre application.

N'hésitez pas à me poser d'autres questions si nécessaire !
User
Ah donc mes secrets sont également considéré comme des volumes ?
ChatGPT
Oui, exactement ! Dans Kubernetes, les secrets sont traités comme des volumes spéciaux. Lorsque vous référencez un secret dans la spécification d'un pod, Kubernetes crée un volume contenant les données du secret et monte ce volume dans le conteneur du pod. Cela permet à votre application d'accéder aux données sensibles du secret comme si elles étaient des fichiers normaux sur le système de fichiers du conteneur.

En incluant le secret dans la section `volumes` de la spécification du pod et en ajoutant un `volumeMount` pour le conteneur, vous pouvez spécifier où les données du secret doivent être montées dans le système de fichiers du conteneur.

C'est une pratique courante dans Kubernetes d'utiliser des secrets pour stocker des informations sensibles telles que les identifiants d'accès à la base de données, les clés d'API, les certificats SSL, etc. Le fait que Kubernetes traite les secrets comme des volumes facilite l'accès sécurisé à ces informations depuis vos applications.
