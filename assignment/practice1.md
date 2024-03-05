## Practice one 

### Basic Pod Creation

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
    ports:
    - containerPort: 80
```
### Apply YAML file 

```
kubectl apply -f simple-pod.yaml // Declrative  way to create resource

kubectl run httpd-pod --image=httpd --port=80  // imparetive way to create resource 

```
### Basic CRUD operation for pod / any kubernets object 

```
kubectl get pods

kubectl describe pod my-pod

kubectl create -f my-pod.yaml  // create only it's failed if exists 

kubectl edit pod my-pod  // Directly edit the configuration of a resource on the server. This command opens the resource in an editor.

kubectl apply -f my-pod.yaml  // Update & Create both 

kubectl delete pod my-pod 

```
### Observerbility 

```
kubectl logs my-pod

```

### Actions 

```
kubectl exec -it my-pod -- /bin/bash  //  Execute a command in a container.

kubectl port-forward pod/my-pod 8080:80  // Forward one or more local ports to a pod.

kubectl label pods my-pod new-label=awesome  // Update the labels on a resource.

kubectl annotate pod my-pod annotation-key="annotation-value" // 

```

### Practice Tasks 1 - Updating Label 

```
kubectl get pod nginx-pod --show-labels  // check the exsting lables 

kubectl label pods nginx-pod newlabel=nginx-webserver --overwrite  // Updating lable

kubectl get pods --show-labels

```
### Practice Tasks 2 - Changing the Image of a Running Pod

```
kubectl describe pod nginx-pod  // check the current version

kubectl set image pod/nginx-pod nginx-container=nginx:latest  // update into latest version of image

kubectl describe pod nginx-pod  // verify the change

```
### Practice Tasks 3 - Modifying Resource Limits and Requests

```
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    resources:
      requests:
        memory: "128Mi"
        cpu: "0.5"
      limits:
        memory: "256Mi"
        cpu: "1"
```
#### Applying chages 
```
kubectl apply -f simple-pod.yaml

```
### Practice Tasks 4 - Adding Environment Variables

```
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  containers:
    - name: my-app
      image: myapp:latest
      env:
        - name: DATABASE_URL
          value: "http://my-database-service:3306"

```
### Practice Tasks 5 - Updating Security Contexts 

#### - setting to pod level
```
apiVersion: v1
kind: Pod
metadata:
  name: secure-app-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
    - name: secure-app
      image: secureapp:latest

```
#### - Setting up for container level

```
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  containers:
    - name: my-app
      image: myapp:latest
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL
          add:
            - NET_BIND_SERVICE

```
