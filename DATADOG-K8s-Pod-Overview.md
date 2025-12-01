# Pods

Pods are the smallest Kubernetes execution units containing one or more containers. Tracking pod count shows total workload running in the cluster and is essential for capacity, scaling, and troubleshooting.

https://us5.datadoghq.com/dash/integration/180/kubernetes-pods-overview?fromUser=false&refresh_mode=sliding&from_ts=1764418467937&to_ts=1764422067937&live=true
![Pods](./screenshots/image-33.png)

## Overview

![alt text](./screenshots//pod-overview/image.png)

### **Pods Running**

**Metric(s):**

- Metrics used: `kubernetes.pods.running`

**query** :

```
sum:kubernetes.pods.running{*}
```

**Type** : Gauge (time series)

**Unit** : Count (Number of running pods)

**Description:** The number of running pods

---

### **Pods Running (changed weekly)**

**Metric(s):**

- Metrics used: `kubernetes.pods.running`

**query** :

```
week_before(sum:kubernetes.pods.running{*}), sum:kubernetes.pods.running{*}
```

(Note: Datadog uses an internal _change over time_ function)

**Type** : Gauge / Change indicator

**Unit** : Count difference over 1 week

**Description:** The number of running pods

---

### **Pods in Bad Phase by Namespaces**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.status_phase`

**query** :
Failed:

```
sum:kubernetes_state.pod.status_phase{!pod_phase:running,!pod_phase:succeeded}
```

**Type** : Gauge

**Unit** : Count (Number of bad-phase pods per namespace)

**Description:** The pods current phase.

**Tags:**`node` `kube_namespace` `pod_name` `pod_phase` (`env` `service` `version` from standard labels).

---

### **Pods running by Namespace**

**Metric(s):**

- Metrics used: `kubernetes.pods.running`

**query** :

```
sum:kubernetes.pods.running{*}
```

**Type** : Gauge (time series)

**Unit** : Count (Running pods per namespace)

**Description:** The number of running pods

## Pods

![alt text](./screenshots//pod-overview/image-1.png)

### **CPU-intensive pods**

**Metric(s):**

- Metrics used: `kubernetes.cpu.usage.total`

**query** :

```
sum:kubernetes.cpu.usage.total{!pod_name:no_pod}
```

**Type** : Gauge

**Unit** : nanocore

**Description:** The number of cores used

---

### **CPU Usage by Pod**

**Metric(s):**

- Metrics used: `kubernetes.cpu.usage.total`

**query** :

```
sum:kubernetes.cpu.usage.total{*}
```

**Type** : Gauge (time series)

**Unit** : nanocore

**Description:** The number of cores used

---

### **Memory-intensive pods**

**Metric(s):**

- Metrics used: `kubernetes.memory.usage`

**query** :

```
sum:kubernetes.memory.usage{!pod_name:no_pod}
```

**Type** : Gauge

**Unit** : byte

## **Description:** The amount of memory used

### **Memory Usage by Pod**

**Metric(s):**

- Metrics used: `kubernetes.memory.usage`

**query** :

```
sum:kubernetes.memory.usage{*}
```

**Type** : Gauge (time series)

**Unit** : byte

**Description:** The amount of memory used

---

### **Pods Running By Namespace**

**Metric(s):**

- Metrics used: `kubernetes.pods.running`

**query** :

```
sum:kubernetes.pods.running{*}
```

**Type** : Gauge

**Unit** : Count (Pods running per namespace)

**Description:** The number of running pods

---

### **Pods in Ready State By Node**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.ready`

**query** :

```
sum:kubernetes_state.pod.ready{condition:true}
```

**Type** : Gauge

**Unit** : Count

**Description:** Describes whether the pod is ready to serve requests.

**Tags:** `node` `kube_namespace` `pod_name` `condition` (`env` `service` `version` from standard labels).

---

### **Pods in Bad State by Namespace**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.ready`

**query** :

Failed:

```
exclude_null(sum:kubernetes_state.pod.ready{condition:false,!pod_phase:succeeded})
```

**Type** : Gauge

**Unit** : Count (Failed/pending pods per namespace)

**Description:** Describes whether the pod is ready to serve requests.

**Tags:**`node` `kube_namespace` `pod_name` `condition` (`env` `service` `version` from standard labels).

---

### **Pods Running By Runtime Class**

**Metric(s):**

- Metrics used: `kubernetes.pods.running`

**query** :

```
sum:kubernetes.pods.running{*}
```

**Type** : Gauge

**Unit** : Count

**Description:** The number of running pods

---

### **Pods Status Phase**

**Metric(s):**

- Metrics used: `kubernetes_state.pod.status_phase`

**query** :

```
sum:kubernetes_state.pod.status_phase{pod_phase:pending}
```

**Type** : Gauge (stacked bar chart)

**Unit** : Count (Pods per phase)

**Description:** The pods current phase. Tags:`node` `kube_namespace` `pod_name` `pod_phase` (`env` `service` `version` from standard labels).

## Container

![alt text](./screenshots//pod-overview/image-2.png)

### **Containers States**

**Metric(s):**

- Metrics used: `kubernetes_state.container.ready`

**query** :

```
sum:kubernetes_state.container.ready{*}
```

**Type** : Gauge (stacked time series)

**Unit** : Count (Containers by lifecycle state)

**Description:** Describes whether the containers readiness check succeeded.

**Tags:** `kube_namespace` `pod_name` `kube_container_name` (`env` `service` `version` from standard labels).

---

### **Containers OOM Killed (by Pod)**

**Metric(s):**

- Metrics used: `kubernetes.containers.state.terminated`

**query** :

```
sum:kubernetes.containers.state.terminated
```

**Type** : Gauge

**Unit** : Count (Number of OOMKilled containers per pod)

---

### **Containers in CrashLoopBackOff (by Pod)**

**Metric(s):**

- Metrics used:
  `kubernetes_state.container.status_report.count.waiting`

**query** :

```
sum:kubernetes_state.container.status_report.count.waiting{reason:crashloopbackoff}
```

**Type** : Gauge (time series)

**Unit** : Count (Containers stuck in CrashLoopBackOff)

**Description:** Describes the reason the container is currently in waiting state.

**Tags:**`kube_namespace` `pod_name` `kube_container_name` `reason` (`env` `service` `version` from standard labels).

---

### **Container Restarts by Pod**

**Metric(s):**

- Metrics used: `kubernetes_state.container.restarts`

**query** :

```
sum:kubernetes_state.container.restarts{*}
```

**Type** : Gauge

**Unit** : Count (Total number of container restarts)

**Description:** The number of container restarts per container.

**Tags:**`kube_namespace` `pod_name` `kube_container_name` (`env` `service` `version` from standard labels).

---

### **CPU Usage by Container**

**Metric(s):**

- Metrics used: `kubernetes.cpu.usage.total`

**query** :

```
sum:kubernetes.cpu.usage.total{*}
```

**Type** : Gauge (time series)

**Unit** : nanocore

**Description:** The number of cores used

---

### **Memory Usage by Container**

**Metric(s):**

- Metrics used: `kubernetes.memory.usage`

**query** :

```
sum:kubernetes.memory.usage{*}
```

**Type** : Gauge (time series)

**Unit** : byte

Description: The amount of memory used

---

### **Network Rate by Pod**

**Metric(s):**

- Metrics used: `kubernetes.network.rx_bytes`

**query** :

```
sum:kubernetes.network.rx_bytes{*}
```

**Type** : Gauge (time series)

**Unit** : Bytes/second

**Description:** The amount of bytes per second received

---

### **Network Errors by Pod**

**Metric(s):**

- Metrics used: `kubernetes.network.rx_errors`

**query** :

```
sum:kubernetes.network.rx_errors{*}

```

**Type** : Gauge

**Unit** : Errors/second

**Description:** The amount of rx errors per second
