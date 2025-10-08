# Init Containers in Kubernetes

1. Create a **Deployment** named as **ic-deploy-devops**.

2. Configure **spec** as replicas should be **1**, labels app should be **ic-devops**, template's metadata lables **app** should be the same **ic-devops**.

3. The **initContainers** should be named as **ic-msg-devops**, use image **fedora** with **latest** tag and use command **`'/bin/bash'`**, **`'-c'`** and **`'echo Init Done - Welcome to xFusionCorp Industries > /ic/media'`**. The volume mount should be named as **ic-volume-devops** and mount path should be **/ic**.

4. Main container should be named as **ic-main-devops**, use image **fedora** with **latest** tag and use command **`'/bin/bash'`**, **`'-c'`** and **`'while true; do cat /ic/media; sleep 5; done'`**. The volume mount should be named as **ic-volume-devops** and mount path should be **/ic**.

5. Volume to be named as **ic-volume-devops** and it should be an emptyDir type.


- [Doc - Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/#init-containers-in-use)
- [Doc - emptyDir](https://kubernetes.io/docs/concepts/storage/volumes/#emptydir)

```bash
kubectl create deployment ic-deploy-devops --image fedora:latest --replicas=1 --dry-run=client -oyaml > devops.yaml
```
<details>
<summary>devops.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ic-devops
  name: ic-deploy-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-devops
  template:
    metadata:
      labels:
        app: ic-devops
    spec:
      volumes:
      - name: ic-volume-devops
        emptyDir: {}
      initContainers:
      - image: fedora:latest
        name: ic-msg-devops
        command: ['/bin/bash', '-c', 'echo Init Done - Welcome to xFusionCorp Industries > /ic/media']
        volumeMounts:
        - mountPath: /ic
          name: ic-volume-devops
      containers:
      - image: fedora:latest
        name: ic-main-devops
        command: ['/bin/bash', '-c', 'while true; do cat /ic/media; sleep 5; done']
        volumeMounts:
        - mountPath: /ic
          name: ic-volume-devops
```
</details>


```bash
kubectl apply -f devops.yaml

kubectl get all

kubectl logs pods/ic-deploy-devops-64d7d4d4df-nbslr -c ic-main-devops
```

<details>
<summary>outputs</summary>

  #### kubectl apply -f devops.yaml
    deployment.apps/ic-deploy-devops created

  #### kubectl get all
    NAME                                    READY   STATUS    RESTARTS   AGE
    pod/ic-deploy-devops-64d7d4d4df-nbslr   1/1     Running   0          50s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   22m

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/ic-deploy-devops   1/1     1            1           50s

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/ic-deploy-devops-64d7d4d4df   1         1         1       50s

  #### kubectl logs pods/ic-deploy-devops-64d7d4d4df-nbslr -c ic-main-devops
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
    Init Done - Welcome to xFusionCorp Industries
</details>