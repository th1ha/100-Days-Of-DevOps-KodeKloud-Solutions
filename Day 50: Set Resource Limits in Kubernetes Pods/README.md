# Set Resource Limits in Kubernetes Pods

* Create a pod named `httpd-pod` with a container named `httpd-container`. Use the httpd image with the `latest` tag (specify as `httpd:latest`). Set the following resource limits
  - Requests: Memory: `15Mi`, CPU: `100m`
  - Limits: Memory: `20Mi`, CPU: `100m`

[Container resources example](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#example-1)

  ```bash
  kubectl run httpd-pod --image httpd:latest --dry-run=client -oyaml > httpd-pod.yaml

  kubectl apply -f httpd-pod.yaml

  kubectl get po

  kubectl describe po httpd-pod | grep -i -E -A2 "Requests:|Limits:"
  ```

  <details>
  <summary>httpd-pod.yaml</summary>

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    creationTimestamp: null
    labels:
      run: httpd-pod
    name: httpd-pod
  spec:
    containers:
    - image: httpd:latest
      name: httpd-container
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"
    dnsPolicy: ClusterFirst
    restartPolicy: Always
  ```
  </details>

  <details>
  <summary>outputs</summary>

    # kubectl apply -f httpd-pod.yaml 

    pod/httpd-pod created

    # kubectl  get po
    
    NAME        READY   STATUS    RESTARTS   AGE
    httpd-pod   1/1     Running   0          33s
    
    # kubectl describe po httpd-pod | grep -i -E -A2 "Requests:|Limits:"
    
    Limits:
      cpu:     100m
      memory:  20Mi
    Requests:
      cpu:        100m
      memory:     15Mi
  </details>