# Core concepts and common questions

## Explain

The `kubectl explain` command allows you to view specification documentation through kubectl.

e.g. to get pod spec documentation

```
k explain po.spec
```

## Pods

*Create a pod*


```
k run nginx --image=nginx --restart=Never -n mynamespace
```

*With YAML*
```
k run nginx --image=nginx --restart=Never -n mynamespace -o yaml --dry-run > pod.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*Run container with commands*
```
kubectl run busybox --image=busybox --command --restart=Never -- env
```

*View running container output*
```
kubectl run busybox --image=busybox --command --restart=Never -it -- env
```

OR

```
kubectl logs busybox -n mynamespace
```

If we don't specify parameter --command, then parameters after -- will be treated as arguments instead.

*Create a ResourceQuota with hard limits*

```
k create quota myrq --hard=cpu=1,memory=1G,pods=2 -o yaml --dry-run
```

*Pods all namespaces*
```
k get pods --all-namespaces
```

*Update image in pod*
```
# kubectl set image POD/POD_NAME CONTAINER_NAME=IMAGE_NAME:TAG
kubectl set image pod/nginx nginx=nginx:1.7.1
```

*Get Pods yaml*
```
k get pod nginx -o yaml
```

*Logs of previous instance*
```
kubectl logs nginx -p
```

*Execute simple shell*
```
k exec nginx -it --  /bin/sh
```

*Remove pod after completion*
```
kubectl run busybox --image=busybox -it --rm --restart=Never -- /bin/sh -c 'echo hello world'
```

*Multiple containers in a pod*

First create a dry run of a container with execution args
```
kubectl run busybox --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'echo hello;sleep 3600' > pod.yaml
```

Modify the yaml with an additional container but a new name
```
containers:
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    imagePullPolicy: IfNotPresent
    name: busybox
    resources: {}
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    name: busybox2
```
Exec command in the pod
```
kubectl exec -it busybox -c busybox2 -- ls
```

## Pod Design

*Get pod and show labels*

```
k get pods --show-labels
```

*Overwrite pod labels*
```
k label --overwrite nginx2 app=v2
```

*Show label columns*
```
k get pods -L app
```

*Query specific label*
```
k get pods -l app=v2
```

*Remove pod labels*
```
kubectl label po nginx1 nginx2 nginx3 app-
```

*Target a specific Node*

Here we set a nodeSelector

*Annotate pods*

```
k annotate pods nginx1 nginx2 nginx3 description="my description"
```

*Remove Annotations*
```
k annotate pods nginx1 nginx2 nginx3 description-
```

## Deployments

*Create deployment*
```
kubectl run nginx --image=nginx:1.7.8 --replicas=2 --port=80
```

*Check rollout*
```
k rollout status deploy nginx
```

*Update an Image*
```
k set image deployment nginx nginx=nginx:1.7.9
```
OR
```
kubectl edit deploy nginx
```

*Scale deployments*
```
kubectl scale deploy nginx --replicas=5
```

*Autoscale*
```
k autoscale deploy nginx --min=5 --max=10 --cpu-percent=80
```

## Jobs

*Create job*
```
kubectl create job hw --image=busybox -- echo hello;sleep 30;echo world
```

*Follow/Watch logs*
```
kubectl pod mypod -f
```

*Job logs*
```
kubectl logs job/busybox
```

*Parallel/completions/activeDeadlineSeconds*

* If you want to run a job multiple time set the `completions` field

* If you want to terminate a pod after a certain amount of time set the `activeDeadlineSeconds` field

* If you want to run pods in parallel, set the `parallel` field

```
kubectl explain job.spec
```

*CronJobs*
```
kubectl create cronjob busybox --image=busybox --schedule="*/1 * * * *" -- /bin/sh -c 'date; echo Hello from the Kubernetes cluster'
```

## Configuration

### ConfigMaps

*Create from literal*
```
kubectl create configmap artsmap --from-literal=foo=lala --from-literal=foo2=lolo
```

*Create from file*
```
kubectl create configmap artsmap --from-file=hello.txt
```

*Create from file with key*
```
kubectl create configmap artsmap --from-file=mykey=hello.txt
```

*Create from env-file*
```
kubectl create configmap artsmap --from-env-file=foo=lala --from-literal=foo2=lolo
```

*Show configmap*
```
kubectl get cm configmap -o yaml
```

*Create a pod with loaded value from a configmap*

```
kubectl create cm --from-literal=val1=var1
kubectl run nginx --image=nginx --restart=Never -o yaml --dry-run > nginx.yaml
vi nginx.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    env:
      - name: option
        valueFrom:
          configMapKeyRef:
            name: option
            key: var1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*Create a pod with all env from a configmap*
