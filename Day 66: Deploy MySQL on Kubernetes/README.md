# Deploy MySQL on Kubernetes

1. Create a PersistentVolume **mysql-pv**, its capacity should be **250Mi**, set other parameters as per your preference.

2. Create a PersistentVolumeClaim to request this PersistentVolume storage. Name it as **mysql-pv-claim** and request a **250Mi** of storage. Set other parameters as per your preference.

3. Create a deployment named **mysql-deployment**, use any mysql image as per your preference. Mount the PersistentVolume at mount path **/var/lib/mysql**.

4. Create a **NodePort** type service named **mysql** and set nodePort to **30007**.

5. Create a secret named **mysql-root-pass** having a key pair value, where key is **password** and its value is **YUIidhb667**, create another secret named **mysql-user-pass** having some key pair values, where frist key is **username** and its value is **kodekloud_joy**, second key is password and value is **BruCStnMT5**, create one more secret named **mysql-db-url**, key name is database and value is **kodekloud_db7**

6. Define some Environment variables within the container:
  - **name: MYSQL_ROOT_PASSWORD**, should pick value from secretKeyRef **name: mysql-root-pass** and **key: password**
  - **name: MYSQL_DATABASE**, should pick value from secretKeyRef **name: mysql-db-url** and **key: database**
  - **name: MYSQL_USER**, should pick value from secretKeyRef **name: mysql-user-pass** key **key: username**
  - **name: MYSQL_PASSWORD**, should pick value from secretKeyRef **name: mysql-user-pass** and **key: password**

[Doc - volume example](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)
---

<details>
<summary>steps</summary>

  #### vi mysql-volume.yaml
  ```yaml
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: mysql-pv
  spec:
    storageClassName: manual
    capacity:
      storage: 250Mi
    accessModes:
      - ReadWriteOnce
    hostPath:
      path: /mysql-data
  ---
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: mysql-pv-claim
  spec:
    storageClassName: manual
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 250Mi
  ```
  
  #### kubectl apply -f mysql-volume.yaml 
    persistentvolume/mysql-pv created
    persistentvolumeclaim/mysql-pv-claim created

  #### kubectl get pv,pvc
    NAME                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                    STORAGECLASS   REASON   AGE
    persistentvolume/mysql-pv   250Mi      RWO            Retain           Bound    default/mysql-pv-claim   manual                  29s

    NAME                                   STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    persistentvolumeclaim/mysql-pv-claim   Bound    mysql-pv   250Mi      RWO            manual         29s

  #### create secrets
  ```bash
  kubectl create secret generic mysql-root-pass --from-literal=password=YUIidhb667
  # secret/mysql-root-pass created
  kubectl create secret generic mysql-user-pass --from-literal=username=kodekloud_joy --from-literal=password=BruCStnMT5
  # secret/mysql-user-pass created
  kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db7
  # secret/mysql-db-url created
  ```

  #### vi mysql.yaml
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: mysql-deployment
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: mysql
    template:
      metadata:
        labels:
          app: mysql
      spec:
        containers:
          - name: mysql-container
            image: mysql:5.7
            ports:
              - containerPort: 3306
            env:
              - name: MYSQL_ROOT_PASSWORD
                valueFrom:
                  secretKeyRef:
                    name: mysql-root-pass
                    key: password
              - name: MYSQL_DATABASE
                valueFrom:
                  secretKeyRef:
                    name: mysql-db-url
                    key: database
              - name: MYSQL_USER
                valueFrom:
                  secretKeyRef:
                    name: mysql-user-pass
                    key: username
              - name: MYSQL_PASSWORD
                valueFrom:
                  secretKeyRef:
                    name: mysql-user-pass
                    key: password
            volumeMounts:
              - name: mysql-storage
                mountPath: /var/lib/mysql
        volumes:
          - name: mysql-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
  ```

  #### kubectl apply -f mysql.yaml 
    deployment.apps/mysql-deployment created

  #### kubectl get all
    NAME                                   READY   STATUS    RESTARTS   AGE
    pod/mysql-deployment-57f6fcccf-k446h   1/1     Running   0          52s

    NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   33m

    NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/mysql-deployment   1/1     1            1           52s

    NAME                                         DESIRED   CURRENT   READY   AGE
    replicaset.apps/mysql-deployment-57f6fcccf   1         1         1       52s

  #### kubectl expose deployment mysql-deployment --name mysql --type NodePort --port 3306 --target-port 3306
    service/mysql exposed

  #### kubectl edit svc mysql
    ports:
    - nodePort: 30766
    ---
    ports:
    - nodePort: 30007
    =====
    service/mysql edited
</details>