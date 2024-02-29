# This everything about pod that you know

```
pod id the atomic object among the kubernetes objects

```

## creating pod from command (imparative mode)

```
kubectl run nginx-pod --image=nginx:latest --restart=Never

```

## creatin pod from file (declartive mode)
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-container
    image: nginx:latest

```
## creatin pod from file command 
```
kuberctl apply -f pod.yaml  (which create if not exsits or update)

kubectl create -f pod.yam  (which create or failed if exsits)

```
