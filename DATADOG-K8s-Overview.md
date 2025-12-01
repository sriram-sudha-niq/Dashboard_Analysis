# Data analysis

# Kubernetes overview

https://us5.datadoghq.com/dash/integration/kubernetes?fromUser=false&refresh_mode=sliding&from_ts=1764411786950&to_ts=1764415386950&live=true

![Kubernetes overview](./screenshots/image-30.png)
![Kubernetes overview2](./screenshots/image-31.png)
![Kubernetes overview3](./screenshots/image-32.png)

![Overview dashboard](./screenshots/image50.png)

### cluster

A Kubernetes cluster is the highest-level logical unit that contains all nodes, pods, namespaces, workloads, and services. It represents one complete Kubernetes environment.
Tracking the number of clusters is important because each cluster is an independent deployment boundary, billing unit, and operational environment. Monitoring cluster count ensures that Datadog is receiving data from all active clusters and helps detect if any cluster has stopped reporting metrics.

**Metric(s):**

- Metris used: kubernetes.pods.running

**query** :
`count_nonzero(avg:kubernetes.pods.running{*})`

**Type** : Guage

**Unit** : Count (Number of running pods per entity)
Datadog Aggregation Logic

**Datadog Aggregation Logic:**

- **avg**: takes the average of the pod running metric per host or per scope

- **count_nonzero**: counts how many entities return a non-zero value

- In this dashboard, each “cluster” is represented by at least one running pod, therefore counting non-zero values returns the number of clusters Datadog detects.

### Nodes

Nodes are the individual worker machines (VMs or EC2 instances) in a Kubernetes cluster where pods actually run. Monitoring node count ensures the cluster has enough capacity and helps detect scaling events, failures, or missing nodes.

**Metric(s):**

- Metrics used: `kubernetes_state.node.status` (implicitly through Datadog’s internal rollups)

**Query:**

```
count_nonzero(avg:kubernetes_state.node.status{status:ready})
```

**Type:** Gauge

**Unit:** Count (number of nodes reporting Ready=true)

**Datadog Aggregation Logic:**

- `avg:` normalizes node readiness values
- `count_nonzero:` counts how many nodes are reporting as ready
- Each ready node contributes a “1” value → aggregated into total node count

---

### Namespaces

Namespaces logically group Kubernetes resources within a cluster. They help separate environments, teams, or applications. Tracking namespace count helps understand cluster segmentation and workload organization.

**Metric(s):**

- Metrics used: `kubernetes_state.namespace.count`

**Query:**

```
count:kubernetes_state.namespace.count{*}
```

**Type:** Gauge

**Unit:** Count (Number of namespaces)

**Datadog Aggregation Logic:**

- `count:` sums all discovered namespaces
- Each namespace contributes a single value
- Total shows the number of active namespaces in the cluster

---

### DaemonSets

A DaemonSet ensures a specific pod runs on **every node** (or a selected group of nodes). This is used for agents, log collectors, monitoring, and networking components. Knowing total DaemonSets helps understand cluster-wide infrastructure workloads.

**Metric(s):**

- Metrics used: `kubernetes_state.daemonset.count`

**Query:**

```
count:kubernetes_state.daemonset.count{*}
```

**Type:** Gauge

**Unit:** Count (Number of daemonsets)

**Aggregation Logic:**

- Datadog counts all daemonsets discovered via kube-state-metrics
- Provides a global count of daemonset resources in the cluster

---

### Services

Services provide stable networking endpoints in Kubernetes and load-balance traffic to pods. Tracking service count helps understand how many applications are exposed inside or outside the cluster.

**Metric(s):**

- Metrics used: `kubernetes_state.service.count`

**Query:**

```
count:kubernetes_state.service.count{*}
```

**Type:** Gauge

**Unit:** Count (Number of services)

**Aggregation Logic:**

- Datadog sums every discovered service
- Useful for cluster topology understanding and monitoring changes

---

### Deployments

A Deployment manages replicas of stateless applications. It ensures rollout, rollback, and scaling. Monitoring deployment count indicates how many applications or microservices are active.

**Metric(s):**

- Metrics used: `kubernetes_state.deployment.count`

**Query:**

```
count:kubernetes_state.deployment.count{*}
```

**Type:** Gauge

**Unit:** Count (Number of deployments)

**Aggregation Logic:**

- Datadog counts all deployment resources
- Derived from kube-state-metrics
- Represents total workloads controlled by deployments

---

### Pods

Pods are the smallest Kubernetes execution units containing one or more containers. Tracking pod count shows total workload running in the cluster and is essential for capacity, scaling, and troubleshooting.

**Metric(s):**

- Metrics used: `kubernetes_state.pod.count`

**Query:**

