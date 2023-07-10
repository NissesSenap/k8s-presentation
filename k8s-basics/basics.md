---
marp: true
theme: gaia
paginate: true
style: @import url('https://unpkg.com/tailwindcss@^2/dist/utilities.min.css');
---

# Kubernetes Basics

An introduction to Kubernetes resources

----

## Agenda

<style scoped>
section {
  font-size: 21px;
}
</style>

- What is Kubernetes
- CNCF
- Pods
- Deployments
- Service
- Ingress
- cert-manager/external dns
- Cronjob
- External secrets
- Disk
- ArgoCD
- Best practices
  - Security
  - Antiaffinity
  - Priorityclasses
  - PDB
  - HPA

---

## What is Kubernetes?

- Container orchestration system
- Created by Google and RedHat with inspiration from Borg and Omega
- Part of CNCF

---

## CNCF

![bg contain](cncf.png)

<!--
CNCF is a part of the Linux foundation
It’s umbrella organization to manage cloud native projects.

https://landscape.cncf.io/

They have 3 levels of projects

- Graduated
- Incubating
- Sandbox

This image is taken May 17 2023, it contains the graduated and the incubating projects that CNCF has.

Mostly what I do is to use these kind of third party applications to build a platform that you can use.

Some of these projects we use and some not.
-->

---

## Pods

<div class="grid grid-cols-2 gap-4">
<div>

- 1 or more containers
- shared filesystem between containers
- 1 ip per pod
- If a node goes down so does your pod

</div>
<div>

<font size="6">

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  containers:
  - image: nginx:1.13.9
    name: nginx
    ports:
    - name: http
      containerPort: 80
```

</font>

</div>
</div>

---

## Deployment

<div class="grid grid-cols-2 gap-4">
<div>

- As a developer you create deployments
- Deployment you can manage Pods in a declarative and upgradable manner.

</div>
<div>

<font size="6">

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.13.9-alpine
        name: nginx
        ports:
        - name: http
          containerPort: 80
```

</font>

</div>
</div>

<!--
- replicas
- matchLabels
- containerPort
-->

---

## Service

<div class="grid grid-cols-2 gap-4">
<div>
<font size="6">

- Expose one or multiple pods within the cluster
- Pod discovery and round robin LB (it’s stupid)
  - In general, don’t take zone or node in the consideration
- {service}.{namespace}.svc.cluster.local
- Service can expose multiple ports
- As developer, you should only use type: ClusterIP
</font>

</div>
<div>

<font size="6">

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  type: ClusterIP
  ports:
  - name: http
    port: 80
    targetPort: 80
  selector:
    app: nginx
```

</font>

</div>
</div>

<!--
As a developer you should only use type: ClusterIP
TargetPort matches the deployment yaml
-->

---

## Ingress

<div class="grid grid-cols-2 gap-4">
<div>

- Multiple ingress implementations
  - ingress-nginx
  - GKE
- Gateway API is the new option

</div>
<div>

<font size="6">

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app1
  labels:
    app: app1
  annotations:
    nginx.ingress.kubernetes.io/proxy-body-size: ”10m”
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - hosts:
    port: 80
    targetPort: 80
  selector:
    app: nginx
```

</font>

</div>
</div>

<!--
Ingress-nginx is probably the most well used ingress implementation.
-->

---

stfuff
