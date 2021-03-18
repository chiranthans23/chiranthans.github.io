---
title: "AWS EKS"
date: "2021-03-03"
menu:
  sidebar:
    name: AWS EKS
    identifier: aws-eks
    parent: aws
    weight: 10
---

# AWS EKS


## EKS Overview
- Service managed by Amazon that allows us to run Kubernetes on AWS, without having to manage the infra


## Create a cluster
1. 
```shell=
eksctl create cluster \
--name my-cluster \
--region region-name \
--fargate
```
```
...
[✓]  EKS cluster "my-cluster" in "region-name" region is ready
```
Above line tells the cluster is ready to use in the given region

2. After successful creation of cluster, in `~/.kube/config` we can see cluster details, like below
```yaml
- name: chiras@cisco.com@chiras-eks-cluster1.ap-south-1.eksctl.io
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1alpha1
      args:
      - token
      - -i
      - chiras-eks-cluster1
      command: aws-iam-authenticator
      env:
      - name: AWS_STS_REGIONAL_ENDPOINTS
        value: regional
      - name: AWS_DEFAULT_REGION
        value: ap-south-1
```

3. View your cluster nodes
```shell=
kubectl get nodes -o wide
```
4. View workloads on the cluster
```shell=
kubectl get pods --all-namespaces -o wide
```


## Deploy a sample application
1. Create a namespace
```shell=
kubectl create namespace <my-namespace>
```
2. Create a K8s Service and Deployment using a YAML file
```yaml=
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: my-namespace
  labels:
    app: my-app
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  namespace: my-namespace
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: beta.kubernetes.io/arch
                operator: In
                values:
                - amd64
                - arm64
      containers:
      - name: nginx
        image: public.ecr.aws/z9d2n7e1/nginx:1.19.5
        ports:
        - containerPort: 80
```
3. Deploy the application
```shell=
kubectl apply -f <sample-service.yaml>
```
4. View the resources in the namespace
```shell=
kubectl get all -n my-namespace
```
