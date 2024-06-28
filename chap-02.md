# 2 Cloud-native application challenges

Tools to run cloud-native applications:

- Where to run? Kubernetes cluster - `Kind`
- How to install microservices? A package manager - `helm`
- How to know what happening? Inspect the K8s cluster - `kubectl`

> [!IMPORTANT]
> Follow the [Creating a local cluster with Kubernetes KinD] guide to create a local KinD cluster that simulates having
>
> - three nodes
> - a special port mapping to allow our Ingress controller to route incoming traffic that we will send to <http://localhost>.

<details>
<summary>
<b>Walkthrough the guide to create a local cluster with Kubernetes KinD</b>
</summary>

- Creating a local cluster with Kubernetes KinD

  ```bash
  cat <<EOF | kind create cluster --name dev --config=-
  kind: Cluster
  apiVersion: kind.x-k8s.io/v1alpha4
  nodes:
  - role: control-plane
    kubeadmConfigPatches:
    - |
      kind: InitConfiguration
      nodeRegistration:
        kubeletExtraArgs:
          node-labels: "ingress-ready=true"
    extraPortMappings:
    - containerPort: 80
      hostPort: 80
      protocol: TCP
    - containerPort: 443
      hostPort: 443
      protocol: TCP
  - role: worker
  - role: worker
  - role: worker
  EOF
  ```

- Loading some container images into Kind before installing the application and other components

  ```bash
  ./kind-load.sh
  ```

  ```bash
  # kind-load.sh
  docker pull bitnami/redis:7.0.11-debian-11-r12
  docker pull bitnami/postgresql:15.3.0-debian-11-r17
  docker pull bitnami/kafka:3.4.1-debian-11-r0
  docker pull registry.k8s.io/ingress-nginx/controller:v1.8.1
  docker pull registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20230407
  docker pull salaboy/frontend-go-1739aa83b5e69d4ccb8a5615830ae66c:v1.0.0
  docker pull salaboy/agenda-service-0967b907d9920c99918e2b91b91937b3:v1.0.0
  docker pull salaboy/c4p-service-a3dc0474cbfa348afcdf47a8eee70ba9:v1.0.0
  docker pull salaboy/notifications-service-0e27884e01429ab7e350cb5dff61b525:v1.0.0

  kind load docker-image -n dev bitnami/redis:7.0.11-debian-11-r12
  kind load docker-image -n dev bitnami/postgresql:15.3.0-debian-11-r17
  kind load docker-image -n dev bitnami/kafka:3.4.1-debian-11-r0
  kind load docker-image -n dev registry.k8s.io/ingress-nginx/controller:v1.8.1
  kind load docker-image -n dev registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20230407
  kind load docker-image -n dev salaboy/frontend-go-1739aa83b5e69d4ccb8a5615830ae66c:v1.0.0
  kind load docker-image -n dev salaboy/agenda-service-0967b907d9920c99918e2b91b91937b3:v1.0.0
  kind load docker-image -n dev salaboy/c4p-service-a3dc0474cbfa348afcdf47a8eee70ba9:v1.0.0
  kind load docker-image -n dev salaboy/notifications-service-0e27884e01429ab7e350cb5dff61b525:v1.0.0
  ```

- Installing NGINX Ingress Controller (to route traffic from our laptop to the services running inside the cluster)

  ```bash
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/release-1.8/deploy/static/provider/kind/deploy.yaml
  ```

  - Confirm the pods start correctly

    ```bash
    kubectl get pods -n ingress-nginx
    ```

</details>

## 2.1 Running our cloud-native applications

### Choosing the best Kubernetes environment for you

- Local Kubernetes cluster (in your computer) <= You'll use this (by using `Kind`)
- On-premises Kubernetes cluster (in your data-center)
- Managed Kubernetes cluster (in the cloud)

### Running workloads on Kubernetes

To run the application on top of Kubernetes:

- Each service needs to be packaged as a container image.

