# Troubleshoot Deployment issues in Kubernetes

> The deployment name is **redis-deployment**. The pods are not in running state right now, so please look into the issue and fix the same.


```bash
kubectl get all

kubectl describe pod redis-deployment-54cdf4f76d-6nck9

kubectl get pods redis-deployment-54cdf4f76d-6nck9 -oyaml  | grep -i -C5 "configMap:"

kubectl get cm

kubectl edit deployments.apps redis-deployment

kubectl get all

kubectl describe pod/redis-deployment-5bcd4c7d64-g6qp8

kubectl edit deployments.apps redis-deployment

kubectl get all
```

<details>
<summary>outputs</summary>

  #### kubectl get all
    NAME                                    READY   STATUS              RESTARTS   AGE
    pod/redis-deployment-54cdf4f76d-6nck9   0/1     ContainerCreating   0          7m33s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   11m

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/redis-deployment   0/1     1            0           7m33s

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/redis-deployment-54cdf4f76d   1         1         0       7m33s

  #### kubectl describe pod redis-deployment-54cdf4f76d-6nck9
    Events:
    Type     Reason       Age                   From               Message
    ----     ------       ----                  ----               -------
    Normal   Scheduled    7m49s                 default-scheduler  Successfully assigned default/redis-deployment-54cdf4f76d-6nck9 to kodekloud-control-plane
    Warning  FailedMount  97s (x11 over 7m49s)  kubelet            MountVolume.SetUp failed for volume "config" : configmap "redis-conig" not found
    Warning  FailedMount  77s (x3 over 5m46s)   kubelet            Unable to attach or mount volumes: unmounted volumes=[config], unattached volumes=[], failed to process volumes=[]: timed out waiting for the condition

  #### kubectl get pods redis-deployment-54cdf4f76d-6nck9 -oyaml  | grep -i -C5 "configMap:"
    operator: Exists
    tolerationSeconds: 300
    volumes:
    - emptyDir: {}
      name: data
    - configMap:
        defaultMode: 420
        name: redis-conig
      name: config
    - name: kube-api-access-lv2n8
      projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
              path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
    
  #### kubectl get cm
    NAME               DATA   AGE
    kube-root-ca.crt   1      17m
    redis-config       2      13m

  #### kubectl edit deployments.apps redis-deployment
    - configMap:
          defaultMode: 420
          name: redis-conig
        name: config
    ---
    - configMap:
          defaultMode: 420
          name: redis-config
        name: config
    =====
    deployment.apps/redis-deployment edited

  #### kubectl get all
    NAME                                    READY   STATUS              RESTARTS   AGE
    pod/redis-deployment-54cdf4f76d-6nck9   0/1     ContainerCreating   0          15m
    pod/redis-deployment-5bcd4c7d64-g6qp8   0/1     ErrImagePull        0          31s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   19m

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/redis-deployment   0/1     1            0           15m

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/redis-deployment-54cdf4f76d   1         1         0       15m
    replicaset.apps/redis-deployment-5bcd4c7d64   1         1         0       31s

  #### kubectl describe pod/redis-deployment-5bcd4c7d64-g6qp8
    Events:
    Type     Reason     Age                From               Message
    ----     ------     ----               ----               -------
    Normal   Scheduled  62s                default-scheduler  Successfully assigned default/redis-deployment-5bcd4c7d64-g6qp8 to kodekloud-control-plane
    Normal   Pulling    18s (x3 over 61s)  kubelet            Pulling image "redis:alpin"
    Warning  Failed     17s (x3 over 61s)  kubelet            Failed to pull image "redis:alpin": rpc error: code = NotFound desc = failed to pull and unpack image "docker.io/library/redis:alpin": failed to resolve reference "docker.io/library/redis:alpin": docker.io/library/redis:alpin: not found
    Warning  Failed     17s (x3 over 61s)  kubelet            Error: ErrImagePull
    Normal   BackOff    5s (x3 over 60s)   kubelet            Back-off pulling image "redis:alpin"
    Warning  Failed     5s (x3 over 60s)   kubelet            Error: ImagePullBackOff

  #### kubectl edit deployments.apps redis-deployment
    containers:
      - image: redis:alpin
        imagePullPolicy: IfNotPresent
        name: redis-container
    ----
    containers:
      - image: redis:alpine
        imagePullPolicy: IfNotPresent
        name: redis-container
    =====
    deployment.apps/redis-deployment edited

  #### kubectl get all
    NAME                                    READY   STATUS        RESTARTS   AGE
    pod/redis-deployment-54cdf4f76d-6nck9   0/1     Terminating   0          19m
    pod/redis-deployment-7c8d4f6ddf-jql5n   1/1     Running       0          29s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   23m

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/redis-deployment   1/1     1            1           19m

    NAME                                          DESIRED   CURRENT   READY   AGE
    replicaset.apps/redis-deployment-54cdf4f76d   0         0         0       19m
    replicaset.apps/redis-deployment-5bcd4c7d64   0         0         0       3m55s
    replicaset.apps/redis-deployment-7c8d4f6ddf   1         1         1       29s
</details>