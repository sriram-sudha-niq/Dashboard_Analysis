# Nodes

https://us5.datadoghq.com/dash/integration/200/kubernetes-nodes-overview?fromUser=false&refresh_mode=paused&from_ts=1764404245694&to_ts=1764404545694&live=false
![Nodes](./screenshots/image-34.png)
![Nodes2](./screenshots/image-35.png)

## Overview

![alt text](./screenshots/node-overview/image.png)

Below is the **Nodes Overview** documentation written in the **exact same style and formatting** you’ve been using.

You can paste this directly into your analysis document.

---

### **Number of nodes**

**Metric(s):**

- Metrics used: `kubernetes_state.node.status{status:ready}`

**query** :

```
count_nonzero(max:kubernetes_state.node.status{status:ready})
```

**Type** : Gauge

**Unit** : Count (Number of Ready nodes)

**Datadog Aggregation Logic:**

- **max**: returns 1 if node is Ready, 0 if not
- **count_nonzero**: counts all nodes reporting Ready status
- Represents the number of active, healthy nodes in the cluster

---

### **Total CPU**

**Metric(s):**

- Metrics used: `kubernetes.cpu.capacity`

**query** :

```
sum:kubernetes.cpu.capacity{*}
```

**Type** : Gauge

**Unit** : Cores (Total CPU capacity across all nodes)

**Datadog Aggregation Logic:**

- **sum**: adds total CPU capacity (in cores) from each node
- Shows total available compute power in the entire cluster

---

### **Nodes not ready**

**Metric(s):**

- Metrics used: `kubernetes_state.node.status{status:not_ready}`

**query** :

```
sum:kubernetes_state.node.status{status:not_ready}
```

**Type** : Gauge

**Unit** : Count (Nodes in NotReady state)

**Datadog Aggregation Logic:**

- **sum**: counts all nodes marked in NotReady
- This widget shows “No data” when all nodes are healthy

---

### **Total memory**

**Metric(s):**

- Metrics used: `kubernetes.memory.capacity`

**query** :

```
sum:kubernetes.memory.capacity{*}
```

**Type** : Gauge

**Unit** : Gigabytes (GiB) of total cluster memory

**Datadog Aggregation Logic:**

- **sum**: aggregates total memory capacity from all nodes
- Represents the entire cluster’s memory availability

---

### **Memory utilization % per node**

**Metric(s):**

- Metrics used:

  - `system.mem.used`
  - `system.mem.total`

Datadog computes memory % internally.

**query** :

