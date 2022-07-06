# Creating a new Helm chart

## What is Helm and charts?

Helm is an application package manager for Kubernetes. It simplifies creating, deploying and managing applications on Kubernetes.

A Helm Chart is a collection of files that describe a related set of Kubernetes resources.

## Create your chart folder

```shell
mkdir micro-crypto-finder
cd micro-crypto-finder
```

## Create base chart

```shell
helm create micro-crypto-finder
```

## Run example chart

```shell
helm install --dry-run --debug --generate-name ./micro-crypto-finder
```

The output will be:

```shell
install.go:178: [debug] Original chart version: ""
install.go:195: [debug] CHART PATH: /Users/betofonseca/IdeaProjects/micro-crypto-finder/helm-chart/micro-crypto-finder

NAME: micro-crypto-finder-1657115411
LAST DEPLOYED: Wed Jul  6 15:50:12 2022
NAMESPACE: default
STATUS: pending-install
REVISION: 1
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
affinity: {}
autoscaling:
  enabled: false
  maxReplicas: 100
  minReplicas: 1
  targetCPUUtilizationPercentage: 80
fullnameOverride: ""
image:
  pullPolicy: IfNotPresent
  repository: nginx
  tag: ""
imagePullSecrets: []
ingress:
  annotations: {}
  className: ""
  enabled: false
  hosts:
  - host: chart-example.local
    paths:
    - path: /
      pathType: ImplementationSpecific
  tls: []
nameOverride: ""
nodeSelector: {}
podAnnotations: {}
podSecurityContext: {}
replicaCount: 1
resources: {}
securityContext: {}
service:
  port: 80
  type: ClusterIP
serviceAccount:
  annotations: {}
  create: true
  name: ""
tolerations: []

HOOKS:
---
# Source: micro-crypto-finder/templates/tests/test-connection.yaml
apiVersion: v1
kind: Pod
metadata:
  name: "micro-crypto-finder-1657115411-test-connection"
  labels:
    helm.sh/chart: micro-crypto-finder-0.1.0
    app.kubernetes.io/name: micro-crypto-finder
    app.kubernetes.io/instance: micro-crypto-finder-1657115411
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['micro-crypto-finder-1657115411:80']
  restartPolicy: Never
MANIFEST:
---
# Source: micro-crypto-finder/templates/serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: micro-crypto-finder-1657115411
  labels:
    helm.sh/chart: micro-crypto-finder-0.1.0
    app.kubernetes.io/name: micro-crypto-finder
    app.kubernetes.io/instance: micro-crypto-finder-1657115411
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
---
# Source: micro-crypto-finder/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: micro-crypto-finder-1657115411
  labels:
    helm.sh/chart: micro-crypto-finder-0.1.0
    app.kubernetes.io/name: micro-crypto-finder
    app.kubernetes.io/instance: micro-crypto-finder-1657115411
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: micro-crypto-finder
    app.kubernetes.io/instance: micro-crypto-finder-1657115411
---
# Source: micro-crypto-finder/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: micro-crypto-finder-1657115411
  labels:
    helm.sh/chart: micro-crypto-finder-0.1.0
    app.kubernetes.io/name: micro-crypto-finder
    app.kubernetes.io/instance: micro-crypto-finder-1657115411
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: micro-crypto-finder
      app.kubernetes.io/instance: micro-crypto-finder-1657115411
  template:
    metadata:
      labels:
        app.kubernetes.io/name: micro-crypto-finder
        app.kubernetes.io/instance: micro-crypto-finder-1657115411
    spec:
      serviceAccountName: micro-crypto-finder-1657115411
      securityContext:
        {}
      containers:
        - name: micro-crypto-finder
          securityContext:
            {}
          image: "nginx:1.16.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
            {}

NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=micro-crypto-finder,app.kubernetes.io/instance=micro-crypto-finder-1657115411" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

## Deploy the chart

Lets try to deploy this example using NodePort (default is ClusterIP), this way we bind our service to a port, which is useful for development, allowing us to access this application using a port.

```shell
helm install micro-crypto-finder ./micro-crypto-finder --set service.type=NodePort
### result...
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /Users/betofonseca/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /Users/betofonseca/.kube/config
NAME: micro-crypto-finder
LAST DEPLOYED: Wed Jul  6 19:06:28 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services micro-crypto-finder)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT
```

We can now check the list of deployed charts using:

```shell
helm list
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
micro-crypto-finder     default         1               2022-07-06 19:06:28.589986 +0200 CEST   deployed        micro-crypto-finder-0.1.0       1.16.0
```
