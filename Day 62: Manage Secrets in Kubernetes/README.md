# Manage Secrets in Kubernetes

1. We already have a secret key file **blog.txt** under **/opt** location on **jump host**. Create a **generic secret** named **blog**, it should contain the password/license-number present in **blog.txt** file.

2. Also create a **pod** named **secret-datacenter**.

3. Configure pod's **spec** as container name should be **secret-container-datacenter**, image should be **fedora** with **latest** tag (remember to mention the tag with image). Use **sleep** command for container so that it remains in running state. Consume the created secret and mount it under **/opt/demo** within the container.

4. To verify you can exec into the container **secret-container-datacenter**, to check the secret key under the mounted path **/opt/demo**. Before hitting the **Check** button please make sure pod/pods are in running state, also validation can take some time to complete so keep patience.

[Doc - Secret](https://kubernetes.io/docs/concepts/configuration/secret/#use-case-dotfiles-in-a-secret-volume)

---

<details>
<summary>steps</summary>

  #### cat /opt/blog.txt 
    5ecur3

  #### kubectl create secret generic blog --from-file /opt/blog.txt 
    secret/blog created

  #### kubectl get secrets blog -oyaml
  ```yaml
  apiVersion: v1
  data:
    blog.txt: NWVjdXIzCg==
  kind: Secret
  metadata:
    creationTimestamp: "2025-10-10T07:52:40Z"
    name: blog
    namespace: default
    resourceVersion: "964"
    uid: 6a140dca-b7ce-4bf6-921d-4f808833c586
  type: Opaque
  ```

  #### kubectl run secret-datacenter --image fedora:latest --dry-run=client -oyaml > sec-pod.yaml
  ```yaml
  apiVersion: v1
    kind: Pod
    metadata:
      creationTimestamp: null
      labels:
        run: secret-datacenter
      name: secret-datacenter
    spec:
      volumes:
        - name: secret-volume
          secret:
            secretName: blog
      containers:
      - image: fedora:latest
        name: secret-container-datacenter
        command: ["/bin/bash", "-c", "sleep infinity"]
        volumeMounts:
          - name: secret-volume
            readOnly: true
            mountPath: /opt/demo
      dnsPolicy: ClusterFirst
      restartPolicy: Always
  ```

  #### kubectl apply -f sec-pod.yaml 
    pod/secret-datacenter created

  #### kubectl get po
    NAME                READY   STATUS    RESTARTS   AGE
    secret-datacenter   1/1     Running   0          15s

  #### kubectl exec secret-datacenter -- ls /opt/demo
    blog.txt

  #### kubectl exec secret-datacenter -- cat /opt/demo/blog.txt
    5ecur3
</details>