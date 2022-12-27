University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4111C

Author: Shulman Emil

Lab: Lab3

Date of create: 22.12.2022

Date of finished:

## Ход работы 

minikube start

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: frontend-configmap
data:
  react_app_user_name: "Emil"
  react_app_company_name: "ITMO"
```

Создаем ```kubectl create -f configmap.yaml```

Проверяем ```kubectl get configmaps```

![1](https://user-images.githubusercontent.com/54935204/209647856-7ec7492c-1282-45f3-b4ed-c32fd09b5773.png)


ReplicaSet

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-replicaset
  labels:
    app: lab3-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab3-frontend
  template:
    metadata:
      labels:
        app: lab3-frontend
    spec:
      containers:
      - name: frontend-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          valueFrom:
            configMapKeyRef:
              name: frontend-configmap
              key: react_app_user_name
        - name: REACT_APP_COMPANY_NAME
          valueFrom:
            configMapKeyRef:
              name: frontend-configmap
              key: react_app_company_name
```

Создаем контроллер  ```kubectl create -f replicaset.yaml```

Проверяем ```kubectl get rs```

![2](https://user-images.githubusercontent.com/54935204/209650276-91cd44db-9667-4dd5-839a-1ecfe6171529.png)


Создания Inngress ресурса 

```
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  labels:
    app: lab3-frontend
spec:
  type: NodePort
  selector:
    app: lab3-frontend
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30333
```

```kubectl create -f in.yaml```

Проверяем ```kubectl get services```

![3](https://user-images.githubusercontent.com/54935204/209650338-c7910afe-08d2-4be5-9b66-aee45710108d.png)

Генерация TLS

Создаем приватный ключ ```openssl genrsa -out lab3.key 2048```

Запрос на подпись  ```openssl req -key lab3.key -new -out lab3.csr```

Подписываем сертификат ```openssl x509 -signkey lab3.key -in lab3.csr -req -days 30 -out lab3.crt```

![Снимок экрана от 2022-12-27 14-13-25](https://user-images.githubusercontent.com/54935204/209657722-cf44c940-e8d4-455b-8ecf-07bec3652996.png)

Создаем секрет ```kubectl create secret tls lab3-tls --cert=lab3.crt --key=lab3.key```

![Снимок экрана от 2022-12-27 13-25-38](https://user-images.githubusercontent.com/54935204/209651768-f4db3258-3f8a-47aa-98c1-0d6f4740ff3b.png)

Подключаем ingress

![Снимок экрана от 2022-12-27 13-55-10](https://user-images.githubusercontent.com/54935204/209657421-9140c3ca-495b-4eca-b60a-73c00d40c15b.png)

Манифест

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
spec:
  tls:
  - hosts:
      - lab3.emil
    secretName: lab3-tls
  rules:
  - host: lab3.emil
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 3000
```

Создаем ```kubectl create -f ing.yaml```

Проверяем ```kubectl get ingress```

Подключаемся к Ingress командой ```minikube tunnel```

![Снимок экрана от 2022-12-27 13-56-29](https://user-images.githubusercontent.com/54935204/209657581-620c6365-bfc5-4ebc-8e31-5ee1d3a559de.png)

Проверяем сертификат

![Снимок экрана от 2022-12-27 14-08-24](https://user-images.githubusercontent.com/54935204/209657552-5140457b-aca4-4f75-af76-3f5bec54f54a.png)

![lab3](https://user-images.githubusercontent.com/54935204/209657835-935bfc98-7894-4e8b-86e3-90c81f0cba0e.png)
