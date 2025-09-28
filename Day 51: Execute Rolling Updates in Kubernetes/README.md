# Execute Rolling Updates in Kubernetes

* Execute a rolling update for this application, integrating the `nginx:1.18` image. The deployment is named `nginx-deployment`

[Doc - Rolling update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)

```bash
kubectl  get all

kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

kubectl rollout history deployment nginx-deployment

kubectl set image -h

kubectl set image deployment/nginx-deployment nginx-container=nginx:1.18

kubectl rollout history deployment nginx-deployment

kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

kubectl  get all
```

<details>
<summary>outputs</summary>

  ### kubectl  get all

    NAME                                   READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-989f57c54-bq6pk   1/1     Running   0          70s
    pod/nginx-deployment-989f57c54-djdzl   1/1     Running   0          70s
    pod/nginx-deployment-989f57c54-kb954   1/1     Running   0          70s

    NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
    service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        9m25s
    service/nginx-service   NodePort    10.96.186.22   <none>        80:30008/TCP   70s

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/nginx-deployment   3/3     3            3           70s

    NAME                                         DESIRED   CURRENT   READY   AGE
    replicaset.apps/nginx-deployment-989f57c54   3         3         3       70s

  ### kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

    spec:
      containers:
      - image: nginx:1.16
        imagePullPolicy: IfNotPresent
        name: nginx-container

  ### kubectl rollout history deployment nginx-deployment

    deployment.apps/nginx-deployment 
    REVISION  CHANGE-CAUSE
    1         <none>

  ### kubectl set image -h
    Examples:
    # Set a deployment's nginx container image to 'nginx:1.9.1', and its busybox container image to 'busybox'
    kubectl set image deployment/nginx busybox=busybox nginx=nginx:1.9.1
    
    # Update all deployments' and rc's nginx container's image to 'nginx:1.9.1'
    kubectl set image deployments,rc nginx=nginx:1.9.1 --all
    
    # Update image of all containers of daemonset abc to 'nginx:1.9.1'
    kubectl set image daemonset abc *=nginx:1.9.1
    ---
    Usage:
    kubectl set image (-f FILENAME | TYPE NAME) CONTAINER_NAME_1=CONTAINER_IMAGE_1 ...
    CONTAINER_NAME_N=CONTAINER_IMAGE_N [options]

  ### kubectl set image deployment/nginx-deployment nginx-container=nginx:1.18

    deployment.apps/nginx-deployment image updated

  ### kubectl rollout history deployment nginx-deployment

    deployment.apps/nginx-deployment 
    REVISION  CHANGE-CAUSE
    1         <none>
    2         <none>

  ### kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

    spec:
      containers:
      - image: nginx:1.18
        imagePullPolicy: IfNotPresent
        name: nginx-container

  ### kubectl  get all

    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-58cf54c7f6-2jh99   1/1     Running   0          66s
    pod/nginx-deployment-58cf54c7f6-5g2z4   1/1     Running   0          72s
    pod/nginx-deployment-58cf54c7f6-zcgs8   1/1     Running   0          64s

    NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
    service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        18m
    service/nginx-service   NodePort    10.96.186.22   <none>        80:30008/TCP   9m50s

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/nginx-deployment   3/3     3            3           9m50s

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/nginx-deployment-58cf54c7f6   3         3         3       72s
    replicaset.apps/nginx-deployment-989f57c54    0         0         0       9m50s
</details>