# Kubectl Reference Guide
## For Kubernetes System Administrators

A comprehensive reference covering essential operations, advanced techniques, and powerful features that even experienced users might not know about.

---

## Table of Contents
1. [Installation & Configuration](#installation--configuration)
2. [Cluster & Context Management](#cluster--context-management)
3. [Pod Management](#pod-management)
4. [Deployment & ReplicaSet Operations](#deployment--replicaset-operations)
5. [Service & Networking](#service--networking)
6. [ConfigMaps & Secrets](#configmaps--secrets)
7. [Storage Management](#storage-management)
8. [Namespace Operations](#namespace-operations)
9. [Resource Management & Scaling](#resource-management--scaling)
10. [Debugging & Troubleshooting](#debugging--troubleshooting)
11. [Advanced Query & Filtering](#advanced-query--filtering)
12. [Security & RBAC](#security--rbac)

---

## Installation & Configuration

### Installation

**macOS:**
```bash
# Using Homebrew
brew install kubectl

# Or download binary
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

**Linux:**
```bash
# Download latest stable
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Or using package manager (Ubuntu/Debian)
sudo apt-get update && sudo apt-get install -y kubectl
```

**Windows:**
```powershell
# Using Chocolatey
choco install kubernetes-cli

# Or using scoop
scoop install kubectl
```

**Verify Installation:**
```bash
kubectl version --client
```

### Configuration

**Kubeconfig Location:**
- Default: `~/.kube/config`
- Custom: Set `KUBECONFIG` environment variable

**View Configuration:**
```bash
kubectl config view
```

**Get Current Context:**
```bash
kubectl config current-context
```

**Enable Auto-completion:**
```bash
# Bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc

# Zsh
source <(kubectl completion zsh)
echo "source <(kubectl completion zsh)" >> ~/.zshrc

# Alias with completion
alias k=kubectl
complete -o default -F __start_kubectl k
```

---

## Cluster & Context Management

### Basic Operations
```bash
# List clusters
kubectl config get-clusters

# List contexts
kubectl config get-contexts

# Switch context
kubectl config use-context context-name

# Set default namespace for context
kubectl config set-context --current --namespace=my-namespace

# View cluster info
kubectl cluster-info

# Check component status
kubectl get componentstatuses
# or
kubectl get cs
```

### 🎯 Easter Eggs & Power Tips

**1. Context Management Shortcuts**
```bash
# Quick context switching with alias
alias kctx='kubectl config use-context'
alias kcurrent='kubectl config current-context'

# List contexts with current highlighted
kubectl config get-contexts

# Create new context from existing cluster
kubectl config set-context dev-context \
  --cluster=my-cluster \
  --user=dev-user \
  --namespace=development

# Delete context
kubectl config delete-context old-context

# Rename context
kubectl config rename-context old-name new-name
```

**2. Multiple Kubeconfig Files**
```bash
# Merge multiple kubeconfig files
export KUBECONFIG=~/.kube/config:~/.kube/config-dev:~/.kube/config-prod

# View merged config
kubectl config view --flatten

# Save merged config
kubectl config view --flatten > ~/.kube/config-merged
```

**3. Quick Cluster Access Check**
```bash
# Test authentication
kubectl auth can-i create deployments

# Check all permissions
kubectl auth can-i --list

# Check permissions as different user
kubectl auth can-i create pods --as=system:serviceaccount:default:my-sa
```

---

## Pod Management

### Basic Operations
```bash
# List pods in current namespace
kubectl get pods

# List pods in all namespaces
kubectl get pods -A
# or
kubectl get pods --all-namespaces

# Get pod details
kubectl describe pod pod-name

# Get pod YAML
kubectl get pod pod-name -o yaml

# Create pod from file
kubectl apply -f pod.yaml

# Delete pod
kubectl delete pod pod-name

# Force delete pod
kubectl delete pod pod-name --force --grace-period=0
```

### Advanced Pod Operations
```bash
# Watch pods in real-time
kubectl get pods -w

# Get pods with labels
kubectl get pods -l app=nginx

# Get pods sorted by creation time
kubectl get pods --sort-by=.metadata.creationTimestamp

# Get pods on specific node
kubectl get pods --field-selector spec.nodeName=node-1

# Get pods with custom columns
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP
```

### 🎯 Easter Eggs & Power Tips

**1. Pod Inspection Shortcuts**
```bash
# Get pod with wide output (shows node, IP)
kubectl get pods -o wide

# Get previous container logs (for crashed containers)
kubectl logs pod-name --previous

# Get logs from specific container in pod
kubectl logs pod-name -c container-name

# Follow logs from all containers in pod
kubectl logs pod-name --all-containers=true -f

# Stream logs with timestamps
kubectl logs pod-name -f --timestamps

# Tail last N lines
kubectl logs pod-name --tail=100
```

**2. Pod Resource Usage**
```bash
# Get pod resource usage (requires metrics-server)
kubectl top pod

# Top pods by CPU
kubectl top pod --sort-by=cpu

# Top pods by memory
kubectl top pod --sort-by=memory

# Get resource usage for specific pod
kubectl top pod pod-name
```

**3. Pod Filtering Magic**
```bash
# Get pods not running
kubectl get pods --field-selector=status.phase!=Running

# Get pods on specific node with specific label
kubectl get pods --field-selector spec.nodeName=node-1 -l app=nginx

# Get completed pods
kubectl get pods --field-selector=status.phase=Succeeded

# Get failed pods
kubectl get pods --field-selector=status.phase=Failed
```

**4. Quick Pod Actions**
```bash
# Execute command in pod
kubectl exec pod-name -- ls /app

# Interactive shell in pod
kubectl exec -it pod-name -- /bin/bash
# or for Alpine-based images
kubectl exec -it pod-name -- /bin/sh

# Execute in specific container
kubectl exec -it pod-name -c container-name -- /bin/bash

# Copy files to/from pod
kubectl cp local-file.txt pod-name:/path/in/container
kubectl cp pod-name:/path/in/container/file.txt ./local-file.txt

# Port forward to local machine
kubectl port-forward pod-name 8080:80
```

**5. Pod Debugging**
```bash
# Run temporary debug pod
kubectl run debug-pod --rm -it --image=busybox -- /bin/sh

# Debug with specific image and attach
kubectl run debug --rm -it --image=nicolaka/netshoot -- /bin/bash

# Create ephemeral debug container (K8s 1.23+)
kubectl debug pod-name -it --image=busybox --target=container-name

# Debug node by running pod on it
kubectl debug node/node-name -it --image=ubuntu
```

---

## Deployment & ReplicaSet Operations

### Basic Operations
```bash
# Create deployment
kubectl create deployment nginx --image=nginx:latest

# Create deployment from file
kubectl apply -f deployment.yaml

# List deployments
kubectl get deployments

# Get deployment details
kubectl describe deployment deployment-name

# Delete deployment
kubectl delete deployment deployment-name
```

### Deployment Management
```bash
# Scale deployment
kubectl scale deployment deployment-name --replicas=5

# Update deployment image
kubectl set image deployment/deployment-name container-name=new-image:tag

# Check rollout status
kubectl rollout status deployment/deployment-name

# View rollout history
kubectl rollout history deployment/deployment-name

# Rollback to previous version
kubectl rollout undo deployment/deployment-name

# Rollback to specific revision
kubectl rollout undo deployment/deployment-name --to-revision=2

# Pause rollout
kubectl rollout pause deployment/deployment-name

# Resume rollout
kubectl rollout resume deployment/deployment-name

# Restart deployment (rolling restart)
kubectl rollout restart deployment/deployment-name
```

### 🎯 Easter Eggs & Power Tips

**1. Deployment Strategies**
```bash
# Create deployment with record flag (saves command in annotations)
kubectl create deployment nginx --image=nginx --record

# Update deployment and record change
kubectl set image deployment/nginx nginx=nginx:1.21 --record

# View detailed rollout history with change causes
kubectl rollout history deployment/nginx --revision=3
```

**2. Advanced Scaling**
```bash
# Autoscale based on CPU
kubectl autoscale deployment nginx --min=2 --max=10 --cpu-percent=80

# View HPA (Horizontal Pod Autoscaler)
kubectl get hpa

# Edit HPA
kubectl edit hpa nginx
```

**3. Quick Deployment Updates**
```bash
# Update multiple containers at once
kubectl set image deployment/app \
  container1=image1:v2 \
  container2=image2:v2

# Set environment variable
kubectl set env deployment/app ENV_VAR=value

# Set resource limits
kubectl set resources deployment/nginx \
  --limits=cpu=200m,memory=512Mi \
  --requests=cpu=100m,memory=256Mi
```

**4. Deployment Debugging**
```bash
# Check deployment events
kubectl describe deployment deployment-name | grep -A 10 Events

# Get ReplicaSets for deployment
kubectl get rs -l app=deployment-name

# Get pods for deployment with selector
kubectl get pods -l app=deployment-name

# View deployment spec
kubectl get deployment deployment-name -o yaml
```

---

## Service & Networking

### Basic Operations
```bash
# List services
kubectl get services
# or
kubectl get svc

# Create ClusterIP service
kubectl expose deployment nginx --port=80 --target-port=8080

# Create NodePort service
kubectl expose deployment nginx --type=NodePort --port=80

# Create LoadBalancer service
kubectl expose deployment nginx --type=LoadBalancer --port=80

# Get service details
kubectl describe service service-name

# Delete service
kubectl delete service service-name
```

### Advanced Service Operations
```bash
# Get service endpoints
kubectl get endpoints service-name

# Get service with wide output
kubectl get svc -o wide

# Create service from YAML
kubectl apply -f service.yaml

# Edit service
kubectl edit service service-name
```

### 🎯 Easter Eggs & Power Tips

**1. Service Discovery**
```bash
# Get service cluster IP
kubectl get svc service-name -o jsonpath='{.spec.clusterIP}'

# Get service external IP (for LoadBalancer)
kubectl get svc service-name -o jsonpath='{.status.loadBalancer.ingress[0].ip}'

# Get all services with their selectors
kubectl get svc -o custom-columns=NAME:.metadata.name,SELECTOR:.spec.selector

# Find pods behind service
kubectl get endpoints service-name -o jsonpath='{.subsets[*].addresses[*].ip}'
```

**2. Port Forwarding Techniques**
```bash
# Forward local port to service
kubectl port-forward service/nginx 8080:80

# Forward to pod through service
kubectl port-forward svc/nginx 8080:80

# Forward random local port
kubectl port-forward service/nginx :80

# Forward multiple ports
kubectl port-forward service/nginx 8080:80 8443:443
```

**3. Network Policies**
```bash
# List network policies
kubectl get networkpolicies

# Describe network policy
kubectl describe networkpolicy policy-name

# Test network connectivity
kubectl run test-pod --rm -it --image=busybox -- wget -O- http://service-name
```

**4. Ingress Management**
```bash
# List ingress resources
kubectl get ingress

# Get ingress details
kubectl describe ingress ingress-name

# Get ingress with address
kubectl get ingress -o wide

# Watch ingress for external IP assignment
kubectl get ingress -w
```

---

## ConfigMaps & Secrets

### ConfigMap Operations
```bash
# Create ConfigMap from literal values
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2

# Create ConfigMap from file
kubectl create configmap my-config --from-file=config.properties

# Create ConfigMap from directory
kubectl create configmap my-config --from-file=./config-dir/

# List ConfigMaps
kubectl get configmaps
# or
kubectl get cm

# View ConfigMap data
kubectl describe configmap my-config

# Get ConfigMap YAML
kubectl get configmap my-config -o yaml

# Edit ConfigMap
kubectl edit configmap my-config

# Delete ConfigMap
kubectl delete configmap my-config
```

### Secret Operations
```bash
# Create generic secret from literal
kubectl create secret generic my-secret --from-literal=password=supersecret

# Create secret from file
kubectl create secret generic my-secret --from-file=ssh-privatekey=~/.ssh/id_rsa

# Create Docker registry secret
kubectl create secret docker-registry regcred \
  --docker-server=myregistry.com \
  --docker-username=user \
  --docker-password=pass \
  --docker-email=email@example.com

# Create TLS secret
kubectl create secret tls tls-secret --cert=path/to/cert.crt --key=path/to/key.key

# List secrets
kubectl get secrets

# View secret (base64 encoded)
kubectl get secret my-secret -o yaml

# Decode secret value
kubectl get secret my-secret -o jsonpath='{.data.password}' | base64 -d

# Delete secret
kubectl delete secret my-secret
```

### 🎯 Easter Eggs & Power Tips

**1. ConfigMap Management**
```bash
# Create ConfigMap from env file
kubectl create configmap my-config --from-env-file=.env

# Replace ConfigMap from file
kubectl create configmap my-config --from-file=app.conf --dry-run=client -o yaml | kubectl apply -f -

# Get specific key from ConfigMap
kubectl get configmap my-config -o jsonpath='{.data.key1}'

# List all ConfigMap keys
kubectl get configmap my-config -o jsonpath='{.data}' | jq 'keys'
```

**2. Secret Management Best Practices**
```bash
# Create secret without shell history
kubectl create secret generic my-secret --from-literal=password="$(read -s -p 'Password: ' pwd; echo $pwd)"

# View secret names only (don't expose values)
kubectl get secrets -o custom-columns=NAME:.metadata.name,TYPE:.type

# Backup secrets
kubectl get secret my-secret -o yaml > secret-backup.yaml

# Copy secret to another namespace
kubectl get secret my-secret -n namespace1 -o yaml | \
  sed 's/namespace: namespace1/namespace: namespace2/' | \
  kubectl apply -f -
```

**3. Immutable ConfigMaps & Secrets (K8s 1.19+)**
```bash
# Create immutable ConfigMap
kubectl create configmap my-config --from-literal=key=value --dry-run=client -o yaml | \
  kubectl patch --local -f - -p '{"immutable": true}' -o yaml | \
  kubectl apply -f -
```

---

## Storage Management

### Persistent Volume Operations
```bash
# List persistent volumes
kubectl get pv

# Get PV details
kubectl describe pv pv-name

# List persistent volume claims
kubectl get pvc

# Get PVC details
kubectl describe pvc pvc-name

# Create PVC from file
kubectl apply -f pvc.yaml

# Delete PVC
kubectl delete pvc pvc-name
```

### Storage Classes
```bash
# List storage classes
kubectl get storageclass
# or
kubectl get sc

# Get storage class details
kubectl describe storageclass standard

# Set default storage class
kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

### 🎯 Easter Eggs & Power Tips

**1. Volume Inspection**
```bash
# Get PV with capacity and status
kubectl get pv -o custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage,STATUS:.status.phase,CLAIM:.spec.claimRef.name

# Get PVC with storage class
kubectl get pvc -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,VOLUME:.spec.volumeName,CAPACITY:.spec.resources.requests.storage,STORAGECLASS:.spec.storageClassName

# Find unbound PVs
kubectl get pv --field-selector=status.phase=Available

# Find PVCs pending
kubectl get pvc --field-selector=status.phase=Pending
```

**2. Volume Troubleshooting**
```bash
# Check pod's volume mounts
kubectl get pod pod-name -o jsonpath='{.spec.volumes[*].name}'

# Check if PVC is actually bound
kubectl get pvc pvc-name -o jsonpath='{.status.phase}'

# Find which pod is using PVC
kubectl get pods -o json | jq -r '.items[] | select(.spec.volumes[]?.persistentVolumeClaim.claimName=="pvc-name") | .metadata.name'
```

---

## Namespace Operations

### Basic Operations
```bash
# List namespaces
kubectl get namespaces
# or
kubectl get ns

# Create namespace
kubectl create namespace dev

# Delete namespace (deletes all resources in it!)
kubectl delete namespace dev

# Get resources in namespace
kubectl get all -n namespace-name

# Get all resources in all namespaces
kubectl get all -A
```

### 🎯 Easter Eggs & Power Tips

**1. Namespace Management**
```bash
# Set default namespace for current context
kubectl config set-context --current --namespace=dev

# Get current namespace
kubectl config view --minify -o jsonpath='{..namespace}'

# Create namespace from YAML
kubectl apply -f - <<EOF
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    env: prod
EOF
```

**2. Resource Quotas & Limits**
```bash
# List resource quotas
kubectl get resourcequota -n namespace-name

# Describe quota
kubectl describe resourcequota quota-name -n namespace-name

# List limit ranges
kubectl get limitrange -n namespace-name

# View namespace resource usage
kubectl describe namespace namespace-name
```

**3. Cross-Namespace Operations**
```bash
# Copy secret across namespaces
kubectl get secret my-secret -n source-ns -o yaml | \
  sed 's/namespace: source-ns/namespace: target-ns/' | \
  kubectl apply -f -

# Find resources across all namespaces with label
kubectl get all --all-namespaces -l app=nginx
```

---

## Resource Management & Scaling

### Basic Scaling
```bash
# Scale deployment
kubectl scale deployment nginx --replicas=5

# Scale ReplicaSet
kubectl scale rs replicaset-name --replicas=3

# Scale StatefulSet
kubectl scale statefulset mysql --replicas=3
```

### Resource Limits
```bash
# View resource requests and limits
kubectl describe nodes | grep -A 5 "Allocated resources"

# Get pod resource usage
kubectl top pod

# Get node resource usage
kubectl top node
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Scaling**
```bash
# Scale based on current replicas
kubectl scale deployment nginx --current-replicas=2 --replicas=4

# Conditional scaling (only if current count matches)
kubectl scale deployment nginx --replicas=5 --current-replicas=3

# Scale multiple deployments
kubectl scale deployment --replicas=3 deploy1 deploy2 deploy3

# Get current replica count
kubectl get deployment nginx -o jsonpath='{.spec.replicas}'
```

**2. Resource Requests & Limits**
```bash
# Set resource limits for deployment
kubectl set resources deployment nginx \
  --limits=cpu=500m,memory=512Mi \
  --requests=cpu=250m,memory=256Mi

# View resource requests/limits
kubectl describe pod pod-name | grep -A 5 "Limits\|Requests"

# Get pods over resource limits
kubectl get pods -o json | jq -r '.items[] | select(.status.phase=="Running") | select(.status.containerStatuses[].restartCount > 0) | .metadata.name'
```

**3. Horizontal Pod Autoscaling**
```bash
# Create HPA
kubectl autoscale deployment nginx --cpu-percent=50 --min=2 --max=10

# View HPA status
kubectl get hpa

# Detailed HPA info
kubectl describe hpa nginx

# Update HPA
kubectl patch hpa nginx -p '{"spec":{"maxReplicas":20}}'

# Delete HPA
kubectl delete hpa nginx
```

**4. Vertical Pod Autoscaling (if installed)**
```bash
# Create VPA
kubectl create -f vpa.yaml

# List VPAs
kubectl get vpa

# Describe VPA
kubectl describe vpa vpa-name
```

---

## Debugging & Troubleshooting

### Basic Debugging
```bash
# Get pod logs
kubectl logs pod-name

# Follow logs
kubectl logs -f pod-name

# Get logs from previous container instance
kubectl logs pod-name --previous

# Get events
kubectl get events --sort-by=.metadata.creationTimestamp

# Get events for specific pod
kubectl get events --field-selector involvedObject.name=pod-name
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Log Management**
```bash
# Get logs from all containers in pod
kubectl logs pod-name --all-containers=true

# Get logs from multiple pods
kubectl logs -l app=nginx --all-containers=true

# Get logs with tail
kubectl logs pod-name --tail=100 -f

# Get logs since time
kubectl logs pod-name --since=1h

# Get logs between times
kubectl logs pod-name --since-time=2024-01-01T00:00:00Z

# Export logs to file
kubectl logs pod-name > pod-logs.txt

# Stream logs from all pods with label
kubectl logs -l app=nginx -f --max-log-requests=10
```

**2. Events Analysis**
```bash
# Watch events in real-time
kubectl get events -w

# Get warning events only
kubectl get events --field-selector type=Warning

# Get events for namespace
kubectl get events -n namespace-name

# Events for specific resource type
kubectl get events --field-selector involvedObject.kind=Pod

# Events sorted by time (most recent first)
kubectl get events --sort-by=.metadata.creationTimestamp -A | tail -20
```

**3. Pod Troubleshooting**
```bash
# Check pod status
kubectl get pod pod-name -o jsonpath='{.status.phase}'

# Get pod restart count
kubectl get pod pod-name -o jsonpath='{.status.containerStatuses[0].restartCount}'

# Check container state
kubectl get pod pod-name -o jsonpath='{.status.containerStatuses[*].state}'

# Get pod conditions
kubectl get pod pod-name -o jsonpath='{.status.conditions[*].type}'

# Get pod QoS class
kubectl get pod pod-name -o jsonpath='{.status.qosClass}'

# Check which node pod is on
kubectl get pod pod-name -o jsonpath='{.spec.nodeName}'
```

**4. Node Troubleshooting**
```bash
# Get node conditions
kubectl describe node node-name | grep -A 5 Conditions

# Check node capacity
kubectl describe node node-name | grep -A 5 Capacity

# Get node resource usage
kubectl top node node-name

# Cordon node (mark unschedulable)
kubectl cordon node-name

# Drain node (evict pods)
kubectl drain node-name --ignore-daemonsets --delete-emptydir-data

# Uncordon node
kubectl uncordon node-name

# Get pods on specific node
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=node-name
```

**5. Network Debugging**
```bash
# Test DNS resolution from pod
kubectl exec pod-name -- nslookup kubernetes.default

# Test connectivity to service
kubectl exec pod-name -- wget -O- http://service-name

# Run network debug pod
kubectl run netshoot --rm -it --image=nicolaka/netshoot -- /bin/bash

# Check service endpoints
kubectl get endpoints service-name

# Describe network policy
kubectl describe networkpolicy policy-name
```

**6. Debug Containers (K8s 1.23+)**
```bash
# Add ephemeral debug container to running pod
kubectl debug pod-name -it --image=busybox --target=container-name

# Create copy of pod with debug container
kubectl debug pod-name -it --image=busybox --share-processes --copy-to=debug-pod

# Debug node by running privileged pod
kubectl debug node/node-name -it --image=ubuntu
```

---

## Advanced Query & Filtering

### JSONPath Queries
```bash
# Get pod names only
kubectl get pods -o jsonpath='{.items[*].metadata.name}'

# Get pod IPs
kubectl get pods -o jsonpath='{.items[*].status.podIP}'

# Get container images
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'

# Get pod and container names
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].name}{"\n"}{end}'
```

### Custom Columns
```bash
# Custom columns for pods
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName,IP:.status.podIP

# Custom columns for nodes
kubectl get nodes -o custom-columns=NAME:.metadata.name,CPU:.status.capacity.cpu,MEMORY:.status.capacity.memory

# Custom columns for deployments
kubectl get deployments -o custom-columns=NAME:.metadata.name,REPLICAS:.spec.replicas,AVAILABLE:.status.availableReplicas
```

### 🎯 Easter Eggs & Power Tips

**1. Advanced Filtering**
```bash
# Get pods with specific label
kubectl get pods -l app=nginx

# Get pods with multiple label selectors
kubectl get pods -l 'app=nginx,env=prod'

# Get pods without specific label
kubectl get pods -l 'app!=nginx'

# Get pods with label in set
kubectl get pods -l 'env in (prod,staging)'

# Get pods with label exists
kubectl get pods -l 'app'

# Field selector examples
kubectl get pods --field-selector status.phase=Running
kubectl get pods --field-selector metadata.namespace!=default
kubectl get services --field-selector spec.type=LoadBalancer
```

**2. Output Formatting**
```bash
# YAML output
kubectl get pod pod-name -o yaml

# JSON output
kubectl get pod pod-name -o json

# Name only
kubectl get pods -o name

# Wide output
kubectl get pods -o wide

# Custom output with go-template
kubectl get pods -o go-template='{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
```

**3. Complex JSONPath Queries**
```bash
# Get pods not ready
kubectl get pods -o jsonpath='{range .items[?(@.status.phase!="Running")]}{.metadata.name}{"\n"}{end}'

# Get persistent volumes not bound
kubectl get pv -o jsonpath='{range .items[?(@.status.phase!="Bound")]}{.metadata.name}{"\t"}{.status.phase}{"\n"}{end}'

# Get services with external IPs
kubectl get svc -o jsonpath='{range .items[?(@.status.loadBalancer.ingress)]}{.metadata.name}{"\t"}{.status.loadBalancer.ingress[0].ip}{"\n"}{end}'

# Get pods grouped by node
kubectl get pods -o json | jq -r '.items | group_by(.spec.nodeName) | .[] | {(.[0].spec.nodeName): [.[] | .metadata.name]} | to_entries[] | "\(.key): \(.value | join(", "))"'
```

**4. Sorting**
```bash
# Sort pods by creation time
kubectl get pods --sort-by=.metadata.creationTimestamp

# Sort pods by restart count
kubectl get pods --sort-by='.status.containerStatuses[0].restartCount'

# Sort nodes by CPU capacity
kubectl get nodes --sort-by=.status.capacity.cpu
```

**5. Using jq for Advanced Processing**
```bash
# Get pod names and images
kubectl get pods -o json | jq -r '.items[] | "\(.metadata.name): \(.spec.containers[].image)"'

# Count pods by status
kubectl get pods -o json | jq -r '.items | group_by(.status.phase) | .[] | {(.[0].status.phase): length}'

# Find pods with high restart counts
kubectl get pods -o json | jq -r '.items[] | select(.status.containerStatuses[]?.restartCount > 5) | .metadata.name'

# Get all container images used
kubectl get pods -o json | jq -r '.items[].spec.containers[].image' | sort -u
```

---

## Security & RBAC

### Service Accounts
```bash
# List service accounts
kubectl get serviceaccounts
# or
kubectl get sa

# Create service account
kubectl create serviceaccount my-sa

# Get service account details
kubectl describe serviceaccount my-sa

# Get service account token
kubectl get secret $(kubectl get sa my-sa -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 -d
```

### RBAC (Role-Based Access Control)
```bash
# List roles
kubectl get roles

# List cluster roles
kubectl get clusterroles

# Create role
kubectl create role pod-reader --verb=get,list,watch --resource=pods

# Create cluster role
kubectl create clusterrole pod-reader --verb=get,list,watch --resource=pods

# Create role binding
kubectl create rolebinding read-pods --role=pod-reader --user=john

# Create cluster role binding
kubectl create clusterrolebinding read-pods --clusterrole=pod-reader --user=john

# Check permissions
kubectl auth can-i create deployments
kubectl auth can-i create deployments --as=john
kubectl auth can-i create deployments --as=system:serviceaccount:default:my-sa
```

### 🎯 Easter Eggs & Power Tips

**1. RBAC Auditing**
```bash
# List all permissions for user
kubectl auth can-i --list --as=john

# List all permissions for service account
kubectl auth can-i --list --as=system:serviceaccount:namespace:sa-name

# Get all role bindings
kubectl get rolebindings,clusterrolebindings --all-namespaces -o wide

# Find bindings for specific user
kubectl get rolebindings,clusterrolebindings --all-namespaces -o json | \
  jq -r '.items[] | select(.subjects[]?.name=="john") | {name: .metadata.name, namespace: .metadata.namespace, kind: .kind}'

# Find bindings for specific service account
kubectl get rolebindings,clusterrolebindings --all-namespaces -o json | \
  jq -r '.items[] | select(.subjects[]?.name=="my-sa") | {name: .metadata.name, namespace: .metadata.namespace, kind: .kind}'
```

**2. Security Context**
```bash
# Check pod security context
kubectl get pod pod-name -o jsonpath='{.spec.securityContext}'

# Check container security context
kubectl get pod pod-name -o jsonpath='{.spec.containers[0].securityContext}'

# Find privileged pods
kubectl get pods -o json | jq -r '.items[] | select(.spec.containers[].securityContext.privileged==true) | .metadata.name'

# Find pods running as root
kubectl get pods -o json | jq -r '.items[] | select(.spec.securityContext.runAsUser==0 or .spec.containers[].securityContext.runAsUser==0) | .metadata.name'
```

**3. Pod Security Standards**
```bash
# Label namespace with pod security standard
kubectl label namespace my-namespace pod-security.kubernetes.io/enforce=restricted

# Check namespace labels
kubectl get namespace my-namespace -o jsonpath='{.metadata.labels}'

# Audit namespace for pod security
kubectl label namespace my-namespace pod-security.kubernetes.io/audit=restricted
kubectl label namespace my-namespace pod-security.kubernetes.io/warn=restricted
```

**4. Network Policies**
```bash
# Create network policy to deny all ingress
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
EOF

# List network policies
kubectl get networkpolicies

# Test network policy effect
kubectl run test --rm -it --image=busybox -- wget -O- --timeout=2 http://target-service
```

---

## Useful Aliases & Functions

Add these to your `~/.bashrc` or `~/.zshrc`:

```bash
# Basic aliases
alias k='kubectl'
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias kl='kubectl logs'
alias kex='kubectl exec -it'
alias kaf='kubectl apply -f'

# Get resources
alias kgp='kubectl get pods'
alias kgd='kubectl get deployments'
alias kgs='kubectl get services'
alias kgn='kubectl get nodes'

# Describe resources
alias kdp='kubectl describe pod'
alias kdd='kubectl describe deployment'
alias kds='kubectl describe service'

# Logs
alias kl='kubectl logs'
alias klf='kubectl logs -f'
alias klp='kubectl logs -p'

# Context and namespace
alias kctx='kubectl config use-context'
alias kns='kubectl config set-context --current --namespace'
alias kcurrent='kubectl config current-context'

# Useful functions
function kpf() {
  kubectl port-forward "$@"
}

function kshell() {
  kubectl exec -it "$1" -- /bin/sh
}

function kbash() {
  kubectl exec -it "$1" -- /bin/bash
}

function kdebug() {
  kubectl run debug-$RANDOM --rm -it --image=nicolaka/netshoot -- /bin/bash
}

# Get pod by label
function kgpl() {
  kubectl get pods -l "$1"
}

# Watch pods
function kwp() {
  watch kubectl get pods "$@"
}

# Get all resources in namespace
function kgall() {
  kubectl get all -n "$1"
}

# Delete all pods with label
function kdelpods() {
  kubectl delete pods -l "$1"
}

# Restart deployment
function krestart() {
  kubectl rollout restart deployment "$1"
}
```

---

## Quick Reference Tables

### Resource Short Names
| Full Name | Short Name |
|-----------|------------|
| pods | po |
| services | svc |
| deployments | deploy |
| replicasets | rs |
| statefulsets | sts |
| daemonsets | ds |
| jobs | - |
| cronjobs | cj |
| configmaps | cm |
| secrets | - |
| namespaces | ns |
| nodes | no |
| persistentvolumes | pv |
| persistentvolumeclaims | pvc |
| storageclasses | sc |
| ingresses | ing |
| networkpolicies | netpol |
| serviceaccounts | sa |

### Common Label Selectors
| Selector Type | Example |
|--------------|---------|
| Equality | `app=nginx` |
| Inequality | `app!=nginx` |
| Set-based (in) | `env in (prod,staging)` |
| Set-based (not in) | `env notin (dev,test)` |
| Exists | `app` |
| Does not exist | `!app` |
| Multiple | `app=nginx,env=prod` |

### Field Selectors
| Field | Example |
|-------|---------|
| metadata.name | `metadata.name=my-pod` |
| metadata.namespace | `metadata.namespace=default` |
| status.phase | `status.phase=Running` |
| spec.nodeName | `spec.nodeName=node-1` |

### Pod Phases
| Phase | Description |
|-------|-------------|
| Pending | Pod accepted but not running |
| Running | Pod bound to node and running |
| Succeeded | All containers terminated successfully |
| Failed | All containers terminated, at least one failed |
| Unknown | Pod state cannot be obtained |

---

## Best Practices & Common Pitfalls

### General Best Practices
1. **Always use namespaces** to organize resources
2. **Label everything** for easy filtering and organization
3. **Use resource requests and limits** for all containers
4. **Implement health checks** (liveness, readiness, startup probes)
5. **Never use `latest` tag** in production
6. **Use ConfigMaps and Secrets** instead of hardcoding
7. **Implement RBAC** with least privilege principle
8. **Use Network Policies** to control traffic flow
9. **Enable Pod Security Standards** in namespaces
10. **Always test with `--dry-run=client`** before applying

### Common Pitfalls

**Resource Management:**
- Not setting resource requests/limits
- Over-provisioning resources
- Not monitoring resource usage
- Forgetting to clean up old resources

**Networking:**
- Not understanding service types
- Incorrect network policies blocking traffic
- DNS resolution issues
- Not considering load balancer costs

**Storage:**
- Using wrong access modes
- Not backing up persistent volumes
- Forgetting to delete PVCs (orphaned volumes)
- Not understanding storage class reclaim policies

**Debugging:**
- Not checking events first
- Ignoring pod logs
- Not using proper log aggregation
- Forgetting about previous container logs

**Security:**
- Running containers as root
- Using privileged containers unnecessarily
- Not implementing RBAC
- Exposing secrets in environment variables
- Not scanning container images

---

## Troubleshooting Checklist

When things go wrong, check in this order:

1. **Pod Status**: `kubectl get pods`
2. **Pod Events**: `kubectl describe pod <pod-name>`
3. **Container Logs**: `kubectl logs <pod-name>`
4. **Previous Logs**: `kubectl logs <pod-name> --previous`
5. **Resource Usage**: `kubectl top pod <pod-name>`
6. **Service Endpoints**: `kubectl get endpoints <service-name>`
7. **Network Policies**: `kubectl get networkpolicies`
8. **RBAC Permissions**: `kubectl auth can-i <verb> <resource>`
9. **Node Status**: `kubectl describe node <node-name>`
10. **Cluster Events**: `kubectl get events --sort-by=.metadata.creationTimestamp`

---

## Additional Resources

### Official Documentation
- **Kubernetes Docs**: https://kubernetes.io/docs/
- **kubectl Reference**: https://kubernetes.io/docs/reference/kubectl/
- **API Reference**: https://kubernetes.io/docs/reference/kubernetes-api/

### Interactive Learning
- **Kubernetes Playground**: https://www.katacoda.com/courses/kubernetes
- **Play with Kubernetes**: https://labs.play-with-k8s.com/

### Tools
- **k9s**: Terminal UI for Kubernetes
- **kubectx/kubens**: Fast context/namespace switching
- **stern**: Multi-pod log tailing
- **kustomize**: Template-free YAML customization
- **helm**: Package manager for Kubernetes

### Cheat Sheets
- **Official Cheat Sheet**: https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- **kubectl Quick Reference**: https://kubernetes.io/docs/reference/kubectl/quick-reference/

---

*This guide is designed to grow with your Kubernetes journey. Keep experimenting, stay curious, and happy clustering!*
