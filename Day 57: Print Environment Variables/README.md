# Print Environment Variables

1. Create a **pod** named **print-envars-greeting**.

2. Configure spec as, the container name should be **print-env-container** and use **bash** image.

3. Create three environment variables:

  - **GREETING** and its value should be **Welcome to**
  - **COMPANY** and its value should be **Stratos**
  - **GROUP** and its value should be **Group**

4. Use command **`["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']`** (please use this exact command), also set its **restartPolicy** policy to **Never** to avoid crash loop back.

5. You can check the output using **`kubectl logs -f print-envars-greeting`** command.

[Doc - environment variable for a container](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#define-an-environment-variable-for-a-container)

```bash
kubectl run print-envars-greeting --image=bash:latest --dry-run=client -oyaml > envpod.yaml 

# change the container name and add env
vi envpod.yaml

kubectl  apply -f envpod.yaml

kubectl  get po

kubectl logs print-envars-greeting
```
<details>
<summary>envpod.yaml</summary>

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: print-envars-greeting
  name: print-envars-greeting
spec:
  containers:
  - image: bash:latest
    name: print-env-container
    command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
    resources: {}
    env:
      - name: GREETING
        value: "Welcome to"
      - name: COMPANY
        value: "Stratos"
      - name: GROUP
        value: "Group"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
  ```
</details>

<details>
<summary>outputs</summary>

  ### kubectl  apply -f envpod.yaml
    pod/print-envars-greeting created

  #### kubectl  get po
    NAME                    READY   STATUS      RESTARTS   AGE
    print-envars-greeting   0/1     Completed   0          43s

  #### kubectl logs print-envars-greeting 
    Welcome to Stratos Group
</details>