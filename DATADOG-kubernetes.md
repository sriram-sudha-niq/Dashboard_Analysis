# DATADOG

## Infrastrutre

![Infrastrutre](./screenshots/image.png)

## kubernetes overview

https://us5.datadoghq.com/kubernetes
![kubernetes overview1](./screenshots/image-1.png)
![kubernetes overview2](./screenshots/image-2.png)
![kubernetes overview3](./screenshots/image-3.png)

## kubernetes overview sidebar

![sidebar1](./screenshots/image-26.png)
![sidebar 2](./screenshots/image-27.png)

## cluster overview

![cluster overview](./screenshots/image-4.png)

## explorer/cluster (On clicking the cluster)

https://us5.datadoghq.com/orchestration/explorer/cluster?explorer-na-groups=false&panel_tab=metrics&panel_view=pod&pte=1764416385187&ptfu=false&ptilt=false&ptm=sliding&pts=1764415485187&sp=%5B%7B%22p%22%3A%7B%22type%22%3A%22cluster%22%2C%22inspectedNodeId%22%3A%227b5eea8f-aed8-45f5-b263-936c34057443%22%7D%2C%22iid%22%3A%22cluster%237b5eea8f-aed8-45f5-b263-936c34057443%22%2C%22mp%22%3A%7B%22size%22%3A%22lg%22%7D%2C%22b%22%3A%22Back%22%2C%22i%22%3A%22orchestration-entity-panel%22%7D%5D

### cluster overview

![explorer/cluster](./screenshots/image-5.png)

### Log overview

![explorer/cluster/logs](./screenshots/image-6.png)

### Metrics

![explorer/cluster/metrics](./screenshots/image-5.png)

### Events

![explorer/cluster/events](./screenshots/image-7.png)

### explorer/cluster -> open remidiaction(top-right-btn)

![explorer/cluster -> open remidiaction(top-right-btn)](./screenshots/image-8.png)

<!-- ![remidiaction 1](./screenshots/image-9.png) -->

![remidiaction 2](./screenshots/image-10.png)
![remidiaction 3](./screenshots/image-11.png)

### explorer/cluster -> open remidiaction(top-right-btn) -> resource utilization

![explorer/cluster -> open remidiaction(top-right-btn) -> resource utilization](./screenshots/image-12.png)

### resource utilization -> options

![resource utilization -> options ](./screenshots/image-13.png)

### resource utilization -> related options

![resource utilization -> related options](./screenshots/image-14.png)

# Namspaces overview

![namspaces](./screenshots/image-15.png)

### namspaces -> yaml

![namspaces -> yaml](./screenshots/image-16.png)

# Nodes overview

![nodes](./screenshots/image-17.png)

### Nodes related resources

![nodes -> related resources](./screenshots/image-18.png)

## Deployments

![deployments](./screenshots/image-19.png)

### deployments -> explorer

![deployments -> explorer](./screenshots/image-20.png)

### deployments -> explorer -> resource_utilization

![deployments -> explorer -> resource_utilization](./screenshots/image-21.png)

# deployments -> explorer -> compare

![compare option](./screenshots/image-22.png)
![comparing 2 options](./screenshots/image-23.png)

# services

![services](./screenshots/image-24.png)
![services -> related data](./screenshots/image-25.png)

# Integrations -> kubernetes

### Another way to access the kubernetes

https://us5.datadoghq.com/integrations?category=Kubernetes&integrationId=kubernetes
![integrations -> kubernetes](./screenshots/image-28.png)

# Integrations -> kubernetes -> monitoring resources

![integrations -> kubernetes -> monitoring resources](./screenshots/image-29.png)

# Kubernetes overview

https://us5.datadoghq.com/dash/integration/kubernetes?fromUser=false&refresh_mode=sliding&from_ts=1764411786950&to_ts=1764415386950&live=true

![Kubernetes overview](./screenshots/image-30.png)
![Kubernetes overview2](./screenshots/image-31.png)
![Kubernetes overview3](./screenshots/image-32.png)

### Pods overview

https://us5.datadoghq.com/dash/integration/180/kubernetes-pods-overview?fromUser=false&refresh_mode=sliding&from_ts=1764418467937&to_ts=1764422067937&live=true
![Pods](./screenshots/image-33.png)

### Nodes overview

https://us5.datadoghq.com/dash/integration/200/kubernetes-nodes-overview?fromUser=false&refresh_mode=paused&from_ts=1764404245694&to_ts=1764404545694&live=false
![Nodes](./screenshots/image-34.png)
![Nodes2](./screenshots/image-35.png)

# Data analysis

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
