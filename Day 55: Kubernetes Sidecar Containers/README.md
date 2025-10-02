# Kubernetes Sidecar Containers

1. Create a pod named **webserver**.

2. Create an **emptyDir** volume **shared-logs**.

3. Create two containers from **nginx** and **ubuntu** images with **latest** tag only and remember to mention tag i.e **nginx:latest**, nginx container name should be **nginx-container** and ubuntu container name should be **sidecar-container** on webserver pod.

4. Add command on sidecar-container **`"sh","-c","while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"`**

5. Mount the volume **shared-logs** on both containers at location **/var/log/nginx**, all containers should be up and running.


- [Doc - Sidecar Containers](https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers/)

```bash
kubectl run webserver --image=nginx:latest --dry-run=client -oyaml > sidecar.yaml

# vi sidecar.yaml

kubectl  apply -f sidecar.yaml

kubectl get po

kubectl exec webserver -c sidecar-container -- ls /var/log/nginx
```
<details>
<summary>sidecar.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: webserver
  name: webserver
spec:
  volumes:
    - name: shared-logs
      emptyDir: {}
  containers:
  - image: nginx:latest
    name: nginx-container
    volumeMounts:
      - name: shared-logs
        mountPath: /var/log/nginx
  - image: ubuntu:latest
    name: sidecar-container
    command: ["sh","-c","while true; do cat /var/log/nginx/access.log /var/log/nginx/error.log; sleep 30; done"]
    volumeMounts:
      - name: shared-logs
        mountPath: /var/log/nginx
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  ```
</details>

<details>
<summary>outputs</summary>

  ### kubectl  apply -f sidecar.yaml
    pod/webserver created

  ### kubectl get po
    NAME        READY   STATUS    RESTARTS   AGE
    webserver   2/2     Running   0          42s
  
  ### kubectl exec webserver -c sidecar-container -- ls /var/log/nginx
    access.log
    rror.log

  ### kubectl exec webserver -c nginx-container -- ls /var/log/nginx
    access.log
    error.log
</details>