# Pods

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

