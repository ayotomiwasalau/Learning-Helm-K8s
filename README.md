### Refreshing my K8s knowledge.
I wrote a 3 series guide last year on `Kubernetes`, so I will be taking my readings and examples from there.
Kubernetes cluster includes one master node and many worker machines called nodes.
#### MASTER NODE
The master node manages the worker nodes and the pods in the cluster.

The control plane’s (master node)components include the following

- `kube-apiserver` :  It is a cluster gateway that gets queries or updates from the clusters. It is used to create, delete, and update any object inside the cluster using the kubectl command.
- `etcd` : is a key-value store of the cluster state, eg when a pod dies, rescheduled, such information is recorded on etcd.
- `kube-scheduler` : Scheduler is responsible for physically scheduling pods across multiple nodes after kube-apiserver validates request from the cluster.
- `kube-controller-manager` : It is  responsible for the overall health of the entire cluster, such as no of nodes insides the cluster, up and running status. For E.g .when a pod dies, the Controller manager makes a request to the Scheduler to schedules those pods.

#### WORKER MACHINES (NODES)-
The component on a node includes the

- `Kubelet Service`: It is responsible for setting pod requirements, such as mounting volumes, starting containers, and reporting status.


- `The container runtime` is the software that is responsible for running containers. Kubernetes supports several container runtimes such as Docker, contained and CRI-O, etc
 
 - `Kube-proxy`:  It implements Kubernetes service concepts such as giving Pods their own IP addresses, a single DNS names for a set of Pods,  load-balancing across them.

1. Pods are the smallest deployable units of computing that you can create and manage in Kubernetes.
1.  Pod is a group of one or more containers, with shared storage/network resources, and a specification for how to run the containers.


#### KUBERNETES OBJECTS
Kubernetes Objects are abstractions that represent the state of the service that’s deployed in the cluster.

Kubernetes objects are persistent entities in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster. Specifically, they can describe:

- What containerized applications are running (and on which nodes)
- The resources available to those applications
- The policies around how those applications behave, such as restart policies, upgrades, and fault-tolerance

### K8s Objects
1. `ConfigMap`

This defines a set of key-value pairs that can be injected into pods, typically as environment variables or files. This allows us to share configurations between different defined pods, for example,  a ConfigMap can be used to dynamically set the value of an environment variable.

2. `Secret`

While ConfigMaps are great for most configuration data, sensitive data like passwords, security tokens, database info(username and password) are very sensitive.
Secrets enable container images to be created without bundling sensitive data. 

3. `Volumes`

The files that are inside a container are ephemeral (lasting for a very short time) and will be lost if the container stops its execution.

A volume is a form of persistent storage that can be used to maintain data information and share information between containers in a pod.

For E.g. If you have a pod that has a database, if your pod dies or get restarted, you could lose data, so volume helps you attach physical storage on  a hard drive, remote or cloud  storage, etc

`A PersistentVolume` defines a storage volume independent of the normal Pod-lifecycle. It’s managed outside of the particular Pod that it resides in.

`A PersistentVolumeClaim` is a request to use the PersistentVolume by a user.

4. `Deployment:`

This is a grouping of one or more identical pods and it helps us handle Stateless apps.

A deployment’s primary purpose is to declare how many replicas of a pod should be running at a time. When a deployment is added to the cluster, it will automatically spin up the requested number of pods, and then monitor if any of the pods die, the deployment will start another until the defined number is reached.

5. `Service`.

Service is an abstract way to expose an application running on a set of pods as a network service.

Since pods are created and recreated and have different IPs, to allow services to access them, a service needs to define the name that other elements can use to discover it.
In other words, it routes requests to the proper pods.

6. `Label`

Labels are key/value pairs that are attached to resources (like Pods) which are used to organize related resources. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users but do not directly imply semantics to the core system.

7. `Ingress:`

While service is internal, an Ingress is external. An ingress is an API object that manages external access to the services in a cluster, typically HTTP.

Ingress may provide load balancing, SSL termination, and name-based virtual hosting.

Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

8.`StatefulSets`

StatefulSet is the workload API object used to manage stateful applications like Database.

It manages the deployment and scaling of a set of Pods and provides guarantees about the ordering and uniqueness of these Pods.

Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

# Learning-Helm-K8s
Helm interacts directly with the Kubernetes API server.

