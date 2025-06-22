## Repository structure

.
├── go-server
│   ├── Dockerfile
│   ├── go.mod
│   ├── go-server.yaml
│   ├── go-simple-server
│   ├── go.sum
│   └── main.go
├── grafana
│   ├── grafana-graphs.pdf
│   ├── grafana-secret.yaml
│   └── grafana.yaml
├── ingress
│   ├── monitoring-ingress.yaml
│   └── webserver-ingress.yaml
├── prometheus
│   └── prometheus.yaml
└── README.md

## Assignment

1. Start an instance of Kubernetes (use  minikube or other similar project, don't use cloud provider's managed kubernetes).
2. Run the `go-simple-server` service (built from this repo) in the Kubernetes instance.
   * create Dockerfile and build container
   * push container image into some public registry (e.g. https://hub.docker.com/)
   * create kubernetes manifests and deploy them into your k8s cluster - Service rolling update must not lead to an outage.
3. Run Prometheus and Grafana in the same Kubernetes instance observing the running service.
4. Create a Grafana dashboard with information about the state of the `go-simple-server` service. It should show at least:
   1. Number of requests served by the service grouped by a status code (as a graph).
   2. Histogram of a response latency.

## Solution

### 1.1
https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download

### 2.1
created the file "Dockerfile" built it with "docker build -t <image:tag> ."

### 2.2
pushed it to docker hub https://hub.docker.com/repository/docker/ffdzzr/go-simple-server/general
with "docker login" and then "docker push <image:tag>"

### 2.3
wrote all the .yaml manifests for go-server, Prometheus and Grafana

### 3
applied the manifests with
"kubectl apply -f <path_to_manifest>"

### 4
you can view the graphs in path ./grafana/grafana-graphs.pdf

## Few commands used along the way
- Watch the namespace as you update to verify Rolling update
```bash
while true; do kubectl get all; sleep 5; printf '=%.0s' {1..70}; echo; done
```
- Get more requests to fill up your grafana graphs
```bash
while true; do curl webserver.com; printf "\\t"; sleep 2; done
```

## Thoughts
- Most of the components are in one file. it was easier to apply all the changes, but for production it might be better to separate them
- I shared grafana-secret.yaml which should be a sin, but this is only for testing purposes
- For Ingress controller I used "minikube addons enable ingress", but I saw one pretty nicely written on github.
https://github.com/kubernetes/ingress-nginx/blob/main/deploy/prometheus/deployment.yaml
but from what I understood, there is a limit on what you can do with minikube. However, I still tried it but had to run "sudo minikube tunnel" to get it working, so I went back to the addon.
- Using only version specific images like grafana/grafana:12.0.2 and not latest. However maybe it's overkill for Grafana when it's not required to run all the time, unlike Go-server and Prometheus. By using latest tag you wouldn't have to watch it's versions for new vulnerabilities all the time.

## Links that I saved
### Docker + Kubernetes
yaml validator - https://www.yamllint.com/
k8s Service - https://kubernetes.io/docs/concepts/services-networking/service/
k8s Deployment - https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
k8s Namespaces - https://kubernetes.io/docs/tasks/administer-cluster/namespaces/#creating-a-new-namespace
k8s Secret - https://kubernetes.io/docs/concepts/configuration/secret/
k8s ConfigMap - https://kubernetes.io/docs/concepts/configuration/configmap/
k8s Volumes - https://kubernetes.io/docs/concepts/storage/volumes/
k8s RolingUpdate - https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy
k8s liveness and readiness probe - https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
k8s Persistant volume and claim - https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/
Ingress controller manifest - https://github.com/kubernetes/ingress-nginx/blob/main/deploy/prometheus/deployment.yaml
test ingress in minikube - https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

### Prometheus
Grafana + Prometheus - https://grafana.com/docs/grafana/latest/getting-started/get-started-grafana-prometheus/
Prometheus promql operators - https://prometheus.io/docs/prometheus/latest/querying/operators/
Prometheus manifest example - https://github.com/techiescamp/kubernetes-prometheus
Inspiration - https://github.com/techiescamp/kubernetes-prometheus/tree/master
cheatsheet - https://promlabs.com/promql-cheat-sheet/

### Grafana
Environment variables - https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/

### Some amazing people on Youtube
- Kubernetes
https://www.youtube.com/@TechWorldwithNana

- Prometheus
https://www.youtube.com/@PromLabs