```
kubectl create cm --from-literal=val1=var1
kubectl run nginx --image=nginx --restart=Never -o yaml --dry-run > nginx.yaml
vi nginx.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    envFrom:
      configMapRef:
        - name: anotherone
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*Create a pod with a mounted configmap*
```
kubectl create cm cmvolume --from-literal=var8=val8 --from-literal=var9=val9
kubectl run nginxvol --image=nginx --restart=Never -o yaml --dry-run > nginxvol.yaml
```

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginxvol
  name: nginxvol
spec:
  volumes:
    - name: myvol
      configMap:
        name: cmvolume
  containers:
  - image: nginx
    name: nginxvol
    resources: {}
    volumeMounts:
      - name: myvol
        mountPath: /etc/lala
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

### Securtiy Context

*Run as User with ID 101*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginxsec
  name: nginxsec
spec:
  securityContext:
    runAsUser: 101
  containers:
  - image: nginx
    name: nginxsec
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*Running with capabilities*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginxsec
  name: nginxsec
spec:
  securityContext:
    capabilities: 
      add: ["NET_ADMIN", "SYS_TIME"]
  containers:
  - image: nginx
    name: nginxsec
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

### Requests and Limits

```
kubectl run nginx --image=nginx --restart=Never --requests='cpu=100m,memory=256Mi' --limits='cpu=200m,memory=512Mi'
```

### Secrets
*Create a secret*
```
kubectl create secret generic mysecret --from-literal=pw=mypassword
```

*Mount a secret*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginxsec
  name: nginxsec
spec:
  volumes:
    - name: secvol
      secret:
        secretName: mysecret
  containers:
  - image: nginx
    name: nginxsec
    resources: {}
    volumeMounts:
      - name: secvol
        mountPath: /etc/foo
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*From env variable*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    env: # our env variables
    - name: USERNAME # asked name
      valueFrom:
        secretKeyRef: # secret reference
          name: mysecret2 # our secret's name
          key: username # the key of the data in the secret
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
### Service Accounts

*Create*

```
kubectl create sa my-serviceaccount
```

*User service account*
```
kubectl run nginx --image=nginx --restart=Never --serviceaccount=myuser
```
OR
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  serviceAccountName: myuser # we use pod.spec.serviceAccountName
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

## Observability

### Liveness and Readiness

*Liveness with exec*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    livenessProbe: 
      initialDelaySeconds: 5 # add this line
      periodSeconds: 5 # add this line as well
      exec:
        command:
        - ls
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

*Readiness with httpGet*
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    ports:
      - containerPort: 80 # Note: Readiness probes runs on the container during its whole lifecycle. Since nginx exposes 80, containerPort: 80 is not required for readiness to work.
    readinessProbe: # declare the readiness probe
      httpGet: # add this line
        path: / #
        port: 80 #
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

### Debugging

*Delete w/o grace*
```
kubectl delete po busybox --force --grace-period=0
```

### Show resource usages
```
k top nodes
```

**metric-server must be running**

## Services and Networking
*Create a pod and expose*
```
kubectl run nginx --image=nginx --restart=Never --port 80 --expose
```

*Query service*
```
kubectl get svc nginx
```

*Query endpoints*
```
kubectl get ep
```

*Convert ClusterIP to NodePort*

```
kubectl edit svc nginx
```

Replace the type from `ClusterIP` to `NodePort`

*Expose a deployment / Creating a service*
```
kubectl expose deployment foo --port=6201 --target-port=8080 --type=ClusterIP
```

*Creating a test pod*
```
kubectl run busybox --image=busybox -it --rm --restart=Never -- sh
```

*Create a network policy*
```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: access-nginx # pick a name
spec:
  podSelector:
    matchLabels:
      run: nginx # selector for the pods
  ingress: # allow ingress traffic
  - from:
    - podSelector: # from pods
        matchLabels: # with this label
          access: granted
```