- You have to define different kinds of **Kubernetes resources** (using YAML format) to configure how your containers will _run & communicate_ with each other.

  - Deployment: Declaration of Pods & ReplicaSets
    - Pod: Which containers? How are they configured?
    - ReplicaSet: How many replica Pods (running at the same time)?
  - Service: Which "service" (a group of Pods) to be exposed (inside the cluster)?
  - Ingress: Which "service" to be exposed from outside of the cluster?
  - ConfigMap/Secret: Which configuration data the services need?
  - There is a lot of other types of Kubernetes resources.

#### Packaging and installing Kubernetes applications

To package and manage Kubernetes applications, you usually needs 2 types of tools:

- **Templating engines**: Reuse the same resources for different environments (by replacing variables with values)
  - [Helm's Templates]
  - or [Kustomize], Carvel's [YTT]
- **Package managers**: Grouping, versioning, distributing a group of YAML files as a package
  - [Helm]
  - or Carvel's [Kapp]

## 2.2 Installing the Conference application with a single command

- (Optional) Check the Helm Chart to see what you're going to install

  ```bash
  helm show all oci://docker.io/salaboy/conference-app --version v1.0.0
  ```

  <details>
  <summary>Output</summary>

  ```bash
  Pulled: docker.io/salaboy/conference-app:v1.0.0
  Digest: sha256:8fc41281755a3824ed00f502148977bc1be93db9382d77da991898d7343bf9d3
  apiVersion: v2
  appVersion: v1.0.0
  dependencies:
  - condition: install.infrastructure
    name: redis
    repository: oci://registry-1.docker.io/bitnamicharts
    version: 17.11.3
  - condition: install.infrastructure
    name: postgresql
    repository: oci://registry-1.docker.io/bitnamicharts
    version: 12.5.7
  - condition: install.infrastructure
    name: kafka
    repository: oci://registry-1.docker.io/bitnamicharts
    version: 22.1.5
  description: A Helm chart for the Conference App
  home: http://github.com/salaboy/platforms-on-k8s
  icon: https://www.salaboy.com/content/images/2023/06/avatar-new.png
  keywords:
  - cloudnative
  - platform-engineering
  - platforms
  - book
  - kubernetes
  - tutorial
  - example
  maintainers:
  - email: salaboy@gmail.com
    name: salaboy
    url: http://salaboy.com
  name: conference-app
  type: application
  version: v1.0.0

  # A lot of other things
  # ...
  ```

  </details>

- Use Helm to install the Helm Chart for the Conference application (install the Conference application, and named it `conference`)

  ```bash
  helm install conference oci://docker.io/salaboy/conference-app --version v1.0.0
  ```

  <details>
  <summary>Output</summary>

  ```bash
  Pulled: docker.io/salaboy/conference-app:v1.0.0
  Digest: sha256:8fc41281755a3824ed00f502148977bc1be93db9382d77da991898d7343bf9d3
  NAME: conference
  LAST DEPLOYED: Thu Apr 25 20:03:34 2024
  NAMESPACE: default
  STATUS: deployed
  REVISION: 1
  TEST SUITE: None
  NOTES:
  Cloud-Native Conference Application v1.0.0

  Chart Deployed: conference-app - v1.0.0
  Release Name: conference

  For more information visit: https://github.com/salaboy/platforms-on-k8s

  Access the Conference Application Frontend by running `kubectl port-forward svc/frontend -n default 8080:80`
  ```

  </details>

> [!TIP]
> From Helm 3.7, Helm chart is stored as `OCI artifacts`, which can be collected/distributed with container registry, e.g. Docker Hub
>
> Before Helm 3.7, you needed to manually add and fetch packages from a `Helm Chart Repository`, which used `tar` files to distribute these charts.

> [!TIP]
> If you use Docker, you need to login to Docker Registry to access to public repos (and pull images).

> [!TIP]
> If you can't login with `docker login`, delete `~/.docker/config.json` and try again.

> [!IMPORTANT]
> The `helm install` command creates a _Helm release_, which means that you have created an application instance,
>
> In this case, the instance is called `conference`

### Verifying that the application is up and running

- Check the `conference` instance of our Helm Chart for the Conference application[^1]

  ```bash
  helm list
  ```

  <details>
  <summary>Output</summary>

  ```bash
  NAME      	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART                	APP VERSION
  conference	default  	1       	2024-04-25 20:03:34.284937903 +0700 +07	deployed	conference-app-v1.0.0	v1.0.0
  ```

  </details>

- Check the Pods

  ```bash
  kubernetes get pods
  ```

  <details>
  <summary>Output</summary>

  ```bash
  NAME                                                          READY   STATUS    RESTARTS      AGE
  conference-agenda-service-deployment-746bddcdb4-7nn62         1/1     Running   2 (22m ago)   22m
  conference-c4p-service-deployment-67bc96879c-5wrk7            1/1     Running   3 (22m ago)   22m
  conference-frontend-deployment-797fc494c4-92ff2               1/1     Running   3 (22m ago)   22m
  conference-kafka-0                                            1/1     Running   0             22m
  conference-notifications-service-deployment-f76c4578f-hrhs4   1/1     Running   2 (22m ago)   22m
  conference-postgresql-0                                       1/1     Running   0             22m
  conference-redis-master-0                                     1/1     Running   0             22m
  ```

  - Some Pods restarts 2, 3 times. It's because if the service cannot connect to other containers (which is still bootstrapping), that service will restart and try to connect again.

  </details>

- Get more information of the pods with `-o wide` flag

  ```bash
  kubectl get pods -o wide
  ```

  <details>
  <summary>Output</summary>

  ```bash
  NAME                                                          READY   STATUS    RESTARTS      AGE   IP           NODE          NOMINATED NODE   READINESS GATES
  conference-agenda-service-deployment-746bddcdb4-7nn62         1/1     Running   2 (63m ago)   64m   10.244.1.4   dev-worker3   <none>           <none>
  conference-c4p-service-deployment-67bc96879c-5wrk7            1/1     Running   3 (63m ago)   64m   10.244.3.2   dev-worker2   <none>           <none>
  conference-frontend-deployment-797fc494c4-92ff2               1/1     Running   3 (63m ago)   64m   10.244.2.3   dev-worker    <none>           <none>
  conference-kafka-0                                            1/1     Running   0             64m   10.244.2.6   dev-worker    <none>           <none>
  conference-notifications-service-deployment-f76c4578f-hrhs4   1/1     Running   2 (63m ago)   64m   10.244.1.3   dev-worker3   <none>           <none>
  conference-postgresql-0                                       1/1     Running   0             64m   10.244.3.4   dev-worker2   <none>           <none>
  conference-redis-master-0                                     1/1     Running   0             64m   10.244.2.7   dev-worker    <none>           <none>
  ```

  - The Pods can be scheduled in different nodes.

  </details>

- (Optional) Check all Kubernetes resources (created by the Conference application).

  ```bash
  kubectl get all
  ```

  <details>
  <summary>Output: Can you guess how many are there?</summary>

  ```
  NAME                                                              READY   STATUS    RESTARTS      AGE
  pod/conference-agenda-service-deployment-746bddcdb4-7nn62         1/1     Running   2 (22m ago)   22m
  pod/conference-c4p-service-deployment-67bc96879c-5wrk7            1/1     Running   3 (21m ago)   22m
  pod/conference-frontend-deployment-797fc494c4-92ff2               1/1     Running   3 (22m ago)   22m
  pod/conference-kafka-0                                            1/1     Running   0             22m
  pod/conference-notifications-service-deployment-f76c4578f-hrhs4   1/1     Running   2 (22m ago)   22m
  pod/conference-postgresql-0                                       1/1     Running   0             22m
  pod/conference-redis-master-0                                     1/1     Running   0             22m

  NAME                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
  service/agenda-service              ClusterIP   10.96.188.116   <none>        80/TCP                       22m
  service/c4p-service                 ClusterIP   10.96.118.116   <none>        80/TCP                       22m
  service/conference-kafka            ClusterIP   10.96.205.142   <none>        9092/TCP                     22m
  service/conference-kafka-headless   ClusterIP   None            <none>        9092/TCP,9094/TCP,9093/TCP   22m
  service/conference-postgresql       ClusterIP   10.96.97.215    <none>        5432/TCP                     22m
  service/conference-postgresql-hl    ClusterIP   None            <none>        5432/TCP                     22m
  service/conference-redis-headless   ClusterIP   None            <none>        6379/TCP                     22m
  service/conference-redis-master     ClusterIP   10.96.67.111    <none>        6379/TCP                     22m
  service/frontend                    ClusterIP   10.96.228.70    <none>        80/TCP                       22m
  service/kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                      4h27m
  service/notifications-service       ClusterIP   10.96.220.231   <none>        80/TCP                       22m

  NAME                                                          READY   UP-TO-DATE   AVAILABLE   AGE
  deployment.apps/conference-agenda-service-deployment          1/1     1            1           22m
  deployment.apps/conference-c4p-service-deployment             1/1     1            1           22m
  deployment.apps/conference-frontend-deployment                1/1     1            1           22m
  deployment.apps/conference-notifications-service-deployment   1/1     1            1           22m

  NAME                                                                    DESIRED   CURRENT   READY   AGE
  replicaset.apps/conference-agenda-service-deployment-746bddcdb4         1         1         1       22m
  replicaset.apps/conference-c4p-service-deployment-67bc96879c            1         1         1       22m
  replicaset.apps/conference-frontend-deployment-797fc494c4               1         1         1       22m
  replicaset.apps/conference-notifications-service-deployment-f76c4578f   1         1         1       22m

  NAME                                       READY   AGE
  statefulset.apps/conference-kafka          1/1     22m
  statefulset.apps/conference-postgresql     1/1     22m
  statefulset.apps/conference-redis-master   1/1     22m
  ```

  </details>

### Interacting with your application

The application is now up and running, and you can access it by pointing your favorite browser to <http://localhost>.

## 2.3 Inspecting the walking skeleton

To inspect a Kubernetes cluster (and the walking skeleton), you can use `kubectl`.

### Kubernetes deployments basics

In [Running workloads on Kubernetes](#running-workloads-on-kubernetes), you know that a deployment is a declaration of Pods & ReplicaSets.

A deployment also handle how the containers will be upgraded to newer versions when needed.

A deployment detail has many information:

- The _container_ (used by the deployment)
  - The _resource allocation_ (for the container)
  - The status of _readiness probe_ & _liveness probe_
- The number of _replicas_ (required by the deployment)
- The rolling update strategy
- ...

### Exploring deployments

- First, let's see all the deployment you have

  ```bash
  kubernetes get deployments
  ```

  <details>
  <summary>Output</summary>

  ```
  NAME                                                          READY   STATUS    RESTARTS      AGE
  conference-agenda-service-deployment-746bddcdb4-ztfj7         1/1     Running   2 (13h ago)   13h
  conference-c4p-service-deployment-67bc96879c-9fd5l            1/1     Running   2 (13h ago)   13h
  conference-frontend-deployment-797fc494c4-tv6f9               1/1     Running   2 (13h ago)   13h
  conference-kafka-0                                            1/1     Running   0             13h
  conference-notifications-service-deployment-f76c4578f-x2xv6   1/1     Running   1 (13h ago)   13h
  conference-postgresql-0                                       1/1     Running   0             13h
  conference-redis-master-0                                     1/1     Running   0             13h
  ```

  </details>

- Then, see the detail of a deployment (with `kubectl describe deployment`)

  ```bash
  kubectl describe deployment conference-frontend-deployment
  ```

  <details>
  <summary>Output</summary>

  ```yaml
  Name:                   conference-frontend-deployment
  Namespace:              default
  CreationTimestamp:      Fri, 26 Apr 2024 00:31:32 +0700
  Labels:                 app.kubernetes.io/managed-by=Helm
  Annotations:            deployment.kubernetes.io/revision: 1
                          meta.helm.sh/release-name: conference
                          meta.helm.sh/release-namespace: default
  Selector:               app=frontend
  Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable # 1️⃣
  StrategyType:           RollingUpdate # 5️⃣
  MinReadySeconds:        0
  RollingUpdateStrategy:  25% max unavailable, 25% max surge # 5️⃣
  Pod Template:
    Labels:  app=frontend
             app.kubernetes.io/name=frontend
             app.kubernetes.io/part-of=frontend
             app.kubernetes.io/version=v1.0.0
             keptn.sh/post-deployment-tasks=stdout-notification
    Containers:
     frontend:
      Image:      salaboy/frontend-go-1739aa83b5e69d4ccb8a5615830ae66c:v1.0.0 # 2️⃣
      Port:       8080/TCP
      Host Port:  0/TCP
      Liveness:   http-get http://:8080/health/liveness delay=0s timeout=1s period=10s #success=1 #failure=3
      Readiness:  http-get http://:8080/health/readiness delay=0s timeout=1s period=10s #success=1 #failure=3
      Environment: # 3️⃣
        AGENDA_SERVICE_URL:         http://agenda-service.default.svc.cluster.local
        C4P_SERVICE_URL:            http://c4p-service.default.svc.cluster.local
        NOTIFICATIONS_SERVICE_URL:  http://notifications-service.default.svc.cluster.local
        KAFKA_URL:                  conference-kafka.default.svc.cluster.local
        POD_NODENAME:                (v1:spec.nodeName)
        POD_NAME:                    (v1:metadata.name)
        POD_NAMESPACE:               (v1:metadata.namespace)
        POD_IP:                      (v1:status.podIP)
        POD_SERVICE_ACCOUNT:         (v1:spec.serviceAccountName)
      Mounts:                       <none>
    Volumes:                        <none>
    Node-Selectors:                 <none>
    Tolerations:                    <none>
  Conditions:
    Type           Status  Reason
    ----           ------  ------
    Available      True    MinimumReplicasAvailable
    Progressing    True    NewReplicaSetAvailable
  OldReplicaSets:  <none>
  NewReplicaSet:   conference-frontend-deployment-797fc494c4 (1/1 replicas created)
  Events:          <none> # 4️⃣
  ```

  - 1️⃣: The replicas (of this deployment)
  - 2️⃣: The container image (with name & tag)
  - 3️⃣: The environment variables (used to configure this container)
  - 4️⃣: What happen with this Deployment?
  - 5️⃣: The update strategy: `RollingUpdate`

  </details>

### ReplicaSets

The ReplicaSet object ensures the number of replicas (of a pod) run at the same time.

- List the ReplicaSets in the cluster

  ```bash
  kubectl get replicasets
  # kubectl get replicasets.app
  # kubectl get replicaset
  # kubectl get rs
  ```

  <details>
  <summary>Output</summary>

  ```bash
  NAME                                                                    DESIRED   CURRENT   READY   AGE
  replicaset.apps/conference-agenda-service-deployment-746bddcdb4         1         1         1       15h
  replicaset.apps/conference-c4p-service-deployment-67bc96879c            1         1         1       15h
  replicaset.apps/conference-frontend-deployment-797fc494c4               1         1         1       15h
  replicaset.apps/conference-notifications-service-deployment-f76c4578f   1         1         1       15h

  ```

  </details>

> [!NOTE]
> When created by a Deployment object, the ReplicaSet object is fully managed by the Deployment object.

- You shouldn't deal with a managed ReplicaSet, to change number of replicas for a deployment, you use `kubectl scale`:

  ```bash
  kubectl scale --replicas=<NUMBER_OF_REPLICAS> deployments/<DEPLOYMENT_ID>
  ```

  e.g.

  - Scale up the deployment to 2 replicas

    ```bash
    kubectl scale deployment --replicas=2 conference-frontend-deployment
    ```

    <details>
    <summary>Output</summary>

    ```
    deployment.apps/conference-frontend-deployment scaled
    ```

    </details>

> [!IMPORTANT]
> When you change the number of replicas for a deployment, you're _**horizontal** scaling_ that deployment:
>
> - When you increase the number of replicas, you're _scaling **out**_ the deployment.
> - When you decrease the number of replicas, you're _scaling **in**_ the deployment.

> [!CAUTION]
> Be careful when you use the word _scale_.
>
> - _**Vertical** scaling_ (Scale up/down) is a different way to scale a a system.

- The Conference application's Frontend service has a feature to show the information about the application containers.

  This feature can be turned on by setting an environment variable

  ```bash
  kubectl set env deployment/conference-frontend-deployment FEATURE_DEBUG_ENABLED=true
  ```

> [!NOTE]
> When you change the deployment specification (e.g. Setting the environment variable), the rolling update mechanism of the Deployment resource will kick in.
>
> - All the existing pods managed by this deployment will be upgrade to have the new specification
> - By default - the `StrategyType` is `RollingUpdate`:
>   - The deployment will
>     - start a new pod with the new specification
>     - wait for it to be ready
>     - then terminate the old version of the pod
>   - This process is repeated until all the pods are using the new specification.

> [!IMPORTANT]
> By default, Kubernetes will load-balance the requests between the replicas.
>
> Just run `kubectl scale` and let Kubernetes do the rest 😉.

### Connecting services

Each container in a pod has its own IP address. How the containers communicate with each other?

Kubernetes provides and advanced _**service-discovery** mechanism_ that allows:

- services (in the cluster) to communicate without knowing IP address of the ephemeral pods.

> [!TIP]
> How about the services outside the clusters?

### Exploring services

To expose your container to other services, you use the Kubernetes `Service` resource.

The Service resource will be in charge of traffic to your containers:

- Each service represent a logical **name** abstract where the containers run.
- If there are multiple replicas of a container, the service will load balance the load between the replicas.

Let's explore the services

- List the services,

  ```bash
  kubectl get services
  ```

  <details>
  <summary>Output</summary>

  ```
  AME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
  agenda-service              ClusterIP   10.96.181.126   <none>        80/TCP                       24h
  c4p-service                 ClusterIP   10.96.101.236   <none>        80/TCP                       24h
  conference-kafka            ClusterIP   10.96.40.95     <none>        9092/TCP                     24h
  conference-kafka-headless   ClusterIP   None            <none>        9092/TCP,9094/TCP,9093/TCP   24h
  conference-postgresql       ClusterIP   10.96.50.32     <none>        5432/TCP                     24h
  conference-postgresql-hl    ClusterIP   None            <none>        5432/TCP                     24h
  conference-redis-headless   ClusterIP   None            <none>        6379/TCP                     24h
  conference-redis-master     ClusterIP   10.96.201.57    <none>        6379/TCP                     24h
  frontend                    ClusterIP   10.96.204.65    <none>        80/TCP                       24h
  kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                      24h
  notifications-service       ClusterIP   10.96.165.216   <none>        80/TCP                       24h
  ```

  </details>

- Describe the detail of a service

  ```bash
  kubectl describe service frontend
  ```

  <details>
  <summary>Output</summary>

  ```bash
  Name:              frontend
  Namespace:         default
  Labels:            app.kubernetes.io/managed-by=Helm
  Annotations:       meta.helm.sh/release-name: conference
                     meta.helm.sh/release-namespace: default
  Selector:          app=frontend # The selector to link the service and the pods (of the deployment)
  Type:              ClusterIP
  IP Family Policy:  SingleStack
  IP Families:       IPv4
  IP:                10.96.204.65
  IPs:               10.96.204.65
  Port:              <unset>  80/TCP
  TargetPort:        8080/TCP
  Endpoints:         10.244.2.4:8080,10.244.3.5:8080
  Session Affinity:  None
  Events:            <none>
  ```

  - This service will route traffic to all the pods with the label `app=frontend` (created by the deployment)

  </details>

### Service discovery in Kubernetes

To expose your services outside the Kubernetes cluster, you need an `Ingress` resource.

The `Ingress` resource is in charge of routing traffic from outside the cluster to services that are inside the cluster

Let's explore the ingress:

- List the ingresses

  ```bash
  kubectl get ingresses
  ```

  ```
  NAME                          CLASS   HOSTS   ADDRESS     PORTS   AGE
  conference-frontend-ingress   nginx   *       localhost   80      25h
  ```

- Get detail of an ingress

  ```bash
  kubectl describe ingresses conference-frontend-ingress
  ```

  <details>
  <summary>Output</summary>

  ```yaml
  Name:             conference-frontend-ingress
  Labels:           app.kubernetes.io/managed-by=Helm
  Namespace:        default
  Address:          localhost
  Ingress Class:    nginx
  Default backend:  <default>
  Rules:
    Host        Path  Backends
    ----        ----  --------
    *
                /   frontend:80 (10.244.2.4:8080,10.244.3.5:8080)
  Annotations:  meta.helm.sh/release-name: conference
                meta.helm.sh/release-namespace: default
                nginx.ingress.kubernetes.io/rewrite-target: /
  Events:       <none>
  ```

  - All traffic to `/` will go to the `frontend:80` service

    The ingress also uses the service's name (`frontend`) to route traffics

  </details>

> [!IMPORTANT]
> Usually, you will not expose multiple services and limit the entry points for your applications.
>
> For example, only `frontend` service.

### Troubleshooting internal services

To access an _internal_ **service** for debug, troubleshoot purpose, you can use `kubectl port-forward` to _temporary_ expose that service.

For example:

- To temporary expose the `Agenda` service,

  ```bash
  $ kubectl port-forward svc/agenda-service 8080:80
  Forwarding from 127.0.0.1:8080 -> 8080
  Forwarding from [::1]:8080 -> 8080
  ```

> [!TIP]
> Don’t kill the `kubectl port-forward` command.

- Access the `Agenda` service

  ```bash
  $ curl -s localhost:8080/service/info | jq --color-output
  {
    "name": "AGENDA",
    "version": "1.0.0",
    "source": "https://github.com/salaboy/platforms-on-k8s/tree/main/conference-application/agenda-service",
    "podName": "conference-agenda-service-deployment-746bddcdb4-ztfj7",
    "podNamespace": "default",
    "podNodeName": "dev-worker",
    "podIp": "10.244.2.3",
    "podServiceAccount": "default"
  }
  ```

## 2.4 Cloud-native application challenges

### Downtime is not allowed

With Kubernetes, your cloud-native application can run without downtime by its ability to:

- Easily scale up/down (the number of replicas)
- Self-healing (for each replica)

You only needs to ensure:

- Your services were designed based on the assumption that the platform will scale them by creating new copies of the containers running the service.
- Your user-facing services are running with more than 1 replica.

### Service’s resilience built-in

Your cloud-native application should not suffer from downtime, but its services may go down a lot of times.

If a down-stream service is failing, the user should still be able to user the application (with limited functionality).

To ensure that errors or services going down don’t bring your entire application down, each service should be designed to be resilient:

- not propagate the error back to the user
- deal with the errors generated by downstream services

  e.g. Having a CronJob the periodically caches response from a down-stream service.

### Dealing with the application state is not trivial

To allow Kubernetes to scale the service up/down efficiently, you must understand

- where to store "application" state:

  - in-memory store (inside the pod)
  - external store (outside the pod)

    e.g. You can use an external persistent store: a database, a memory cache. But remember to ensure connection pool size to handle connections from all replicas.

- do subsequent requests need to go to the same replica

  e.g. Web application may use HTTP session and require sticky sessions. This requires more component at the infrastructure level, e.g. a cache

- the infrastructure of each service

### Dealing with inconsistent data

Your cloud-native application - a distributed system - will store data in a lot of different stores that are hidden by the services API.

The application will need to be ready to deal with cases where different services have different views of the state of the world.

You need mechanisms to check ensure your application data is _eventual consistency_.

### Understanding how the application is working (monitoring, tracing, and telemetry)

To fully understand how your cloud-native application work, you can use:

- OpenTelemetry: a centralized place that stores & aggregates:

  - _metrics_: A metric is a measurement of a service captured at runtime.
  - _logs_: A log is timestamped text record, either structured (recommended) or unstructured, with metadata
  - _traces_: Help you understanding the full “path” a request takes in your application

  for analysis to understand your software's performance & behavior.

- Prometheus: Systems _monitoring_ & _alerting_ toolkit: Collects & stores metrics as time series data.

  - has ad-hoc graphs (for basic query and debug)
  - for advanced graphs/dashboards, use Grafana to query Prometheus.

- Grafana: Data **visualization** & monitoring solution: Build domain-specific dashboards.

  See <https://play.grafana.org/dashboards>

For more information, see:

- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
  - [What is OpenTelemetry?](https://opentelemetry.io/docs/what-is-opentelemetry/)
  - [Signals](https://opentelemetry.io/docs/concepts/signals/)
- [Prometheus Overview | Prometheus Docs](https://prometheus.io/docs/introduction/overview/)
- [What is Prometheus? | Grafana Docs](https://grafana.com/docs/grafana/latest/fundamentals/intro-to-prometheus/)

### Application security and identity management

For cloud-native application:

- Authentication & authorization identity must be propagated across different services.

- It's common to have a component that generates requests on behalf of a user (instead of exposing all the services for the user to interact directly).

  This external-facing component:

  - act as the barrier between external and internal services.
  - is usually connect with an _Authentication & Authorization Provider_ (a.k.a _Identity Management Service_) using OAuth2 protocol
    - responsible to connect to an _Identity Provider_:
      - internal LDAP server
      - Google, GitHub (aka _social logins_)
    - validate the user credential, and provide the roles/groups that define what the user can do/can't do in different services.
  - handles the login flow (authentication & authorization), but only the context is propagated to the backend services

  There are some solutions - such as [Keycloak], [Zitadel] - that bring both:

  - SSO
  - Identity Management

### Other challenges

## 2.5 Linking back to platform engineering

Kubernetes has built-in mechanism:

- for resilient and scale up your cloud-native application.
- to run your application without downtime, even when you're constantly updating it.
  This allow you to release new versions of your application's components more frequently.

By using a _package manager_ (Helm) to

- package
- version

the _configuration files_ (Kubernetes resources) required to deploy our application, you can easily create new **application instances** in different **environments**.

Platform teams should automate all steps involved in

- testing, building (Continuous Integration)
- package, publishing (Continuous Delivery)
- deploy into an environment (Continuous Deployment

so developers can focus on building features.

## Summary

[Creating a local cluster with Kubernetes KinD]: https://github.com/salaboy/platforms-on-k8s/tree/main/chapter-2#creating-a-local-cluster-with-kubernetes-kind
[Kustomize]: https://kustomize.io/
[Helm's Templates]: https://helm.sh/docs/chart_best_practices/templates/#helm
[Helm]: https://helm.sh/
[YTT]: https://carvel.dev/ytt/
[Kapp]: https://carvel.dev/kapp/
[Keycloak]: https://www.keycloak.org/
[Zitadel]: https://zitadel.com/opensource

[^1]: https://github.com/salaboy/platforms-on-k8s/tree/main/conference-application/helm/conference-app