```
sum:kubernetes_state.pod.count{*}
```

**Type:** Gauge

**Unit:** Count (Total number of pods)

**Aggregation Logic:**

- Sums all pods across all namespaces
- High pod count indicates high workload density
- Helps correlate with cluster resource consumption

---

### Containers

Containers represent actual application processes running inside pods. Container count gives a more detailed view of workload size and is used for resource tracking, billing, and capacity planning.

**Metric(s):**

- Metrics used: `kubernetes.containers.running`

**Query:**

```
sum:kubernetes.containers.running{*}
```

**Type:** Gauge

**Unit:** Count (Number of running containers)

**Aggregation Logic:**

- Datadog sums all running containers from kubelet
- Container count > pod count (because pods can have multiple containers)

---

### Kubelets Up

Kubelet is the primary agent running on every node responsible for pod lifecycle management. A kubelet must be healthy for the node to function. This widget shows how many kubelets (nodes) are alive.

**Metric(s):**

- Metrics used: `kubernetes.kubelet.check`

**Query:**

```
count_nonzero(max:kubernetes.kubelet.check{status:ok})
```

**Type:** Gauge

**Unit:** Count (Number of healthy kubelets)

**Aggregation Logic:**

- `kubernetes.kubelet.check` reports health
- `status:ok` → kubelet is functioning
- `count_nonzero` counts all healthy kubelets
- Should match node count if all nodes are up

---

### Kubelet Ping

This represents connectivity/heartbeat checks between Datadog agent and kubelet. If kubelet ping fails, node metrics become unreliable. It is an uptime indicator for the node agent.

**Metric(s):**

- Metrics used: `kubernetes.kubelet.ping`

**Query:**

```
count_nonzero(max:kubernetes.kubelet.ping{*})
```

**Type:** Gauge

**Unit:** Count (Number of responding kubelets)

**Aggregation Logic:**

- Each kubelet returns a ping metric
- Non-zero = responsive
- Count shows number of kubelets reachable by Datadog

![Pods](./screenshots/overview/image-80.png)

### **Ready state by node**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.ready`

**query** :

```
sum:kubernetes_state.pod.ready{*} by {node}
```

**Type** : Gauge

**Unit** : Count (Number of ready pods per node)

**Datadog Aggregation Logic:**

- **sum**: adds up all pods in Ready state for each node
- Grouping by **{node}** distributes ready pod counts across all nodes
- Helps determine node stability and workload health per node

---

### **Running pods per node**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.running`

**query** :

```
avg:kubernetes_state.pod.running{*} by {node}
```

**Type** : Gauge

**Unit** : Count (Number of running pods per node)

**Datadog Aggregation Logic:**

- **avg**: smooths the running pod count per node over time
- Grouping by **{node}** shows pod distribution patterns
- Helps detect overloaded or underutilized nodes

---

### **Running by namespace**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.running`

**query** :

```
sum:kubernetes_state.pod.running{*} by {kube_namespace}
```

**Type** : Gauge

**Unit** : Count (Number of running pods per namespace)

**Datadog Aggregation Logic:**

- **sum**: adds all running pods grouped by namespace
- Shows workload separation based on application or team
- Helps identify high-density namespaces

---

### **Running pods per namespace (time series)**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.running`

**query** :

```
avg:kubernetes_state.pod.running{*} by {kube_namespace}
```

**Type** : Gauge

**Unit** : Count (Running pods over time per namespace)

**Datadog Aggregation Logic:**

- **avg**: provides a smooth time-series of running pods
- Grouping by **{kube_namespace}** shows pod scaling trends
- Helps detect changes in workload patterns across namespaces

---

### **Failure by namespaces**

**Metric(s):**

- Metrics used:

  - `kubernetes_state.pod.status_phase` with phase: failed
  - `kubernetes_state.pod.status_phase` with phase: pending

**query** :
(Example used by Datadog)

Failed pods:

```
sum:kubernetes_state.pod.status_phase{phase:failed} by {kube_namespace}
```

Pending pods:

```
sum:kubernetes_state.pod.status_phase{phase:pending} by {kube_namespace}
```

**Type** : Gauge

**Unit** : Count (Pods in failed or pending states per namespace)

**Datadog Aggregation Logic:**

- **sum**: counts pods that are in failure or pending phases
- Grouping by namespace highlights problematic namespaces
- Used to detect failures or deployment issues

---

### **CrashLoopBackOff by Pod**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.container.waiting{reason:CrashLoopBackOff}`
  (depending on Datadog version)

**query** :

```
sum:kubernetes_state.pod.container.waiting{reason:CrashLoopBackOff} by {pod_name}
```

**Type** : Gauge

**Unit** : Count (Number of containers in CrashLoopBackOff per pod)

