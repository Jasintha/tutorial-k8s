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

## - Advance pod configurations 

### -   Liveness and Readiness Probes

```
# This example presumes that the container within the pod exposes a /healthz endpoint, which responds to HTTP GET requests. This endpoint is used by the liveness probe to check the health of the container. If the probe fails (e.g., if the endpoint does not return a success status code), Kubernetes can automatically restart the container based on the failure threshold and restart policy specified in the pod's configuration.

apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: example-image
    ports:
      - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3  // This denotes the time to wait before first initiating the probe after the container has started. It's set to 3 seconds in this example,
      periodSeconds: 3   // This indicates how often (in seconds) the probe is performed. Here, it's configured to run every 3 seconds, ensuring that the application's health is checked frequently.


```
#### - Specifying Failure Threshold in Liveness Probe

```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: example-image
    ports:
      - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 3
      failureThreshold: 3 # Number of failed attempts before taking action ( After it reaches the failure threshold, the container will be restarted according to the pod's restart Policy.)

```
##### - Specifying Restart Policy for the Pod

```
# Always: Restart the container if it stops for any reason.
# OnFailure: Restart the container only if it exits with a non-zero status (indicative of an error).
# Never: Do not automatically restart the container if it stops or fails.
```
```
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  restartPolicy: Always
  containers:
  - name: example-container
    image: example-image
    ports:
      - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 3
      failureThreshold: 3

```
#### -  Readiness Probe 

```
# Readiness probes are particularly useful in managing the rollout and updates of applications. For example, during a rolling update, Kubernetes uses readiness probes to ensure that new pods are fully ready to handle requests before scaling down the old ones, minimizing downtime or disruption.

```

```
apiVersion: v1
kind: Pod
metadata:
  name: readiness-example-pod
spec:
  containers:
    - name: readiness-example-container
      image: nginx:latest
      ports:
        - containerPort: 8080
      readinessProbe:
        httpGet:
          path: /ready
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 5

```

## -  Pod Affinity Configuration:

```
Pod affinity is a set of rules that allows you to constrain which nodes your pod can be scheduled on based on labels on other pods that are already running on the node rather than based on labels on nodes. This can ensure that certain pods are co-located in the same cloud provider zone, region, or node. Here, I'll integrate this snippet into a full pod configuration for clarity and explain how it works:

```

```
apiVersion: v1
kind: Pod
metadata:
  name: my-affinity-pod
  labels:
    app: my-app
spec:
  containers:
  - name: my-container
    image: nginx
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
              - key: app
                operator: In
                values:
                - web-server
          topologyKey: "kubernetes.io/hostname"

```
```
In  # This operator is used to filter based on the presence of a key and its value being in the specified list. Example: "operator": "In", "values": ["value1", "value2"] means the label must have a key with a value either "value1" or "value2".
NotIn # This is the opposite of In. It filters out labels where the key exists and its value is in the specified list.
Exists #This operator checks for the existence of a label with the specified key, regardless of the value. "operator": "Exists" (values field is not specified) means any label with the specified key will fulfill the requirement.
DoesNotExist #This checks that no label with the specified key exists. Example: "operator": "DoesNotExist" means that the label’s key must not be present on the pod for the condition to be satisfied.

```
#### - podAntiAffinity 

```
podAntiAffinity to ensure that the pod is not scheduled on a node that already hosts a pod with specific labels. 
```

```
apiVersion: v1
kind: Pod
metadata:
  name: my-anti-affinity-pod
  labels:
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
              - key: app
                operator: In
                values:
                  - myapp
          topologyKey: "kubernetes.io/hostname"

```

