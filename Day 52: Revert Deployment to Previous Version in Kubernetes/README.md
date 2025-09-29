# Revert Deployment to Previous Version in Kubernetes

* There exists a deployment named `nginx-deployment`; initiate a rollback to the previous revision

[Doc - Rollout](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_rollout/kubectl_rollout_undo/)

```bash
kubectl  get all

kubectl rollout history deployment nginx-deployment

kubectl rollout history deployment nginx-deployment --revision=1

kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

# rollback to the previous version
kubectl rollout undo deployment nginx-deployment --to-revision=1

kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"

kubectl rollout history deployment nginx-deployment

kubectl rollout history deployment nginx-deployment --revision=3
```

<details>
<summary>outputs</summary>

  ### kubectl  get all
    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/nginx-deployment-665bf769f5-jclkm   1/1     Running   0          31s
    pod/nginx-deployment-665bf769f5-pt75j   1/1     Running   0          41s
    pod/nginx-deployment-665bf769f5-qxxsh   1/1     Running   0          33s

    NAME                    TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
    service/kubernetes      ClusterIP   10.96.0.1    <none>        443/TCP        13m
    service/nginx-service   NodePort    10.96.73.9   <none>        80:30008/TCP   52s

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/nginx-deployment   3/3     3            3           52s

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/nginx-deployment-665bf769f5   3         3         3       41s
    replicaset.apps/nginx-deployment-989f57c54    0         0         0       52s

  ### kubectl rollout history deployment nginx-deployment
    deployment.apps/nginx-deployment 
    REVISION  CHANGE-CAUSE
    1         <none>
    2         kubectl set image deployment nginx-deployment nginx-container=nginx:stable --kubeconfig=/root/.kube/config --record=true

  ### kubectl rollout history deployment nginx-deployment --revision=1
    deployment.apps/nginx-deployment with revision #1
    Pod Template:
      Labels:       app=nginx-app
            pod-template-hash=989f57c54
      Containers:
      nginx-container:
        Image:      nginx:1.16
        Port:       <none>
        Host Port:  <none>
        Environment:        <none>
        Mounts:     <none>
      Volumes:      <none>
      Node-Selectors:       <none>
      Tolerations:  <none>

  ### kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"
    spec:
      containers:
      - image: nginx:stable
        imagePullPolicy: IfNotPresent
        name: nginx-container

  ### kubectl rollout undo deployment nginx-deployment --to-revision=1
    deployment.apps/nginx-deployment rolled back

  ### kubectl get deployments.apps nginx-deployment -oyaml | grep -i -C2 "image:"
    spec:
      containers:
      - image: nginx:1.16
        imagePullPolicy: IfNotPresent
        name: nginx-container

  ### kubectl rollout history deployment nginx-deployment
    deployment.apps/nginx-deployment 
    REVISION  CHANGE-CAUSE
    2         kubectl set image deployment nginx-deployment nginx-container=nginx:stable --kubeconfig=/root/.kube/config --record=true
    3         <none>
    
  ### kubectl rollout history deployment nginx-deployment --revision=3
    deployment.apps/nginx-deployment with revision #3
    Pod Template:
      Labels:       app=nginx-app
            pod-template-hash=989f57c54
      Containers:
      nginx-container:
        Image:      nginx:1.16
        Port:       <none>
        Host Port:  <none>
        Environment:        <none>
        Mounts:     <none>
      Volumes:      <none>
      Node-Selectors:       <none>
      Tolerations:  <none>
</details>