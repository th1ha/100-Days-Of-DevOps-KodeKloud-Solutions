# Deploy Pods in Kubernetes Cluster

* Create a pod named `pod-nginx` using the nginx image with the `latest` tag. Ensure to specify the tag as `nginx:latest`
* Set the app label to `nginx_app`, and name the container as `nginx-container`


  ```bash
  kubectl run pod-nginx --image nginx:latest --dry-run=client -oyaml > pod-nginx.yaml

  kubectl  apply -f pod-nginx.yaml

  kubectl get po --show-labels

  kubectl get po -l app=nginx_app
  ```

  <details>
  <summary>pod-nginx.yaml</summary>
  
    ```yaml
    apiVersion: v1
      kind: Pod
      metadata:
        creationTimestamp: null
        labels:
          run: pod-nginx
          app: nginx_app
        name: pod-nginx
      spec:
        containers:
        - image: nginx:latest
          name: nginx-container
          resources: {}
        dnsPolicy: ClusterFirst
        restartPolicy: Always
      status: {}
    ```
  </details>

  <details>
  <summary>outputs</summary>

      # kubectl run pod-nginx --image nginx:latest --dry-run=client -oyaml > pod-nginx.yaml

      # kubectl  apply -f pod-nginx.yaml

      pod/pod-nginx created

      # kubectl get po --show-labels

      NAME        READY   STATUS    RESTARTS   AGE   LABELS
      pod-nginx   1/1     Running   0          31s   app=nginx_app,run=pod-nginx

      # kubectl get po -l app=nginx_app

      NAME        READY   STATUS    RESTARTS   AGE
      pod-nginx   1/1     Running   0          78s
  </details>