# Deploy Guest Book App on Kubernetes

## BACK-END TIER
1. Create a deployment named **redis-master** for Redis master.
  - Replicas count should be **1**.
  - Container name should be **master-redis-datacenter** and it should use image **redis**.
  - Request resources as **CPU** should be **100m** and **Memory** should be **100Mi**.
  - Container port should be redis default port i.e **6379**.

2. Create a service named **redis-master** for Redis master. Port and targetPort should be Redis default port i.e **6379**.

3. Create another deployment named **redis-slave** for Redis slave.
  - Replicas count should be **2**.
  - Container name should be **slave-redis-datacenter** and it should use **gcr.io/google_samples/gb-redisslave:v3** image.
  - Requests resources as **CPU** should be **100m** and **Memory** should be **100Mi**.
  - Define an environment variable named **GET_HOSTS_FROM** and its value should be **dns**.
  - Container port should be Redis default port i.e **6379**.

4. Create another service named **redis-slave**. It should use Redis default port i.e **6379**.

## FRONT END TIER

1. Create a deployment named **frontend**.
  - Replicas count should be **3**.
  - Container name should be **php-redis-datacenter** and it should use **gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff** image.
  - Request resources as **CPU** should be **100m** and **Memory** should be **100Mi**.
  - Define an environment variable named as **GET_HOSTS_FROM** and its value should be **dns**.
  - Container port should be **80**.

2. Create a service named **frontend**. Its **type** should be **NodePort**, port should be **80** and its **nodePort** should be **30009**.

Finally, you can check the **guestbook ap**p by clicking on **App** button.

> You can use any labels as per your choice.
---

<details>
<summary>steps</summary>

  #### create a deployment for Redis master
  ```bash
  kubectl create deployment redis-master --image redis --replicas 1 --port 6379 --dry-run=client -oyaml > redis-master.yaml
  ```
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: redis-master
    name: redis-master
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: redis-master
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: redis-master
      spec:
        containers:
        - image: redis
          name: master-redis-datacenter
          ports:
          - containerPort: 6379
          resources:
            requests:
              memory: "100Mi"
              cpu: "100m"
  ```

  #### kubectl apply -f redis-master.yaml 
    deployment.apps/redis-master created

  #### kubectl get po
    NAME                            READY   STATUS    RESTARTS   AGE
    redis-master-686856dd49-dnz8g   1/1     Running   0          14s

  #### kubectl expose deployment redis-master --name redis-master --port 6379 --target-port 6379
    service/redis-master exposed

  #### create a deployment for Redis slave
  ```bash
  kubectl create deployment redis-slave --image gcr.io/google_samples/gb-redisslave:v3 --replicas 2 --port 6379 --dry-run=client -oyaml > redis-slave.yaml
  ```
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: redis-slave
    name: redis-slave
  spec:
    replicas: 2
    selector:
      matchLabels:
        app: redis-slave
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: redis-slave
      spec:
        containers:
        - image: gcr.io/google_samples/gb-redisslave:v3
          name: slave-redis-datacenter
          ports:
          - containerPort: 6379
          resources:
            requests:
              memory: "100Mi"
              cpu: "100m"
          env:
            - name: GET_HOSTS_FROM
              value: dns
  ```

  #### kubectl apply -f redis-slave.yaml 
    deployment.apps/redis-slave created

  #### kubectl expose deployment redis-slave --name redis-slave --target-port 6379 --port 6379
    service/redis-slave exposed

  #### kubectl get all
    NAME                                READY   STATUS    RESTARTS   AGE
    pod/redis-master-686856dd49-dnz8g   1/1     Running   0          8m29s
    pod/redis-slave-668f85fd-6md4m      1/1     Running   0          74s
    pod/redis-slave-668f85fd-twqx6      1/1     Running   0          74s

    NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
    service/kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP    31m
    service/redis-master   ClusterIP   10.96.231.182   <none>        6379/TCP   5m30s
    service/redis-slave    ClusterIP   10.96.177.81    <none>        6379/TCP   11s

    NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/redis-master   1/1     1            1           8m29s
    deployment.apps/redis-slave    2/2     2            2           74s

    NAME                                      DESIRED   CURRENT   READY   AGE
    replicaset.apps/redis-master-686856dd49   1         1         1       8m29s
    replicaset.apps/redis-slave-668f85fd      2         2         2       74s

  #### create a deployment for frontend
  ```bash
  kubectl create deployment frontend --image gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff --replicas 3 --port 80 --dry-run=client -oyaml > frontend.yaml
  ```
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    creationTimestamp: null
    labels:
      app: frontend
    name: frontend
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: frontend
    strategy: {}
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: frontend
      spec:
        containers:
        - image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
          name: php-redis-datacenter
          ports:
          - containerPort: 80
          env:
            - name: GET_HOSTS_FROM
              value: dns
          resources:
            requests:
              memory: "100Mi"
              cpu: "100m"
  ```

  #### kubectl apply -f frontend.yaml 
    deployment.apps/frontend created

  #### kubectl expose deployment frontend --name frontend --type NodePort --port 80 --target-port 80
    service/frontend exposed

  #### kubectl edit svc frontend
    ports:
    - nodePort: 31481
    ---
    ports:
    - nodePort: 30009
    =====
    service/frontend edited

  #### kubectl get all
    NAME                                READY   STATUS    RESTARTS   AGE
    pod/frontend-6698d99d68-4h86t       1/1     Running   0          115s
    pod/frontend-6698d99d68-6jx7j       1/1     Running   0          115s
    pod/frontend-6698d99d68-mw5c9       1/1     Running   0          115s
    pod/redis-master-686856dd49-dnz8g   1/1     Running   0          13m
    pod/redis-slave-668f85fd-6md4m      1/1     Running   0          6m28s
    pod/redis-slave-668f85fd-twqx6      1/1     Running   0          6m28s

    NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
    service/frontend       NodePort    10.96.66.161    <none>        80:30009/TCP   68s
    service/kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        37m
    service/redis-master   ClusterIP   10.96.231.182   <none>        6379/TCP       10m
    service/redis-slave    ClusterIP   10.96.177.81    <none>        6379/TCP       5m25s

    NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/frontend       3/3     3            3           115s
    deployment.apps/redis-master   1/1     1            1           13m
    deployment.apps/redis-slave    2/2     2            2           6m28s

    NAME                                      DESIRED   CURRENT   READY   AGE
    replicaset.apps/frontend-6698d99d68       3         3         3       115s
    replicaset.apps/redis-master-686856dd49   1         1         1       13m
    replicaset.apps/redis-slave-668f85fd      2         2         2       6m28s

  #### kubectl port-forward service/frontend 8080:80 &
    [1] 5755
    Forwarding from [::1]:8080 -> 80

  #### curl localhost:8080
    Handling connection for 8080
    <html ng-app="redis">
      <head>
        <title>Guestbook</title>
        <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.12/angular.min.js"></script>
        <script src="controllers.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.6/ui-bootstrap-tpls.js"></script>
      </head>
      <body ng-controller="RedisCtrl">
        <div style="width: 50%; margin-left: 20px">
          <h2>Guestbook</h2>
</details>