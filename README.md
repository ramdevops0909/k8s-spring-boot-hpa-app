#Kubernetes Spring boot Web Application with Horizontal Pod Autoscaler which expose the Http Custom Metrics on Google Cloud Cluster Environment


This repo contains, Autoscaling of Spring boot Web Application (spring-boot-k8s-hpa-0.0.1-SNAPSHOT.jar) with Horizontal Pod Autoscaler which exposes the Http Custom matrics in Kubernetes Google cloud cluster environment.

In order to achieve this requirement, I have performed following things

	1) Installed Kubernetes Cluster on Google Cloud Environment
	2) Installed `jq` ,It is a lightweight and flexible command-line JSON processor.
	3) Installed the Custom Metrics API
	4) Dockrized the Spring Boot WebApplication
	5) Deployed the Spring Boot WebApplication in Kubernetes Cluster Environment
	5) Pods are getting Spinup during more traffic

# Below are the Installation Steps:
	
## Installing the Custom Metrics API

Clone the Custom Metrics from the Git Repo
     git clone <<URL>>

Deploy the Metrics Server in the `kube-system` namespace:

```bash
kubectl create -f monitoring/metrics-server

Output:
-------
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
serviceaccount/metrics-server created
deployment.extensions/metrics-server created
service/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created

```

After Deployment, metric-server (wait for some time) would starts reporting CPU and memory usage for nodes and pods.

Below  is the command to View nodes metrics:

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes" | jq .

Output:
------
{
  "kind": "NodeMetricsList",
  "apiVersion": "metrics.k8s.io/v1beta1",
  "metadata": {
    "selfLink": "/apis/metrics.k8s.io/v1beta1/nodes"
  },
  "items": []
}

```

Below is the command to View pods metrics:

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/pods" | jq .

Output:
------
{
  "kind": "PodMetricsList",
  "apiVersion": "metrics.k8s.io/v1beta1",
  "metadata": {
    "selfLink": "/apis/metrics.k8s.io/v1beta1/pods"
  },
  "items": []
}

```

Create the monitoring namespace:

```bash
kubectl create -f monitoring/namespaces.yaml

Output:
-------
namespace/monitoring created

```

Deploying Prometheus v2 in the monitoring namespace:

```bash
kubectl create -f monitoring/prometheus

Output:
-------

configmap/prometheus-config created
deployment.apps/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
serviceaccount/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created
service/prometheus created

```

Deploying the Prometheus custom metrics API adapter:

```bash
kubectl create -f monitoring/custom-metrics-api

Output:
-------
secret/cm-adapter-serving-certs created
clusterrolebinding.rbac.authorization.k8s.io/custom-metrics:system:auth-delegator created
rolebinding.rbac.authorization.k8s.io/custom-metrics-auth-reader created
deployment.extensions/custom-metrics-apiserver created
clusterrolebinding.rbac.authorization.k8s.io/custom-metrics-resource-reader created
serviceaccount/custom-metrics-apiserver created
service/custom-metrics-apiserver created
apiservice.apiregistration.k8s.io/v1beta1.custom.metrics.k8s.io created
clusterrole.rbac.authorization.k8s.io/custom-metrics-server-resources created
clusterrole.rbac.authorization.k8s.io/custom-metrics-resource-reader created
clusterrolebinding.rbac.authorization.k8s.io/hpa-controller-custom-metrics created

```

List the custom metrics provided by Prometheus:

```bash
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1" | jq .

Output:
-------

{
  "kind": "APIResourceList",
  "apiVersion": "v1",
  "groupVersion": "custom.metrics.k8s.io/v1beta1",
  "resources": [
    {
      "name": "pods/memory_failures",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_rss",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_working_set_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "jobs.batch/scrape_samples_scraped",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_io_current",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_usage_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/up",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_reads_merged",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_cpu_period",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_memory_swap_limit_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/kubelet_container_log_filesystem_used_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "jobs.batch/scrape_duration_seconds",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_swap",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/start_time_seconds",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "jobs.batch/kubelet_container_log_filesystem_used_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "namespaces/up",
      "singularName": "",
      "namespaced": false,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_memory_reservation_limit_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/tasks_state",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_cfs_throttled_periods",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_cfs_throttled",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_io_time",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_limit_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_write",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_writes_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "namespaces/scrape_samples_scraped",
      "singularName": "",
      "namespaced": false,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_cfs_periods",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_cpu_quota",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/scrape_samples_post_metric_relabeling",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "namespaces/scrape_duration_seconds",
      "singularName": "",
      "namespaced": false,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/scrape_duration_seconds",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_user",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_io_time_weighted",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/last_seen",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_mapped_file",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "namespaces/kubelet_container_log_filesystem_used_bytes",
      "singularName": "",
      "namespaced": false,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_inodes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_reads",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_usage_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_writes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_usage",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_max_usage_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_reads_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_sector_reads",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_failcnt",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_load_average_10s",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_inodes_free",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_read",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/memory_cache",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/scrape_samples_scraped",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/cpu_system",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_sector_writes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/fs_writes_merged",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "jobs.batch/scrape_samples_post_metric_relabeling",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "namespaces/scrape_samples_post_metric_relabeling",
      "singularName": "",
      "namespaced": false,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_cpu_shares",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "pods/spec_memory_limit_bytes",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    },
    {
      "name": "jobs.batch/up",
      "singularName": "",
      "namespaced": true,
      "kind": "MetricValueList",
      "verbs": [
        "get"
      ]
    }
  ]
}

```

