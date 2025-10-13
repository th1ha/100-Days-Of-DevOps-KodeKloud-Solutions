# Deploy Iron Gallery App on Kubernetes

1. Create a namespace **iron-namespace-datacenter**

2. Create a deployment **iron-gallery-deployment-datacenter** for **iron gallery** under the same namespace you created.
  -  Labels **run** should be **iron-gallery**.
  - Replicas count should be **1**.
  - Selector's matchLabels **run** should be **iron-gallery**.
  - Template labels **run** should be **iron-gallery** under metadata.
  - The container should be named as **iron-gallery-container-datacenter**, use **kodekloud/irongallery:2.0** image ( use exact image name / tag ).
  - Resources limits for memory should be **100Mi** and for CPU should be **50m**.
  - First volumeMount name should be **config**, its mountPath should be **/usr/share/nginx/html/data**.
  - Second volumeMount name should be **images**, its mountPath should be **/usr/share/nginx/html/uploads**.
  - First volume name should be **config** and give it **emptyDir** and second volume name should be **images**, also give it **emptyDir**.

3. Create a deployment **iron-db-deployment-datacenter** for **iron db** under the same namespace.
  - Labels **db** should be **mariadb**.
  - Replicas count should be **1**.
  - Selector's matchLabels **db** should be **mariadb**.
  - Template labels **db** should be **mariadb** under metadata.
  - The container name should be **iron-db-container-datacenter**, use **kodekloud/irondb:2.0** image ( use exact image name / tag ).
  - Define environment, set **MYSQL_DATABASE** its value should be **database_blog**, set **MYSQL_ROOT_PASSWORD** and **MYSQL_PASSWORD** value should be with some complex passwords for DB connections, and **MYSQL_USER** value should be any custom user ( except root ).
  - Volume mount name should be **db** and its mountPath should be **/var/lib/mysql**. Volume name should be **db** and give it an **emptyDir**.

4. Create a service for **iron db** which should be named **iron-db-service-datacenter** under the same namespace. Configure spec as selector's db should be **mariadb**. Protocol should be **TCP**, port and targetPort should be **3306** and its type should be **ClusterIP**.

5. Create a service for **iron gallery** which should be named **iron-gallery-service-datacenter** under the same namespace. Configure spec as selector's run should be **iron-gallery**. Protocol should be **TCP**, port and targetPort should be **80**, nodePort should be **32678** and its type should be **NodePort**.


- [Doc - container resources example](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#example-1)
- [Doc - emptyDir example](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir-configuration-example)
- [Doc - env variable for a container](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#define-an-environment-variable-for-a-container)
---

<details>
<summary>steps</summary>

  #### kubectl create ns iron-namespace-datacenter
    namespace/iron-namespace-datacenter created

  #### kubectl config set-context --current --namespace iron-namespace-datacenter
    Context "kind-kodekloud" modified.

  #### kubectl config get-contexts 
    CURRENT   NAME             CLUSTER          AUTHINFO         NAMESPACE
    *         kind-kodekloud   kind-kodekloud   kind-kodekloud   iron-namespace-datacenter

  #### create deployment for app
  ```bash
  kubectl create deployment iron-gallery-deployment-datacenter --image kodekloud/irongallery:2.0 --replicas 1 --dry-run=client -oyaml > iron-deployment.yaml

  vi iron-deployment.yaml
  ```
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      run: iron-gallery
    name: iron-gallery-deployment-datacenter
  spec:
    replicas: 1
    selector:
      matchLabels:
        run: iron-gallery
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          run: iron-gallery
      spec:
        volumes:
          - name: config
            emptyDir: {}
          - name: images
            emptyDir: {}
        containers:
        - image: kodekloud/irongallery:2.0
          name: iron-gallery-container-datacenter
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "50Mi"
              cpu: "25m"
            limits:
              memory: "100Mi"
              cpu: "50m"
          volumeMounts:
            - name: config
              mountPath: /usr/share/nginx/html/data
            - name: images
              mountPath: /usr/share/nginx/html/uploads
  ```

  #### crreate deployment for db
  ```bash
  kubectl create deployment iron-db-deployment-datacenter --image kodekloud/irondb:2.0 --replicas 1 --dry-run=client -oyaml > iron-db.yaml
  
  vi iron-db.yaml
  ```
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    labels:
      db: mariadb
    name: iron-db-deployment-datacenter
  spec:
    replicas: 1
    selector:
      matchLabels:
        db: mariadb
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          db: mariadb
      spec:
        volumes:
          - name: db
            emptyDir: {}
        containers:
        - image: kodekloud/irondb:2.0
          name: iron-db-container-datacenter
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_DATABASE
              value: "database_blog"
            - name: MYSQL_ROOT_PASSWORD
              value: "ironrootpass123"
            - name: MYSQL_PASSWORD
              value: "ironpass123"
            - name: MYSQL_USER
              value: "iron"
          volumeMounts:
            - name: db
              mountPath: /var/lib/mysql
  ```

  #### kubectl apply -f iron-deployment.yaml -f iron-db.yaml 
    deployment.apps/iron-gallery-deployment-datacenter created
    deployment.apps/iron-db-deployment-datacenter created

  #### kubectl get all
    NAME                                                      READY   STATUS    RESTARTS   AGE
    pod/iron-db-deployment-datacenter-56cc4c75fd-j9c6v        1/1     Running   0          17s
    pod/iron-gallery-deployment-datacenter-7676766b7f-426fv   1/1     Running   0          17s

    NAME                                                 READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/iron-db-deployment-datacenter        1/1     1            1           17s
    deployment.apps/iron-gallery-deployment-datacenter   1/1     1            1           17s

    NAME                                                            DESIRED   CURRENT   READY   AGE
    replicaset.apps/iron-db-deployment-datacenter-56cc4c75fd        1         1         1       17s
    replicaset.apps/iron-gallery-deployment-datacenter-7676766b7f   1         1         1       17s

  #### kubectl exec iron-db-deployment-datacenter-56cc4c75fd-j9c6v -- env | grep MYSQL
    MYSQL_ROOT_PASSWORD=ironrootpass123
    MYSQL_DATABASE=database_blog
    MYSQL_USER=iron
    MYSQL_PASSWORD=ironpass123

  #### kubectl expose deployment iron-db-deployment-datacenter --name iron-db-service-datacenter --type ClusterIP --port 3306 --target-port 3306 --protocol TCP
    service/iron-db-service-datacenter exposed

  #### kubectl expose deployment iron-gallery-deployment-datacenter --name iron-gallery-service-datacenter --type NodePort --port 80 --target-port 80 --protocol TCP
    service/iron-gallery-service-datacenter exposed

  #### kubectl edit svc iron-gallery-service-datacenter
    ports:
    - nodePort: 31603
    ---
    ports:
    - nodePort: 32678
    =====
    service/iron-gallery-service-datacenter edited
</details>