```
100 * avg:system.mem.used{*} by {host} / avg:system.mem.total{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Percentage (% memory utilization per node)

**Datadog Aggregation Logic:**

- **avg**: averages memory usage and total memory per node
- Datadog divides used over total to calculate percentage
- Shows how much RAM each node is consuming

---

### **CPU % utilization per node**

**Metric(s):**

- Metrics used:

  - `system.cpu.user`
  - `system.cpu.system`
  - `system.cpu.idle`

Datadog computes CPU % internally.

**query** :

```
100 - avg:system.cpu.idle{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Percentage (% CPU usage per node)

**Datadog Aggregation Logic:**

- CPU utilization = `100 - idle%`
- **avg**: smooths CPU usage per node over the time window
- Helps identify nodes under heavy compute load

---

### **Kubelets up**

**Metric(s):**

- Metrics used: `kubernetes.kubelet.check{status:ok}`

**query** :

```
count_nonzero(max:kubernetes.kubelet.check{status:ok})
```

**Type** : Gauge

**Unit** : Count (Number of healthy kubelets)

**Datadog Aggregation Logic:**

- **max**: identifies whether each kubelet is healthy
- **count_nonzero**: counts all kubelets reporting healthy status
- Should match number of nodes if every node is healthy

---

### **Kubelet Ping**

**Metric(s):**

- Metrics used: `kubernetes.kubelet.ping`

**query** :

```
count_nonzero(max:kubernetes.kubelet.ping{*})
```

**Type** : Gauge

**Unit** : Count (Number of nodes responding to ping)

**Datadog Aggregation Logic:**

- **max**: returns non-zero if ping is successful
- **count_nonzero**: counts responding kubelet agents
- Indicates whether Datadog can reach kubelet on every node

---

## Node Condition

![alt text](./screenshots/node-overview/image-1.png)

### **Node by condition**

**Metric(s):**

- Metrics used: `kubernetes_state.node.status`

**query** :

```
sum:kubernetes_state.node.status{status:ready}
```

**Type** : Gauge

**Unit** : Count (Number of nodes matching the condition)

**Datadog Aggregation Logic:**

- **sum**: counts nodes whose condition is marked as `ready`
- The dashboard shows “42 ready” → meaning 42 nodes are in Ready state
- This represents the health of all nodes in the cluster

---

### **Ready**

**Metric(s):**

- Metrics used: `kubernetes_state.node.status{status:ready}`

**query** :

```
sum:kubernetes_state.node.status{status:ready}
```

**Type** : Gauge (time series)

**Unit** : Count (Nodes in Ready state)

**Datadog Aggregation Logic:**

- **sum**: adds all nodes reporting the Ready condition
- Time series shows stability or fluctuations in node readiness
- A flat horizontal line indicates stable node health

---

### **Not Ready**

**Metric(s):**

- Metrics used: `kubernetes_state.node.status{status:not_ready}`

**query** :

```
sum:kubernetes_state.node.status{status:not_ready}
```

**Type** : Gauge (time series)

**Unit** : Count (Nodes not Ready)

**Datadog Aggregation Logic:**

- **sum**: counts nodes not reporting Ready status
- Absence of values (y = 0) means **all nodes are healthy**
- Useful for detecting node failures, crashes, or network isolation

---

## Node Utilization

![alt text](./screenshots/node-overview/image-2.png)

### **CPU utilization per node**

**Metric(s):**

- Metrics used:

  - `system.cpu.user`
  - `system.cpu.system`
  - `system.cpu.idle`
    (Datadog internally computes CPU utilization %)

**query** :

```
100 - avg:system.cpu.idle{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Percentage (% CPU utilization per node)

**Datadog Aggregation Logic:**

- **avg**: averages CPU idle time per node
- Datadog converts idle% → CPU utilization% (100 - idle%)
- Shows actual CPU load on each node
- Used to detect nodes under high processing pressure

---

### **Memory utilization % per node**

**Metric(s):**

- Metrics used:

  - `system.mem.used`
  - `system.mem.total`

**query** :

```
100 * avg:system.mem.used{*} by {host} / avg:system.mem.total{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Percentage (% memory utilization)

**Datadog Aggregation Logic:**

- **avg**: averages memory usage and capacity per node
- Datadog computes utilization% internally
- Helps identify nodes close to memory saturation

---

### **CPU usage per node**

**Metric(s):**

- Metrics used: `container.cpu.usage` (summed per node)

**query** :

```
avg:container.cpu.usage{kube_node:*} by {kube_node}
```

**Type** : Gauge (time series)

**Unit** : Cores

**Datadog Aggregation Logic:**

- **avg**: averages CPU usage of all containers on each node
- Shows how much CPU is actually consumed (not requested)
- Useful for comparing real utilization vs. node capacity

---

### **Memory usage per node**

**Metric(s):**

- Metrics used: `container.memory.usage`

**query** :

```
avg:container.memory.usage{kube_node:*} by {kube_node}
```

**Type** : Gauge (time series)

**Unit** : GiB

**Datadog Aggregation Logic:**

- **avg**: averages container-level memory usage per node
- Shows real memory used by workloads
- Useful for spotting memory-heavy nodes or memory leaks

---

### **CPU requests per node**

**Metric(s):**

- Metrics used: `kubernetes.cpu.requests`

**query** :

```
sum:kubernetes.cpu.requests{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : Cores

**Datadog Aggregation Logic:**

- **sum**: sums requested CPU of all pods scheduled on a node
- Shows how much CPU is reserved (not necessarily used)
- Helps detect scheduling pressure and overcommitment

---

### **Memory requests per node**

**Metric(s):**

- Metrics used: `kubernetes.memory.requests`

**query** :

```
sum:kubernetes.memory.requests{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : GiB

**Datadog Aggregation Logic:**

- **sum**: totals memory requested by pods on each node
- Shows guaranteed memory reservation for workloads
- Useful for detecting nodes reaching request saturation

---

### **CPU requests / capacity ratio per node**

**Metric(s):**

- Metrics used:

  - `kubernetes.cpu.requests`
  - `kubernetes.cpu.capacity`

**query** :

```
100 * (sum:kubernetes.cpu.requests{*} by {node} / sum:kubernetes.cpu.capacity{*} by {node})
```

**Type** : Gauge (time series)

**Unit** : Percentage (% of CPU requests vs node capacity)

**Datadog Aggregation Logic:**

- **sum**: aggregates CPU requests and capacity per node
- Datadog computes request-to-capacity % = (requests / capacity) × 100
- Helps identify overcommitted nodes
- Values > 100% show CPU over-provisioning

---

## CLuster utilization

![alt text](./screenshots/node-overview/image-3.png)

### **CPU requests / capacity ratio per node**

**Metric(s):**

- Metrics used:

  - `kubernetes.cpu.requests`
  - `kubernetes.cpu.capacity`

**query** :

```
100 * (sum:kubernetes.cpu.requests{*} by {node} / sum:kubernetes.cpu.capacity{*} by {node})
```

**Type** : Gauge (time series)

**Unit** : Percentage (% CPU requests compared to node CPU capacity)

**Datadog Aggregation Logic:**

- **sum**: aggregates CPU requests and CPU capacity per node
- Datadog divides total CPU requests by total node CPU capacity
- Multiplies by 100 to convert to %
- Shows how much of each node’s CPU is reserved
- Useful for detecting scheduling pressure or CPU overcommitment

---

### **Filesystem utilization per cluster**

**Metric(s):**

- Metrics used:

  - `system.fs.used`
  - `system.fs.total`

**query** :

```
100 * (sum:system.fs.used{*} by {kube_cluster_name} / sum:system.fs.total{*} by {kube_cluster_name})
```

**Type** : Gauge (time series)

**Unit** : Percentage (% filesystem utilization)

**Datadog Aggregation Logic:**

- **sum**: aggregates filesystem usage across all nodes in the cluster
- Datadog computes utilization = (used / total) × 100
- Helps identify clusters with disk pressure or approaching storage limits

---

### **Memory utilization per cluster**

**Metric(s):**

- Metrics used:

  - `system.mem.used`
  - `system.mem.total`

**query** :

```
100 * (sum:system.mem.used{*} by {kube_cluster_name} / sum:system.mem.total{*} by {kube_cluster_name})
```

**Type** : Gauge (time series)

**Unit** : Percentage (% of memory used in cluster)

**Datadog Aggregation Logic:**

- **sum**: totals memory usage and total memory across nodes
- Datadog computes a cluster-level memory utilization percentage
- Useful for cluster-wide capacity planning
- Detects memory saturation risks

---

### **CPU utilization per cluster**

**Metric(s):**

- Metrics used:

  - `system.cpu.user`
  - `system.cpu.system`
  - `system.cpu.idle`

Datadog internally calculates CPU utilization.

**query** :

```
100 - avg:system.cpu.idle{*} by {kube_cluster_name}
```

**Type** : Gauge (time series)

**Unit** : Percentage (% CPU usage across the entire cluster)

**Datadog Aggregation Logic:**

- **avg**: averages idle CPU time across all nodes
- CPU utilization = 100 - idle%
- Shows overall cluster CPU consumption
- Helps identify cluster-wide compute pressure

## Pod and Containers

![alt text](./screenshots/node-overview/image-4.png)

### **Allocatable memory per node**

**Metric(s):**

- Metrics used: `kubernetes.memory.allocatable`

**query** :

```
avg:kubernetes.memory.allocatable{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : GiB (Allocatable memory per node)

**Datadog Aggregation Logic:**

- **avg**: returns allocatable memory reported by each node
- Allocatable memory = memory available to pods after reserving kubelet/system overhead
- Used to understand real scheduling capacity per node

---

### **Pods per node**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.count`

**query** :

```
sum:kubernetes_state.pod.count{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : Count (Number of pods running on each node)

**Datadog Aggregation Logic:**

- **sum**: counts all pods assigned to each node
- Shows scheduling distribution and node load
- Helps detect pod imbalance or over-scheduling on specific nodes

---

### **Ready pods per node**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.ready`

**query** :

```
sum:kubernetes_state.pod.ready{*} by {node}
```

**Type** : Gauge

**Unit** : Count (Pods in Ready state per node)

**Datadog Aggregation Logic:**

- **sum**: counts pods that report Ready condition on each node
- Helps evaluate node health and stability
- A low number on a node may indicate pod issues or node pressure

---

### **Containers per node**

**Metric(s):**

- Metrics used: `kubernetes.containers.running`

**query** :

```
sum:kubernetes.containers.running{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : Count (Running containers per node)

**Datadog Aggregation Logic:**

- **sum**: counts active containers per node
- Helps understand container density and workload pressure
- A higher value often correlates with more pods on the node

## Events and Certificates

![alt text](./screenshots/node-overview/image-5.png)

### **Events per node**

**Metric(s):**

- Metrics used: `kubernetes_state.event.count`

**query** :

```
sum:kubernetes_state.event.count{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : Count (Number of Kubernetes events per node)

**Datadog Aggregation Logic:**

- **sum**: counts all Kubernetes events originating from each node
- Events include pod failures, scheduling issues, restarts, eviction warnings, etc.
- Used to detect noisy nodes, instability, or node-level error patterns

---

### **Event logs per node**

**Metric(s):**

- Event table is not based on a single metric; it is pulled from:

  - `kubernetes_state.event.*`
  - Datadog Kubernetes Integration event stream
  - Sources: Pods, DaemonSets, ReplicaSets, Deployments, Nodes, etc.

**query** :

```
kubernetes_state.event.* (Event Stream)
```

**Type** : Log/Event Stream Table

**Unit** : N/A (Events are textual records)

**Datadog Aggregation Logic:**

- Datadog collects Kubernetes events from the API server
- Groups them by **source**, **object**, **node**, and **timestamp**
- Displays events such as:

  - Pod scheduling errors
  - CrashLoopBackOff events
  - ReplicaSet scale events
  - Node pressure warnings
  - Certificate issues

- Helps debug cluster issues in real-time

---

### **Kubelet certificates expirations (<1 hour left)**

**Metric(s):**

- Metrics used: `kubernetes.kubelet.certificate.expiration`

**query** :

```
min:kubernetes.kubelet.certificate.expiration{*} by {node}
```

**Type** : Gauge (alert table)

**Unit** : Seconds or Timestamp (time until certificate expires)

**Datadog Aggregation Logic:**

- **min**: shows the lowest remaining certificates lifetime per node
- Datadog flags certificates expiring in **< 1 hour**
- Used for security posture and preventing kubelet authentication failures
- If no data appears, certificates are healthy and not nearing expiration

---

## Allocatable Resources per Node

![alt text](./screenshots/node-overview/image-6.png)

### **Events per node**

**Metric(s):**

- Metrics used: `kubernetes_state.event.count`

**query** :

```
sum:kubernetes_state.event.count{*} by {node}
```

**Type** : Gauge (time series)

**Unit** : Count (Number of Kubernetes events per node)

**Datadog Aggregation Logic:**

- **sum**: counts all Kubernetes events originating from each node
- Events include pod failures, scheduling issues, restarts, eviction warnings, etc.
- Used to detect noisy nodes, instability, or node-level error patterns

---

### **Event logs per node**

**Metric(s):**

- Event table is not based on a single metric; it is pulled from:

  - `kubernetes_state.event.*`
  - Datadog Kubernetes Integration event stream
  - Sources: Pods, DaemonSets, ReplicaSets, Deployments, Nodes, etc.

**query** :

```
kubernetes_state.event.* (Event Stream)
```

**Type** : Log/Event Stream Table

**Unit** : N/A (Events are textual records)

**Datadog Aggregation Logic:**

- Datadog collects Kubernetes events from the API server
- Groups them by **source**, **object**, **node**, and **timestamp**
- Displays events such as:

  - Pod scheduling errors
  - CrashLoopBackOff events
  - ReplicaSet scale events
  - Node pressure warnings
  - Certificate issues

- Helps debug cluster issues in real-time

---

### **Kubelet certificates expirations (<1 hour left)**

**Metric(s):**

- Metrics used: `kubernetes.kubelet.certificate.expiration`

**query** :

```
min:kubernetes.kubelet.certificate.expiration{*} by {node}
```

**Type** : Gauge (alert table)

**Unit** : Seconds or Timestamp (time until certificate expires)

**Datadog Aggregation Logic:**

- **min**: shows the lowest remaining certificates lifetime per node
- Datadog flags certificates expiring in **< 1 hour**
- Used for security posture and preventing kubelet authentication failures
- If no data appears, certificates are healthy and not nearing expiration

---

## Disk I/O & Network

![alt text](./screenshots/node-overview/image-7.png)

### **Network in per node**

**Metric(s):**

- Metrics used:

  - `system.net.bytes_rcvd`

**query** :

```
avg:system.net.bytes_rcvd{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second (Datadog converts bytes → MB/s)

**Datadog Aggregation Logic:**

- **avg**: averages inbound network traffic per node
- Measures how much data each node is receiving
- Useful for identifying nodes handling heavy ingress traffic
- Helps detect overloaded network interfaces or hotspots

---

### **Network out per node**

**Metric(s):**

- Metrics used:

  - `system.net.bytes_sent`

**query** :

```
avg:system.net.bytes_sent{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages outbound network traffic
- Shows how much data each node is sending out
- Useful for identifying:

  - nodes sending logs
  - nodes handling egress traffic
  - network congestion issues

---

### **Disk reads per node**

**Metric(s):**

- Metrics used:

  - `system.io.rbytes`

**query** :

```
avg:system.io.rbytes{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Kilobytes/second or Megabytes/second (Datadog auto-scales)

**Datadog Aggregation Logic:**

- **avg**: averages disk read bytes per second
- Shows how much data is being read from disk on each node
- Helps detect:

  - I/O-bound workloads
  - slow disks or overloaded storage

---

### **Disk writes per node**

**Metric(s):**

- Metrics used:

  - `system.io.wbytes`

**query** :

```
avg:system.io.wbytes{*} by {host}
```

**Type** : Gauge (time series)

**Unit** : Kilobytes/second or Megabytes/second

**Datadog Aggregation Logic:**

- **avg**: averages disk write throughput
- Shows how much data is being written to disk
- Helps detect:

  - log-heavy workloads
  - applications generating large write bursts
  - potential disk throttling or disk pressure

---