Get the FS usage for all the pods in the `monitoring` namespace:

```bash
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/monitoring/pods/*/fs_usage_bytes" | jq .

{
  "kind": "MetricValueList",
  "apiVersion": "custom.metrics.k8s.io/v1beta1",
  "metadata": {
    "selfLink": "/apis/custom.metrics.k8s.io/v1beta1/namespaces/monitoring/pods/%2A/fs_usage_bytes"
  },
  "items": [
    {
      "describedObject": {
        "kind": "Pod",
        "namespace": "monitoring",
        "name": "custom-metrics-apiserver-5d4ddcf68c-sh6pr",
        "apiVersion": "/__internal"
      },
      "metricName": "fs_usage_bytes",
      "timestamp": "2019-08-14T20:10:36Z",
      "value": "2715648"
    },
    {
      "describedObject": {
        "kind": "Pod",
        "namespace": "monitoring",
        "name": "prometheus-7bc5b8567f-zs244",
        "apiVersion": "/__internal"
      },
      "metricName": "fs_usage_bytes",
      "timestamp": "2019-08-14T20:10:36Z",
      "value": "53248"
    }
  ]
}

```

## Dockrized the Spring Boot WebApplication

```bash
Output:
-------
docker build -t ramdevops0909/k8s-spring-boot-hpa .
Sending build context to Docker daemon  298.5kB
Step 1/12 : FROM maven:3.5.3-jdk-10-slim as build
 ---> 276091e24d4f
Step 2/12 : WORKDIR /app
 ---> Using cache
 ---> bf8f24935bf3
Step 3/12 : COPY pom.xml .
 ---> Using cache
 ---> b9539534e742
Step 4/12 : COPY src src
 ---> Using cache
 ---> de9742d3edd1
Step 5/12 : RUN mvn package -q -Dmaven.test.skip=true
 ---> Using cache
 ---> 8bacdbbb4faf
Step 6/12 : FROM openjdk:10.0.1-10-jre-slim
 ---> 6cf6acb97a09
Step 7/12 : WORKDIR /app
 ---> Using cache
 ---> 77a10dd3f85c
Step 8/12 : EXPOSE 8080
 ---> Using cache
 ---> 8a22cd391b83
Step 9/12 : ENV STORE_ENABLED=true
 ---> Using cache
 ---> 47d1ae63401b
Step 10/12 : ENV WORKER_ENABLED=true
 ---> Using cache
 ---> c6ea94e0a481
Step 11/12 : COPY --from=build /app/target/spring-boot-k8s-hpa-0.0.1-SNAPSHOT.jar /app
 ---> Using cache
 ---> b00e7f534e48
Step 12/12 : CMD ["java", "-jar", "spring-boot-k8s-hpa-0.0.1-SNAPSHOT.jar"]
 ---> Using cache
 ---> 86c8d321e8d0
Successfully built 86c8d321e8d0
Successfully tagged ramdevops0909/k8s-spring-boot-hpa:latest

Tag the docker Image:
---------------------

docker tag 86c8d321e8d0 ramdevops0909/k8s-spring-boot-hpa:1

Push the docker Image
----------------------

docker push ramdevops0909/k8s-spring-boot-hpa:1
The push refers to repository [docker.io/ramdevops0909/k8s-spring-boot-hpa]
b4af25ce3f55: Pushed
d2f05a081b92: Pushed
3cc3678be92d: Mounted from library/openjdk
83d1f6d95864: Mounted from library/openjdk
3e18aac66b64: Mounted from library/openjdk
286a2d74861e: Mounted from library/openjdk
d8fd35cfa5d6: Mounted from library/openjdk
1: digest: sha256:d4f7a40a4b53fd6a274490da3478296c4e6d33861ac3e67a85e60cac38381280 size: 1784

```

