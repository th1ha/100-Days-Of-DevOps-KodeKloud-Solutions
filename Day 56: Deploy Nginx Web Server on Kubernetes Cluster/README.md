#  Deploy Nginx Web Server on Kubernetes Cluster

1. Create a deployment using **nginx** image with **latest** tag only and remember to mention the tag i.e **nginx:latest**. Name it as **nginx-deployment**. The container should be named as **nginx-container**, also make sure replica counts are **3**.

2. Create a **NodePort** type service named **nginx-service**. The nodePort should be **30011**

```bash
kubectl create deployment nginx-deployment --image=nginx:latest --replicas=3 --dry-run=client -oyaml > nginx-deployment.yaml

# change the container name
vi nginx-deployment.yaml

kubectl apply -f nginx-deployment.yaml

kubectl expose deployment nginx-deployment --name=nginx-service --port=80 --target-port=80 --type=NodePort

# change the NodePort port
kubectl edit svc nginx-service

kubectl port-forward svc/nginx-service 8080:80 &

curl -I localhost:8080
```
<details>
<summary>nginx-deployment.yaml</summary>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx:latest
        name: nginx-container
  ```
</details>

<details>
<summary>outputs</summary>

  ### kubectl apply -f nginx-deployment.yaml
    deployment.apps/nginx-deployment created
  
  #### kubectl expose deployment nginx-deployment --name=nginx-service --port=80 --target-port=80 --type=NodePort
    service/nginx-service exposed

  #### kubectl edit svc nginx-service
    ports:
    - nodePort: 30957
    ---
    ports:
    - nodePort: 30011
    ==========
    service/nginx-service edited

  #### kubectl port-forward svc/nginx-service 8080:80 &
    [1] 3757
    Forwarding from [::1]:8080 -> 80

  #### curl -I localhost:8080
    Handling connection for 8080
    HTTP/1.1 200 OK
    Server: nginx/1.29.1
</details>