**Datadog Aggregation Logic:**

- **sum**: counts containers stuck in CrashLoopBackOff state
- Grouping by **{pod_name}** identifies specific failing pods
- Helps detect repeated crashes and application startup failures

![alt text](./screenshots/overview/image-81.png)

## **DaemonSets**

### **DaemonSets – Ready**

**Metric(s):**

- Metrics used: `kubernetes_state.daemonset.ready`

**query** :

```
sum:kubernetes_state.daemonset.ready{*}
```

**Type** : Gauge

**Unit** : Count (Number of ready DaemonSet pods)

**Datadog Aggregation Logic:**

- **sum**: adds all DaemonSet pods that are in _Ready_ state
- Shows how many DaemonSet-managed pods are currently available
- Used to verify cluster-wide DaemonSet health (e.g., agents, CNI pods)

---

### **DaemonSets – Desired**

**Metric(s):**

- Metrics used: `kubernetes_state.daemonset.desired`

**query** :

```
sum:kubernetes_state.daemonset.desired{*}
```

**Type** : Gauge

**Unit** : Count (Desired number of DaemonSet pods)

**Datadog Aggregation Logic:**

- **sum**: adds desired pod count for each DaemonSet
- Desired = number of nodes that _should_ be running the pod
- Difference between desired vs ready indicates rollout or failure

## Deployments

### **Deployments – Pods desired**

**Metric(s):**

- Metrics used: `kubernetes_state.deployment.replicas_desired`

**query** :

```
sum:kubernetes_state.deployment.replicas_desired{*}
```

**Type** : Gauge

**Unit** : Count (Desired replicas for deployments)

**Datadog Aggregation Logic:**

- **sum**: adds all desired replica counts across deployments
- Desired value represents the expected pod count per deployment
- Used to ensure scaling configuration is correct

### **Deployments – Pods available**

**Metric(s):**

- Metrics used: `kubernetes_state.deployment.replicas_available`

**query** :

```
sum:kubernetes_state.deployment.replicas_available{*}
```

**Type** : Gauge

**Unit** : Count (Available replicas)

**Datadog Aggregation Logic:**

- **sum**: adds replicas that are currently available across all deployments
- Shows readiness and health of application deployments
- Difference from desired indicates rollout or issues

### **Deployments – Pods unavailable**

**Metric(s):**

- Metrics used: `kubernetes_state.deployment.replicas_unavailable`

**query** :

```
sum:kubernetes_state.deployment.replicas_unavailable{*}
```

**Type** : Gauge

**Unit** : Count (Unavailable replicas)

**Datadog Aggregation Logic:**

- **sum**: counts pods that are expected but not available
- Helps detect deployment failures, rollouts, or scheduling problems

## ReplicaSets

### **ReplicaSets – Ready**

**Metric(s):**

- Metrics used: `kubernetes_state.replicaset.replicas_ready`

**query** :

```
sum:kubernetes_state.replicaset.replicas_ready{*}
```

**Type** : Gauge

**Unit** : Count (Number of ready ReplicaSet pods)

**Datadog Aggregation Logic:**

- **sum**: counts all pods in _Ready_ state from ReplicaSets
- Used to validate ReplicaSet health (parent of deployments)

---

### **ReplicaSets – Not Ready**

**Metric(s):**

- Metrics used: `kubernetes_state.replicaset.replicas_unready`

**query** :

```
sum:kubernetes_state.replicaset.replicas_unready{*}
```

**Type** : Gauge

**Unit** : Count (Pods not ready in ReplicaSets)

**Datadog Aggregation Logic:**

- **sum**: returns pods that are not yet ready
- Useful during rollouts, restarts, or failures

## Containers

### **Containers – Container States**

**Metric(s):**

- Metrics used: `kubernetes.containers.running`
  (plus internal Datadog container-status metrics depending on version)

**query** :

```
avg:kubernetes.containers.running{*}
```

**Type** : Gauge

**Unit** : Count (Number of running containers)

**Datadog Aggregation Logic:**

- **avg**: smooths container running count across the cluster
- Shows the total active container workload over time
- Useful for workload density and cluster usage patterns

## Resource utilization

![resource utilization](./screenshots/overview/image-82.png)

### **CPU utilization per node**

**Metric(s):**

- Metrics used: `system.cpu.user`, `system.cpu.system`, `system.cpu.idle`, combined internally by Datadog into a CPU utilization percentage

**query** :

```
avg:system.cpu.user{*} by {node}
+
avg:system.cpu.system{*} by {node}
```

(Displayed via Datadog’s infrastructure map visualization)

**Type** : Gauge

**Unit** : Percentage (% CPU utilization)

**Datadog Aggregation Logic:**

