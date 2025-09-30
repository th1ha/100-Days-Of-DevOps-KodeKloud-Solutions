# Resolve VolumeMounts Issue in Kubernetes

* The pod name is `nginx-phpfpm` and configmap name is `nginx-config`. Identify and fix the problem
* Once resolved, copy `/home/thor/index.php` file from the `jump host` to the `nginx-container` within the nginx document root. After this, you should be able to access the `website` using Website button on the top bar.

- [Doc - Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)
- [Doc - kubectl cp](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_cp/)

```bash
kubectl  get all

kubectl port-forward svc/nginx-service 8080:8099 &

curl localhost:8080

kubectl get cm

kubectl get cm nginx-config -oyaml

kubectl get pod nginx-phpfpm -oyaml | grep -i -A3 "volumeMounts:"

kubectl edit pod nginx-phpfpm

kubectl apply -f /tmp/kubectl-edit-2674912302.yaml --force

curl  -I localhost:8080

kubectl cp index.php nginx-phpfpm:/var/www/html/ -c nginx-container

curl  -I localhost:8080
```

<details>
<summary>outputs</summary>

  ### kubectl  get all
    NAME               READY   STATUS    RESTARTS   AGE
    pod/nginx-phpfpm   2/2     Running   0          3m17s

    NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    service/kubernetes      ClusterIP   10.96.0.1       <none>        443/TCP          8m15s
    service/nginx-service   NodePort    10.96.239.166   <none>        8099:30008/TCP   3m17s

  ### kubectl port-forward svc/nginx-service 8080:8099 &
    [1] 3650
    thor@jumphost ~$ Forwarding from [::1]:8080 -> 8099

  ### curl localhost:8080
    Handling connection for 8080
    <html>
    <head><title>404 Not Found</title></head>
    <body>
    <center><h1>404 Not Found</h1></center>
    <hr><center>nginx/1.29.1</center>
    </body>
    </html>

  ### kubectl get cm
    NAME               DATA   AGE
    kube-root-ca.crt   1      12m
    nginx-config       1      7m52s

  ### kubectl get cm nginx-config -oyaml
  ```yaml
  apiVersion: v1
  data:
    nginx.conf: |
      events {
      }
      http {
        server {
          listen 8099 default_server;
          listen [::]:8099 default_server;
          # Set nginx to serve files from the shared volume!
          root /var/www/html;
          index  index.html index.htm index.php;
          server_name _;
          location / {
            try_files $uri $uri/ =404;
          }
          location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param REQUEST_METHOD $request_method;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass 127.0.0.1:9000;
          }
        }
      }
  kind: ConfigMap
  metadata:
    name: nginx-config
    namespace: default
  ```

  ### kubectl get pod nginx-phpfpm -oyaml | grep -i -A3 "volumeMounts:"
    volumeMounts:
    - mountPath: /var/www/html
      name: shared-files
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
    --
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: shared-files
    - mountPath: /etc/nginx/nginx.conf

  ### kubectl edit pod nginx-phpfpm
  ```yaml
  - image: nginx:latest
    imagePullPolicy: Always
    name: nginx-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: shared-files
  ```
  ```yaml
  - image: nginx:latest
    imagePullPolicy: Always
    name: nginx-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/www/html
      name: shared-files
  ```
    error: pods "nginx-phpfpm" is invalid
    A copy of your changes has been stored to "/tmp/kubectl-edit-2674912302.yaml"
    error: Edit cancelled, no valid changes were saved.

  ### kubectl apply -f /tmp/kubectl-edit-2674912302.yaml --force
    pod/nginx-phpfpm configured

  ### curl  -I localhost:8080
    Handling connection for 8080
    HTTP/1.1 403 Forbidden
    Server: nginx/1.29.1
    Date: Tue, 30 Sep 2025 10:43:45 GMT
    Content-Type: text/html
    Content-Length: 153
    Connection: keep-alive

  ### curl  -I localhost:8080
    Handling connection for 8080
    HTTP/1.1 200 OK
    Server: nginx/1.29.1
    Date: Tue, 30 Sep 2025 10:45:03 GMT
    Content-Type: text/html; charset=UTF-8
    Connection: keep-alive
    X-Powered-By: PHP/7.2.34
</details>