## Deploying the application

Deploy the application in Kubernetes with:

```bash
kubectl create -f k8s-hpa-service/deployment

Below is the K8s deployment horizontal pod autoscaler service:

k8s-deployment-hpa-service.yaml

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: ramdevops0909/k8s-spring-boot-hpa:1
        imagePullPolicy: IfNotPresent
        env:
        - name: ACTIVEMQ_BROKER_URL
          value: "tcp://queue:61616"
        - name: STORE_ENABLED
          value: "true"
        - name: WORKER_ENABLED
          value: "false"
        ports:
          - containerPort: 8080
        readinessProbe:
          initialDelaySeconds: 5
          periodSeconds: 5
          httpGet:
            path: /health
            port: 8080
        resources:
          limits:
            memory: 512Mi
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  ports:
  - nodePort: 32000
    port: 80
    targetPort: 8080
  selector:
    app: frontend
  type: NodePort

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: backend
      annotations:
        prometheus.io/scrape: 'true'
    spec:
      containers:
      - name: backend
        image: ramdevops0909/k8s-spring-boot-hpa:1
        imagePullPolicy: IfNotPresent
        env:
        - name: ACTIVEMQ_BROKER_URL
          value: "tcp://queue:61616"
        - name: STORE_ENABLED
          value: "false"
        - name: WORKER_ENABLED
          value: "true"
        ports:
          - containerPort: 8080
        readinessProbe:
          initialDelaySeconds: 5
          periodSeconds: 5
          httpGet:
            path: /health
            port: 8080
        resources:
          limits:
            memory: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  ports:
  - nodePort: 31000
    port: 80
    targetPort: 8080
  selector:
    app: backend
  type: NodePort

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: queue
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: queue
    spec:
      containers:
      - name: web
        image: webcenter/activemq:5.14.3
        imagePullPolicy: IfNotPresent
        ports:
          - containerPort: 61616
        resources:
          limits:
            memory: 512Mi
---
apiVersion: v1
kind: Service
metadata:
  name: queue
spec:
  ports:
  - port: 61616
    targetPort: 61616
  selector:
    app: queue
  
```
Accessing the Application:
-------------------------

I tried to access the Application using Node port. In order to access the application using NodePort from out side the Kubernetes cluster, I have enabeld the the firewall for the perticular port using below command

C:\Program Files (x86)\Google\Cloud SDK>gcloud compute firewall-rules create my-rule-k8s --allow=tcp:32000
Creating firewall...\Created [https://www.googleapis.com/compute/v1/projects/cohesive-ridge-226016/global/firewalls/my-rule-k8s].
Creating firewall...done.
NAME         NETWORK  DIRECTION  PRIORITY  ALLOW      DENY  DISABLED
my-rule-k8s  default  INGRESS    1000      tcp:32000        False

Once enabled port (32000) ,I was able to access the application using this URL (http://<<Nodes_PublicIp>>:32000)


You can post messages to the queue by via http://<<Nodes_PublicIp>>:32000/submit?quantity=2

You should be able to see the number of pending messages from http://<<Nodes_PublicIp>>:32000/metrics and from the custom metrics endpoint:

```bash
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/*/messages" | jq .

Output:
-------

{
  "kind": "MetricValueList",
  "apiVersion": "custom.metrics.k8s.io/v1beta1",
  "metadata": {
    "selfLink": "/apis/custom.metrics.k8s.io/v1beta1/namespaces/default/pods/%2A/messages"
  },
  "items": [
    {
      "describedObject": {
        "kind": "Pod",
        "namespace": "default",
        "name": "backend-54db7469bf-8mqx8",
        "apiVersion": "/__internal"
      },
      "metricName": "messages",
      "timestamp": "2019-08-14T20:50:10Z",
      "value": "207"
    }
  ]
}

```

## Autoscaling workers

You can scale the application in proportion to the number of messages in the queue with the Horizontal Pod Autoscaler. You can deploy the HPA with:

```bash
kubectl create -f k8s-hpa-service/hpa.yaml
```

You can send more traffic to the application with:

```bash
while true; do curl -d "quantity=1" -X POST http://Nodes_PublicIp:32000/submit ; sleep 4; done
```

When the application can't cope with the number of incoming messages, the autoscaler increases the number of pods in 3 minute intervals.

You may need to wait three minutes before you can see more pods joining the deployment with:

```bash
kubectl get pods
```

The autoscaler will remove pods from the deployment every 5 minutes.

You can inspect the event and triggers in the HPA with:

```bash
kubectl get hpa spring-boot-hpa

```
