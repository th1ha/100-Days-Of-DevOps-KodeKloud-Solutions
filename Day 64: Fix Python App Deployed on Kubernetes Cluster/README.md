# Fix Python App Deployed on Kubernetes Cluster

1. The deployment name is **python-deployment-datacenter**, its using **poroko/flask-demo-appimage**. The deployment and service of this app is already deployed.

2. nodePort should be **32345** and targetPort should be python flask app's default port.


---

<details>
<summary>steps</summary>

  #### kubectl get all
    NAME                                                READY   STATUS             RESTARTS   AGE
    pod/python-deployment-datacenter-6fdb496d59-dvrn2   0/1     ImagePullBackOff   0          54s

    NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
    service/kubernetes                  ClusterIP   10.96.0.1      <none>        443/TCP          3m32s
    service/python-service-datacenter   NodePort    10.96.36.143   <none>        8080:32345/TCP   54s

    NAME                                           READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/python-deployment-datacenter   0/1     1            0           54s

    NAME                                                      DESIRED   CURRENT   READY   AGE
    replicaset.apps/python-deployment-datacenter-6fdb496d59   1         1         0       54s

  #### kubectl describe pod python-deployment-datacenter-6fdb496d59-dvrn2
    Events:
    Type     Reason     Age                From               Message
    ----     ------     ----               ----               -------
    Normal   Scheduled  88s                default-scheduler  Successfully assigned default/python-deployment-datacenter-6fdb496d59-dvrn2 to kodekloud-control-plane
    Normal   Pulling    48s (x3 over 87s)  kubelet            Pulling image "poroko/flask-app-demo"
    Warning  Failed     48s (x3 over 87s)  kubelet            Failed to pull image "poroko/flask-app-demo": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/poroko/flask-app-demo:latest": failed to resolve reference "docker.io/poroko/flask-app-demo:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
    Warning  Failed     48s (x3 over 87s)  kubelet            Error: ErrImagePull
    Normal   BackOff    9s (x5 over 86s)   kubelet            Back-off pulling image "poroko/flask-app-demo"
    Warning  Failed     9s (x5 over 86s)   kubelet            Error: ImagePullBackOff

  #### kubectl edit deployments.apps python-deployment-datacenter
    containers:
      - image: poroko/flask-app-demo
    ---
    containers:
      - image: poroko/flask-demo-app
    =====
    deployment.apps/python-deployment-datacenter edited

  #### kubectl get all
    NAME                                                READY   STATUS    RESTARTS   AGE
    pod/python-deployment-datacenter-76b8f5c96c-2dk7d   1/1     Running   0          21s

    NAME                                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
    service/kubernetes                  ClusterIP   10.96.0.1      <none>        443/TCP          6m9s
    service/python-service-datacenter   NodePort    10.96.36.143   <none>        8080:32345/TCP   3m31s

    NAME                                           READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/python-deployment-datacenter   1/1     1            1           3m31s

    NAME                                                      DESIRED   CURRENT   READY   AGE
    replicaset.apps/python-deployment-datacenter-6fdb496d59   0         0         0       3m31s
    replicaset.apps/python-deployment-datacenter-76b8f5c96c   1         1         1       21s

  #### ubectl get pod python-deployment-datacenter-76b8f5c96c-2dk7d -oyaml | grep -i port
    ports:
    - containerPort: 5000

  #### kubectl get svc python-service-datacenter  -oyaml | grep -i port
    ports:
    - nodePort: 32345
      port: 8080
      targetPort: 8080

  #### kubectl edit svc python-service-datacenter
    ports:
    - nodePort: 32345
      port: 8080
      protocol: TCP
      targetPort: 8080
    ---
    ports:
    - nodePort: 32345
      port: 5000
      protocol: TCP
      targetPort: 5000
    =====
    service/python-service-datacenter edited

  #### kubectl port-forward svc/python-service-datacenter 5000:5000 &
    [1] 3436
    Forwarding from 127.0.0.1:5000 -> 5000
    Forwarding from [::1]:5000 -> 5000
  
  #### curl localhost:5000
    Handling connection for 5000
    Hello World Pyvo 1!
</details>