- **avg**: averages CPU usage metrics per node
- CPU utilization combines user + system CPU time
- The infrastructure map colors each node based on utilization
- Helps identify overloaded or underutilized nodes immediately

---

### **Sum Kubernetes CPU requests per node**

**Metric(s):**

- Metrics used: `kubernetes.cpu.requests`

**query** :

```
sum:kubernetes.cpu.requests{*} by {node}
```

**Type** : Gauge

**Unit** : Cores (CPU requests in cores)

**Datadog Aggregation Logic:**

- **sum**: adds all pod CPU _requested_ values per node
- Shows how much CPU is _requested_, not used
- Helps identify scheduling pressure or over-commit situations

---

### **Most CPU-intensive pods**

**Metric(s):**

- Metrics used: `container.cpu.usage`

**query** :

```
top(container.cpu.usage{*} by {pod_name}, 10)
```

(Exact query may vary but Datadog uses a **top list** function)

**Type** : Gauge

**Unit** : Millicores

**Datadog Aggregation Logic:**

- **top()**: sorts pods by CPU consumption, highest first
- **container.cpu.usage** reports real CPU usage (not requests)
- Helps find CPU hotspot pods or noisy neighbors quickly

---

### **Memory usage per node**

**Metric(s):**

- Metrics used: `system.mem.used`, `system.mem.total` (Datadog computes % or bytes)

**query** :

```
avg:system.mem.used{*} by {node}
```

(Display is a node-map heat visualization)

**Type** : Gauge

**Unit** : Bytes (or % depending on UI)

**Datadog Aggregation Logic:**

- **avg**: averages memory usage per node
- Infrastructure map shades nodes based on memory pressure
- Quickly shows which nodes are approaching memory limits

---

### **Sum Kubernetes memory requests per node**

**Metric(s):**

- Metrics used: `kubernetes.memory.requests`

**query** :

```
sum:kubernetes.memory.requests{*} by {node}
```

**Type** : Gauge

**Unit** : GiB (Gigabytes of memory requested)

**Datadog Aggregation Logic:**

- **sum**: adds all pod memory _requests_ on each node
- Shows the guaranteed memory resource reservations
- Helps identify nodes that are overcommitted

---

### **Most memory-intensive pods**

**Metric(s):**

- Metrics used: `container.memory.usage`

**query** :

```
top(container.memory.usage{*} by {pod_name}, 10)
```

**Type** : Gauge

**Unit** : GiB (Memory usage per pod)

**Datadog Aggregation Logic:**

- **top()**: selects top pods based on highest memory usage
- Real-time memory consumption per pod container
- Helps detect runaway pods or memory leaks

## Disk I/O & Network

![Disk I/O & Network](./screenshots/overview/image-83.png)

### **Network in per node**

**Metric(s):**

- Metrics used: `system.net.bytes_rcvd`

**query** :

```
avg:system.net.bytes_rcvd{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages incoming network bytes per node
- `bytes_rcvd` is converted to MB/s by Datadog UI
- Shows total incoming network traffic per node
- Helps identify nodes under heavy ingress or network load

### **Network out per node**

**Metric(s):**

- Metrics used: `system.net.bytes_sent`

**query** :

```
avg:system.net.bytes_sent{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages outgoing network bytes per node
- `bytes_sent` is converted to MB/s by Datadog
- Helps detect nodes generating heavy outbound traffic

### **Network errors per node**

**Metric(s):**

- Metrics used: `system.net.errors`

**query** :

```
sum:system.net.errors{*} by {host}
```

**Type** : Gauge

**Unit** : Errors/second

**Datadog Aggregation Logic:**

- **sum**: counts network interface errors per node
- Indicates packet drops, interface failures, or network congestion
- Useful to detect faulty networking hardware or CNI issues

---

### **Network errors per pod**

**Metric(s):**

- Metrics used: `kubernetes.network.errors`

**query** :

```
sum:kubernetes.network.errors{*} by {pod_name}
```

**Type** : Gauge

**Unit** : Errors/second

**Datadog Aggregation Logic:**

- **sum**: adds pod-level network errors
- Highlights pods experiencing packet loss or CNI-level failures
- Useful for diagnosing network instability at application level

---

### **Disk writes per node**

**Metric(s):**

- Metrics used: `system.disk.write_bytes`

**query** :

```
avg:system.disk.write_bytes{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages write-byte throughput per node
- Datadog converts bytes → MB/s in the UI
- Helps detect high disk write workloads (databases, logging, caching)

---

### **Disk reads per node**

**Metric(s):**

- Metrics used: `system.disk.read_bytes`

**query** :

```
avg:system.disk.read_bytes{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages read-byte throughput per node
- Shows disk read pressure
- Useful for identifying read-heavy workloads like analytics, ETL, or database queries
