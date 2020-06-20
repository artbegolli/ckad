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