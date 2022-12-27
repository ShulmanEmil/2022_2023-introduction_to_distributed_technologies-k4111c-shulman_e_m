University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4111C

Author: Shulman Emil

Lab: Lab4

Date of create: 22.12.2022

Date of finished:

## Ход работы
Запускаем minikube ```minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode-demo```

Проверяем количество запущеных нод ```kubectl get nodes``` и подов ```kubectl get pods -l k8s-app=calico-node -A```

![Снимок экрана от 2022-12-27 15-24-33](https://user-images.githubusercontent.com/54935204/209665521-c652bc94-8e33-4b42-9d0e-2be3dd31cf85.png)

Манифест calico.yaml 

``` yamml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: calicoctl
  namespace: kube-system

---
apiVersion: v1
kind: Pod
metadata:
  name: calicoctl
  namespace: kube-system
spec:
  nodeSelector:
    kubernetes.io/os: linux
  hostNetwork: true
  serviceAccountName: calicoctl
  containers:
    - name: calicoctl
      image: calico/ctl:v3.20.0
      command:
        - /calicoctl
      args:
        - version
        - --poll=1m
      env:
        - name: DATASTORE_TYPE
          value: kubernetes

---

kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: calicoctl
rules:
  - apiGroups: [""]
    resources:
      - namespaces
      - nodes
    verbs:
      - get
      - list
      - update
  - apiGroups: [""]
    resources:
      - nodes/status
    verbs:
      - update
  - apiGroups: [""]
    resources:
      - pods
      - serviceaccounts
    verbs:
      - get
      - list
  - apiGroups: [""]
    resources:
      - pods/status
    verbs:
      - update
  - apiGroups: ["crd.projectcalico.org"]
    resources:
      - bgppeers
      - bgpconfigurations
      - clusterinformations
      - felixconfigurations
      - globalnetworkpolicies
      - globalnetworksets
      - ippools
      - ipreservations
      - kubecontrollersconfigurations
      - networkpolicies
      - networksets
      - hostendpoints
      - ipamblocks
      - blockaffinities
      - ipamhandles
      - ipamconfigs
    verbs:
      - create
      - get
      - list
      - update
      - delete
  - apiGroups: ["networking.k8s.io"]
    resources:
      - networkpolicies
    verbs:
      - get
      - list

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: calicoctl
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: calicoctl
subjects:
  - kind: ServiceAccount
    name: calicoctl
    namespace: kube-system
```

Запустим ```kubectl apply -f calico.yaml```

![Снимок экрана от 2022-12-27 15-29-04](https://user-images.githubusercontent.com/54935204/209666002-32bbe148-5493-40b6-9dff-10e4fed4694a.png)

Манифест IPPool.yaml

```yaml
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: zone-amsterdam-ippool
spec:
  cidr: 10.0.0.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: zone == "amsterdam"
---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: zone-praga-ippool
spec:
  cidr: 192.168.17.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: zone == "praga"

```

Cоздаем IpPool ```kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch create -f - < ippool.yaml```

Проверяем ```kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch get ippool -o wide```

kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch get ippool -o wide

Манифест для развертывания

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab4-front
  labels:
    app: lab4-front
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab4-front
  template:
    metadata:
      labels:
        app: lab4-front
    spec:
      containers:
      - name: lab4-front
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          value: "Emil"
        - name: REACT_APP_COMPANY_NAME
          value: "ITMO"
```

Манифест сервиса

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: lab4-service
spec:
  selector:
    app: lab4-frontend
  ports:
    - port: 3000
      targetPort: 3000
  type: LoadBalancer
  ```
Деплоим ```kubectl apply -f deployment.yaml -f service.yaml```

![Снимок экрана от 2022-12-27 15-50-50](https://user-images.githubusercontent.com/54935204/209668404-cc83c537-c312-4fea-88b8-b4720fb957b0.png)