#### Installing Helm on MacOS
```shell
brew install kubernetes-helm
```
Output :
```shell
==> Upgrading helm
  3.5.3 -> 3.7.1 

==> Pouring helm--3.7.1.catalina.bottle.tar.gz
==> Caveats
zsh completions have been installed to:
  /usr/local/share/zsh/site-functions
==> Summary
🍺  /usr/local/Cellar/helm/3.7.1: 60 files, 51.6MB
Removing: /usr/local/Cellar/helm/3.5.3... (57 files, 42.3MB)
```

> Note : 
> The `helm init` command has been removed. It performed two primary functions. First, it installed Tiller. This is no longer needed. Second, it setup 
> directories and repositories where Helm configuration lived. This is now automated. If the directory is not present it will be created.


### Getting Started with Helm 
It involves the following
1. Add a chart repository.
2. Find a chart to install.
3. Install a Helm chart.
4. See the list of what is installed.
5. Upgrade your installation.
6. Delete the installation


A `Helm chart` is an individual package that can be installed into your Kubernetes cluster. 
A `Helm chart repository `is simply a set of files, reachable over the network, that conforms to the Helm specification for indexing packages.
The easiest way to find the popular K8s repositories is to navigate to the [Artifact Hub](https://artifacthub.io/)

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install mysite bitnami/drupal
 ```

### --dry-run flag

When you supply this flag, it will cause Helm to step
through the first four phases 
- load the chart
- determine the values
- render the templates
- format to YAML
This dry-run feature provides Helm users a way to debug the output of a chart before it is sent on to Kubernetes.
The principal purpose of the `--dry-run` flag is to give people a chance to inspect and debug output before sending it on to Kubernetes

**Note**
>. A --dry-run on upgrade can produce different YAML output than a --dry-run on install, and this can be confusing
```shell
  helm install mysite bitnami/drupal  --set \
drupalEmail=foo@example.com --dry-run
```


Output:

```shell
 helm install mysite bitnami/drupal  --set \
drupalEmail=foo@example.com --dry-run
NAME: mysite
LAST DEPLOYED: Sun Oct 17 11:40:35 2021
NAMESPACE: default
STATUS: pending-install
REVISION: 1
TEST SUITE: None
HOOKS:
MANIFEST:
---
# Source: drupal/charts/mariadb/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
  annotations:
---
# Source: drupal/charts/mariadb/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  mariadb-root-password: "S0c3SWk4ZXVTdQ=="
  mariadb-password: "aWlGU1J1alpJZA=="
---
# Source: drupal/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  drupal-password: "cDRZTllIemdtQw=="
---
# Source: drupal/charts/mariadb/templates/primary/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
data:
  my.cnf: |-
    [mysqld]
    skip-name-resolve
    explicit_defaults_for_timestamp
    basedir=/opt/bitnami/mariadb
    plugin_dir=/opt/bitnami/mariadb/plugin
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    tmpdir=/opt/bitnami/mariadb/tmp
    max_allowed_packet=16M
    bind-address=0.0.0.0
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
    log-error=/opt/bitnami/mariadb/logs/mysqld.log
    character-set-server=UTF8
    collation-server=utf8_general_ci
    
    [client]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    default-character-set=UTF8
    plugin_dir=/opt/bitnami/mariadb/plugin
    
    [manager]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
---
# Source: drupal/templates/pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysite-drupal-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  accessModes:
    - "ReadWriteOnce"
  resources:
    requests:
      storage: "8Gi"
---
# Source: drupal/charts/mariadb/templates/primary/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
  annotations:
spec:
  type: ClusterIP
  ports:
    - name: mysql
      port: 3306
      protocol: TCP
      targetPort: mysql
      nodePort: null
  selector: 
    app.kubernetes.io/name: mariadb
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/component: primary
---
# Source: drupal/templates/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  type: LoadBalancer
  sessionAffinity: None
  externalTrafficPolicy: "Cluster"
  ports:
    - name: http
      port: 80
      targetPort: http
    - name: https
      port: 443
      targetPort: https
  selector:
    app.kubernetes.io/name: drupal
    app.kubernetes.io/instance: mysite
---
# Source: drupal/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: drupal
      app.kubernetes.io/instance: mysite
  strategy:
    type: RollingUpdate
  replicas: 1
  template:
    metadata:
      labels:
        app.kubernetes.io/name: drupal
        helm.sh/chart: drupal-10.3.4
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
      annotations:
        prometheus.io/port: "9117"
        prometheus.io/scrape: "true"
    spec:
      
      securityContext:
        fsGroup: 1001
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: drupal
                    app.kubernetes.io/instance: mysite
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      # yamllint disable rule:indentation
      hostAliases:
        - hostnames:
          - status.localhost
          ip: 127.0.0.1
      # yamllint enable rule:indentation
      initContainers:
      containers:
        - name: mysite-drupal
          image: docker.io/bitnami/drupal:9.2.7-debian-10-r1
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            - name: APACHE_HTTP_PORT_NUMBER
              value: "8080"
            - name: APACHE_HTTPS_PORT_NUMBER
              value: "8443"
            - name: DRUPAL_DATABASE_HOST
              value: "mysite-mariadb"
            - name: DRUPAL_DATABASE_PORT_NUMBER
              value: "3306"
            - name: DRUPAL_DATABASE_NAME
              value: "bitnami_drupal"
            - name: DRUPAL_DATABASE_USER
              value: "bn_drupal"
            - name: DRUPAL_DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: "mariadb-password"
            - name: DRUPAL_SKIP_BOOTSTRAP
              value: "no"
            - name: DRUPAL_PROFILE
              value: "standard"
            - name: DRUPAL_USERNAME
              value: "user"
            - name: DRUPAL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-drupal
                  key: drupal-password
            - name: DRUPAL_EMAIL
              value: "foo@example.com"
          envFrom:
          ports:
            - name: http
              containerPort: 8080
            - name: https
              containerPort: 8443
          livenessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 600
            periodSeconds: 10
            timeoutSeconds: 5
            successThreshold: 1
            failureThreshold: 5
          readinessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 30
            periodSeconds: 5
            timeoutSeconds: 1
            successThreshold: 1
            failureThreshold: 5
          resources:
            requests:
              cpu: 300m
              memory: 512Mi
          volumeMounts:
            - name: drupal-data
              mountPath: /bitnami/drupal
      volumes:
        - name: drupal-data
          persistentVolumeClaim:
            claimName: mysite-drupal-drupal
---
# Source: drupal/charts/mariadb/templates/primary/statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels: 
      app.kubernetes.io/name: mariadb
      app.kubernetes.io/instance: mysite
      app.kubernetes.io/component: primary
  serviceName: mysite-mariadb
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      annotations:
        checksum/configuration: fe8acb0217a8bcbcd59649983c9c50f65d7a281ccee92fc8c584e7454f6c27ee
      labels:
        app.kubernetes.io/name: mariadb
        helm.sh/chart: mariadb-9.6.2
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/component: primary
    spec:
      
      serviceAccountName: mysite-mariadb
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: mariadb
                    app.kubernetes.io/instance: mysite
                    app.kubernetes.io/component: primary
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      securityContext:
        fsGroup: 1001
      containers:
        - name: mariadb
          image: docker.io/bitnami/mariadb:10.5.12-debian-10-r60
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: MARIADB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-root-password
            - name: MARIADB_USER
              value: "bn_drupal"
            - name: MARIADB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-password
            - name: MARIADB_DATABASE
              value: "bitnami_drupal"
          ports:
            - name: mysql
              containerPort: 3306
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 120
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MARIADB_ROOT_PASSWORD:-}"
                  if [[ -f "${MARIADB_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MARIADB_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          readinessProbe:
            failureThreshold: 3
            initialDelaySeconds: 30
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MARIADB_ROOT_PASSWORD:-}"
                  if [[ -f "${MARIADB_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MARIADB_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          resources: 
            limits: {}
            requests: {}
          volumeMounts:
            - name: data
              mountPath: /bitnami/mariadb
            - name: config
              mountPath: /opt/bitnami/mariadb/conf/my.cnf
              subPath: my.cnf
      volumes:
        - name: config
          configMap:
            name: mysite-mariadb
  volumeClaimTemplates:
    - metadata:
        name: data
        labels: 
          app.kubernetes.io/name: mariadb
          app.kubernetes.io/instance: mysite
          app.kubernetes.io/component: primary
      spec:
        accessModes:
          - "ReadWriteOnce"
        resources:
          requests:
            storage: "8Gi"

NOTES:
*******************************************************************
*** PLEASE BE PATIENT: Drupal may take a few minutes to install ***
*******************************************************************

1. Get the Drupal URL:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w mysite-drupal'

  export SERVICE_IP=$(kubectl get svc --namespace default mysite-drupal --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "Drupal URL: http://$SERVICE_IP/"

2. Get your Drupal login credentials by running:

  echo Username: user
  echo Password: $(kubectl get secret --namespace default mysite-drupal -o jsonpath="{.data.drupal-password}" | base64 --decode)

```

### Helm template
While `--dry-run` is designed for debugging, helm template is designed to isolate the template rendering process of Helm from the installation or upgrade logic

##### Helm template features
1. Helm never contacts a remote Kubernetes server.
2. The template command always acts like an installation.Template functions and directives that would normally require
contacting a Kubernetes server will instead only return default data.
3. The chart only has access to default Kubernetes kinds.

Helm does not have access to any CRDs during a helm template run, since CRDs are installed on the cluster and are not
included in the Kubernetes libraries.
Because Helm does not contact a Kubernetes cluster during a helm
template run, it does not do complete validation of the output. It is
possible that Helm will not catch some errors in this case. You may choose
to use the `--validate` flag if you want that behavior, but in this case
Helm will need a valid` kubeconfig file` with credentials for a cluster.

```shell
helm template mysite bitnami/drupal  --set drupalEmail=foo@example.com
```
Output :
```shell
---
# Source: drupal/charts/mariadb/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
  annotations:
---
# Source: drupal/charts/mariadb/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  mariadb-root-password: "b1Y5Vk1OWVJZcA=="
  mariadb-password: "R1ZMY2RwSXRGSQ=="
---
# Source: drupal/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  drupal-password: "SUt6cWozbEU4Zg=="
---
# Source: drupal/charts/mariadb/templates/primary/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
data:
  my.cnf: |-
    [mysqld]
    skip-name-resolve
    explicit_defaults_for_timestamp
    basedir=/opt/bitnami/mariadb
    plugin_dir=/opt/bitnami/mariadb/plugin
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    tmpdir=/opt/bitnami/mariadb/tmp
    max_allowed_packet=16M
    bind-address=0.0.0.0
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
    log-error=/opt/bitnami/mariadb/logs/mysqld.log
    character-set-server=UTF8
    collation-server=utf8_general_ci
    
    [client]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    default-character-set=UTF8
    plugin_dir=/opt/bitnami/mariadb/plugin
    
    [manager]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
---
# Source: drupal/templates/pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysite-drupal-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  accessModes:
    - "ReadWriteOnce"
  resources:
    requests:
      storage: "8Gi"
---
# Source: drupal/charts/mariadb/templates/primary/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
  annotations:
spec:
  type: ClusterIP
  ports:
    - name: mysql
      port: 3306
      protocol: TCP
      targetPort: mysql
      nodePort: null
  selector: 
    app.kubernetes.io/name: mariadb
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/component: primary
---
# Source: drupal/templates/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  type: LoadBalancer
  sessionAffinity: None
  externalTrafficPolicy: "Cluster"
  ports:
    - name: http
      port: 80
      targetPort: http
    - name: https
      port: 443
      targetPort: https
  selector:
    app.kubernetes.io/name: drupal
    app.kubernetes.io/instance: mysite
---
# Source: drupal/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: drupal
      app.kubernetes.io/instance: mysite
  strategy:
    type: RollingUpdate
  replicas: 1
  template:
    metadata:
      labels:
        app.kubernetes.io/name: drupal
        helm.sh/chart: drupal-10.3.4
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
      annotations:
        prometheus.io/port: "9117"
        prometheus.io/scrape: "true"
    spec:
      
      securityContext:
        fsGroup: 1001
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: drupal
                    app.kubernetes.io/instance: mysite
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      # yamllint disable rule:indentation
      hostAliases:
        - hostnames:
          - status.localhost
          ip: 127.0.0.1
      # yamllint enable rule:indentation
      initContainers:
      containers:
        - name: mysite-drupal
          image: docker.io/bitnami/drupal:9.2.7-debian-10-r1
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            - name: APACHE_HTTP_PORT_NUMBER
              value: "8080"
            - name: APACHE_HTTPS_PORT_NUMBER
              value: "8443"
            - name: DRUPAL_DATABASE_HOST
              value: "mysite-mariadb"
            - name: DRUPAL_DATABASE_PORT_NUMBER
              value: "3306"
            - name: DRUPAL_DATABASE_NAME
              value: "bitnami_drupal"
            - name: DRUPAL_DATABASE_USER
              value: "bn_drupal"
            - name: DRUPAL_DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: "mariadb-password"
            - name: DRUPAL_SKIP_BOOTSTRAP
              value: "no"
            - name: DRUPAL_PROFILE
              value: "standard"
            - name: DRUPAL_USERNAME
              value: "user"
            - name: DRUPAL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-drupal
                  key: drupal-password
            - name: DRUPAL_EMAIL
              value: "foo@example.com"
          envFrom:
          ports:
            - name: http
              containerPort: 8080
            - name: https
              containerPort: 8443
          livenessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 600
            periodSeconds: 10
            timeoutSeconds: 5
            successThreshold: 1
            failureThreshold: 5
          readinessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 30
            periodSeconds: 5
            timeoutSeconds: 1
            successThreshold: 1
            failureThreshold: 5
          resources:
            requests:
              cpu: 300m
              memory: 512Mi
          volumeMounts:
            - name: drupal-data
              mountPath: /bitnami/drupal
      volumes:
        - name: drupal-data
          persistentVolumeClaim:
            claimName: mysite-drupal-drupal
---
# Source: drupal/charts/mariadb/templates/primary/statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels: 
      app.kubernetes.io/name: mariadb
      app.kubernetes.io/instance: mysite
      app.kubernetes.io/component: primary
  serviceName: mysite-mariadb
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      annotations:
        checksum/configuration: fe8acb0217a8bcbcd59649983c9c50f65d7a281ccee92fc8c584e7454f6c27ee
      labels:
        app.kubernetes.io/name: mariadb
        helm.sh/chart: mariadb-9.6.2
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/component: primary
    spec:
      
      serviceAccountName: mysite-mariadb
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: mariadb
                    app.kubernetes.io/instance: mysite
                    app.kubernetes.io/component: primary
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      securityContext:
        fsGroup: 1001
      containers:
        - name: mariadb
          image: docker.io/bitnami/mariadb:10.5.12-debian-10-r60
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: MARIADB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-root-password
            - name: MARIADB_USER
              value: "bn_drupal"
            - name: MARIADB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-password
            - name: MARIADB_DATABASE
              value: "bitnami_drupal"
          ports:
            - name: mysql
              containerPort: 3306
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 120
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MARIADB_ROOT_PASSWORD:-}"
                  if [[ -f "${MARIADB_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MARIADB_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          readinessProbe:
oluchi@apples-Air-5 Learning-Helm-K8s % helm template mysite bitnami/drupal  --set drupalEmail=foo@example.com
---
# Source: drupal/charts/mariadb/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
  annotations:
---
# Source: drupal/charts/mariadb/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  mariadb-root-password: "STg4djkySmc5Nw=="
  mariadb-password: "SmVIR3dMUXU5UQ=="
---
# Source: drupal/templates/secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
type: Opaque
data:
  drupal-password: "eDR1eWxLTjJxYg=="
---
# Source: drupal/charts/mariadb/templates/primary/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
data:
  my.cnf: |-
    [mysqld]
    skip-name-resolve
    explicit_defaults_for_timestamp
    basedir=/opt/bitnami/mariadb
    plugin_dir=/opt/bitnami/mariadb/plugin
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    tmpdir=/opt/bitnami/mariadb/tmp
    max_allowed_packet=16M
    bind-address=0.0.0.0
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
    log-error=/opt/bitnami/mariadb/logs/mysqld.log
    character-set-server=UTF8
    collation-server=utf8_general_ci
    
    [client]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    default-character-set=UTF8
    plugin_dir=/opt/bitnami/mariadb/plugin
    
    [manager]
    port=3306
    socket=/opt/bitnami/mariadb/tmp/mysql.sock
    pid-file=/opt/bitnami/mariadb/tmp/mysqld.pid
---
# Source: drupal/templates/pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysite-drupal-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  accessModes:
    - "ReadWriteOnce"
  resources:
    requests:
      storage: "8Gi"
---
# Source: drupal/charts/mariadb/templates/primary/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
  annotations:
spec:
  type: ClusterIP
  ports:
    - name: mysql
      port: 3306
      protocol: TCP
      targetPort: mysql
      nodePort: null
  selector: 
    app.kubernetes.io/name: mariadb
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/component: primary
---
# Source: drupal/templates/svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  type: LoadBalancer
  sessionAffinity: None
  externalTrafficPolicy: "Cluster"
  ports:
    - name: http
      port: 80
      targetPort: http
    - name: https
      port: 443
      targetPort: https
  selector:
    app.kubernetes.io/name: drupal
    app.kubernetes.io/instance: mysite
---
# Source: drupal/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysite-drupal
  labels:
    app.kubernetes.io/name: drupal
    helm.sh/chart: drupal-10.3.4
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: drupal
      app.kubernetes.io/instance: mysite
  strategy:
    type: RollingUpdate
  replicas: 1
  template:
    metadata:
      labels:
        app.kubernetes.io/name: drupal
        helm.sh/chart: drupal-10.3.4
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
      annotations:
        prometheus.io/port: "9117"
        prometheus.io/scrape: "true"
    spec:
      
      securityContext:
        fsGroup: 1001
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: drupal
                    app.kubernetes.io/instance: mysite
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      # yamllint disable rule:indentation
      hostAliases:
        - hostnames:
          - status.localhost
          ip: 127.0.0.1
      # yamllint enable rule:indentation
      initContainers:
      containers:
        - name: mysite-drupal
          image: docker.io/bitnami/drupal:9.2.7-debian-10-r1
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            - name: APACHE_HTTP_PORT_NUMBER
              value: "8080"
            - name: APACHE_HTTPS_PORT_NUMBER
              value: "8443"
            - name: DRUPAL_DATABASE_HOST
              value: "mysite-mariadb"
            - name: DRUPAL_DATABASE_PORT_NUMBER
              value: "3306"
            - name: DRUPAL_DATABASE_NAME
              value: "bitnami_drupal"
            - name: DRUPAL_DATABASE_USER
              value: "bn_drupal"
            - name: DRUPAL_DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: "mariadb-password"
            - name: DRUPAL_SKIP_BOOTSTRAP
              value: "no"
            - name: DRUPAL_PROFILE
              value: "standard"
            - name: DRUPAL_USERNAME
              value: "user"
            - name: DRUPAL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-drupal
                  key: drupal-password
            - name: DRUPAL_EMAIL
              value: "foo@example.com"
          envFrom:
          ports:
            - name: http
              containerPort: 8080
            - name: https
              containerPort: 8443
          livenessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 600
            periodSeconds: 10
            timeoutSeconds: 5
            successThreshold: 1
            failureThreshold: 5
          readinessProbe:
            httpGet:
              path: /user/login
              port: http
            initialDelaySeconds: 30
            periodSeconds: 5
            timeoutSeconds: 1
            successThreshold: 1
            failureThreshold: 5
          resources:
            requests:
              cpu: 300m
              memory: 512Mi
          volumeMounts:
            - name: drupal-data
              mountPath: /bitnami/drupal
      volumes:
        - name: drupal-data
          persistentVolumeClaim:
            claimName: mysite-drupal-drupal
---
# Source: drupal/charts/mariadb/templates/primary/statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysite-mariadb
  namespace: default
  labels:
    app.kubernetes.io/name: mariadb
    helm.sh/chart: mariadb-9.6.2
    app.kubernetes.io/instance: mysite
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: primary
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels: 
      app.kubernetes.io/name: mariadb
      app.kubernetes.io/instance: mysite
      app.kubernetes.io/component: primary
  serviceName: mysite-mariadb
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      annotations:
        checksum/configuration: fe8acb0217a8bcbcd59649983c9c50f65d7a281ccee92fc8c584e7454f6c27ee
      labels:
        app.kubernetes.io/name: mariadb
        helm.sh/chart: mariadb-9.6.2
        app.kubernetes.io/instance: mysite
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/component: primary
    spec:
      
      serviceAccountName: mysite-mariadb
      affinity:
        podAffinity:
          
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - podAffinityTerm:
                labelSelector:
                  matchLabels:
                    app.kubernetes.io/name: mariadb
                    app.kubernetes.io/instance: mysite
                    app.kubernetes.io/component: primary
                namespaces:
                  - "default"
                topologyKey: kubernetes.io/hostname
              weight: 1
        nodeAffinity:
          
      securityContext:
        fsGroup: 1001
      containers:
        - name: mariadb
          image: docker.io/bitnami/mariadb:10.5.12-debian-10-r60
          imagePullPolicy: "IfNotPresent"
          securityContext:
            runAsUser: 1001
          env:
            - name: BITNAMI_DEBUG
              value: "false"
            - name: MARIADB_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-root-password
            - name: MARIADB_USER
              value: "bn_drupal"
            - name: MARIADB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysite-mariadb
                  key: mariadb-password
            - name: MARIADB_DATABASE
              value: "bitnami_drupal"
          ports:
            - name: mysql
              containerPort: 3306
          livenessProbe:
            failureThreshold: 3
            initialDelaySeconds: 120
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MARIADB_ROOT_PASSWORD:-}"
                  if [[ -f "${MARIADB_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MARIADB_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          readinessProbe:
            failureThreshold: 3
            initialDelaySeconds: 30
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            exec:
              command:
                - /bin/bash
                - -ec
                - |
                  password_aux="${MARIADB_ROOT_PASSWORD:-}"
                  if [[ -f "${MARIADB_ROOT_PASSWORD_FILE:-}" ]]; then
                      password_aux=$(cat "$MARIADB_ROOT_PASSWORD_FILE")
                  fi
                  mysqladmin status -uroot -p"${password_aux}"
          resources: 
            limits: {}
            requests: {}
          volumeMounts:
            - name: data
              mountPath: /bitnami/mariadb
            - name: config
              mountPath: /opt/bitnami/mariadb/conf/my.cnf
              subPath: my.cnf
      volumes:
        - name: config
          configMap:
            name: mysite-mariadb
  volumeClaimTemplates:
    - metadata:
        name: data
        labels: 
          app.kubernetes.io/name: mariadb
          app.kubernetes.io/instance: mysite
          app.kubernetes.io/component: primary
      spec:
        accessModes:
          - "ReadWriteOnce"
        resources:
          requests:
            storage: "8Gi"
```


To summarize, helm template is a tool for rendering Helm charts into
YAML, and the --dry-run flag is a tool for debugging installation and
upgrade commands without loading the data into Kubernetes.


### Release Records
```shell
kubectl get secret
```
Output :
```shell
NAME                                     TYPE                                  DATA   AGE
alertmanager-token-fqf2z                 kubernetes.io/service-account-token   3      172d
default-token-7tzdd                      kubernetes.io/service-account-token   3      172d
flask-app-grafana                        Opaque                                2      172d
grafana-token-p6s5k                      kubernetes.io/service-account-token   3      172d
kube-state-metrics-token-94lxj           kubernetes.io/service-account-token   3      172d
mysql-1634466937                         Opaque                                2      18m
mysql-1634466937-token-fxghk             kubernetes.io/service-account-token   3      18m
node-exporter-token-kcln7                kubernetes.io/service-account-token   3      172d
prometheus-token-6cp6p                   kubernetes.io/service-account-token   3      172d
sh.helm.release.v1.mysql-1634466937.v1   helm.sh/release.v1                    1      18m

```

Notice that it uses a special type
(`sh.helm.release.v1.mysql-1634466937.v1 `) to indicate that it is a Helm secret. Helm
automatically generated this secret to track version 1 of our mysite installation (which is a Drupal site)
Each time we upgrade that mysite installation, a new Secret will be created to track each release. In other words, a release record tracks each revision of an installation


```shell
helm ls
```
Output :
```shell
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART              APP VERSION
mysite                  default         1               2021-10-17 11:58:59.850371 +0100 WAT    deployed        drupal-10.3.4      9.2.7      
mysql-1634466937        default         1               2021-10-17 11:35:41.524689 +0100 WAT    deployed        mysql-8.8.8        8.0.26  
```

### Find details of release with helm notes
``` shell
helm get notes mysite
```
### Using helm get values
```shell
helm get values mysite 
helm get values mysite --all
 ```

### Helm get manifest
One important detail about this command is that it does not return the
current state of all of your resources. It returns the manifest generated from
the template.
```shell
helm get manifest mysite
```

#### History and Rollbacks
```shell
helm history mysite
helm rollback mysite <version-number-you-wish>
```
