# Persistent Volumes in Kubernetes

1. Create a **PersistentVolume** named as **pv-devops**. Configure the **spec** as storage class should be **manual**, set capacity to **5Gi**, set access mode to **ReadWriteOnce**, volume type should be **hostPath** and set path to **/mnt/dba** (this directory is already created, you might not be able to access it directly, so you need not to worry about it).

2. Create a **PersistentVolumeClaim** named as **pvc-devops**. Configure the **spec** as storage class should be **manual**, request **2Gi** of the storage, set access mode to **ReadWriteOnce**.

3. Create a **pod** named as **pod-devops**, mount the persistent volume you created with claim name **pvc-devops** at document root of the web server, the container within the pod should be named as **container-devops** using image **nginx** with **latest** tag only (remember to mention the tag i.e **nginx:latest**).

4. Create a node port type service named **web-devops** using node port **30008** to expose the web server running within the pod.

- [Doc - PersistetntVolume example](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolume)
- [Doc - PersistentVolumeClaim example](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-persistentvolumeclaim)
- [Doc - Create a Pod example](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/#create-a-pod)

<details>
<summary>pv-devops.yaml</summary>

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-devops
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/dba"
```
</details>

<details>
<summary>pvc-devops.yaml</summary>

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-devops
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```
</details>

```bash
kubectl run pod-devops --image=nginx:latest --dry-run=client -oyaml > pod-devops.yaml
```
<details>
<summary>pod-devops.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: pod-devops
  name: pod-devops
spec:
  containers:
  - image: nginx:latest
    name: container-devops
    resources: {}
    volumeMounts:
      - mountPath: /usr/share/nginx/html
        name: pvc-vol
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
    - name: pvc-vol
      persistentVolumeClaim:
        claimName: pvc-devops
```

</details>


```bash
kubectl apply -f pv-devops.yaml -f pvc-devops.yaml

kubectl get pv,pvc

kubectl apply -f pod-devops.yaml 

kubectl  get all

kubectl expose pod pod-devops --type NodePort --name web-devops --port 80 --target-port 80

# change the NodePort port
kubectl edit svc web-devops

kubectl port-forward svc/web-devops 8080:80 &

curl localhost:8080

kubectl logs pod-devops

kubectl exec pod-devops -- sh -c "echo Hello > /usr/share/nginx/html/index.html"

curl localhost:8080

kubectl logs pod-devops
```

<details>
<summary>outputs</summary>

  #### kubectl apply -f pv-devops.yaml -f pvc-devops.yaml
    persistentvolume/pv-devops created
    persistentvolumeclaim/pvc-devops created

  #### kubectl get pv,pvc
    NAME                         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE
    persistentvolume/pv-devops   5Gi        RWO            Retain           Bound    default/pvc-devops   manual                  26s

    NAME                               STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/pvc-devops   Bound    pv-devops   5Gi        RWO            manual         26s

  #### kubectl apply -f pod-devops.yaml 
    pod/pod-devops created

  #### kubectl get all
    NAME             READY   STATUS    RESTARTS   AGE
    pod/pod-devops   1/1     Running   0          18s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   30m

  #### kubectl expose pod pod-devops --type NodePort --name web-devops --port 80 --target-port 80
    service/web-devops exposed

  #### kubectl edit svc web-devops
    ports:
    - nodePort: 30823
    ---
    ports:
    - nodePort: 30008
    =====
    service/web-devops edited

  #### kubectl port-forward svc/web-devops 8080:80 &
    [1] 6536
    Forwarding from [::1]:8080 -> 80

  #### curl localhost:8080
    Handling connection for 8080
    <html>
    <head><title>403 Forbidden</title></head>
    <body>
    <center><h1>403 Forbidden</h1></center>

  #### kubectl logs pod-devops
    2025/10/07 14:47:01 [error] 87#87: *8 directory index of "/usr/share/nginx/html/" is forbidden, client: 127.0.0.1, server: localhost, request: "GET / HTTP/1.1", host: "localhost:8080"
    127.0.0.1 - - [07/Oct/2025:14:47:01 +0000] "GET / HTTP/1.1" 403 153 "-" "curl/7.76.1" "-"

  #### kubectl exec pod-devops -- sh -c "echo Hello > /usr/share/nginx/html/index.html"

  #### curl localhost:8080
    Handling connection for 8080
    Hello

  #### kubectl logs pod-devops
    127.0.0.1 - - [07/Oct/2025:14:48:59 +0000] "GET / HTTP/1.1" 200 6 "-" "curl/7.76.1" "-"
</details>