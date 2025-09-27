# Deploy Applications with Kubernetes Deployments

* Create a deployment named `httpd` to deploy the application `httpd` using the image `httpd:latest` (ensure to specify the tag)


  ```bash
  kubectl create deployment httpd --image httpd:latest

  kubectl  get all
  ```

  <details>
  <summary>outputs</summary>

      # kubectl create deployment httpd --image httpd:latest

      deployment.apps/httpd created

      # kubectl  get all

      NAME                         READY   STATUS    RESTARTS   AGE
      pod/httpd-69545969bd-q7pmz   1/1     Running   0          32s

      NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
      service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   11m

      NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
      deployment.apps/httpd   1/1     1            1           32s

      NAME                               DESIRED   CURRENT   READY   AGE
      replicaset.apps/httpd-69545969bd   1         1         1       32s
  </details>