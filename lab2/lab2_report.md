University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4111C

Author: Shulman Emil

Lab: Lab2

Date of create: 22.12.2022

Date of finished: 22.12.2022


## Создание frontend-container

Скачиваем образ - docker pull ifilyaninitmo/itdt-contained-frontend:master
Проверяем - docker images

![1](https://user-images.githubusercontent.com/54935204/209168333-819c4ec4-ffc5-45e5-af5b-ca0d498c6daa.png)

Создаем контейнер - docker run -d --name frontend-container ifilyaninitmo/itdt-contained-frontend:master


Проверяем - docker ps -a


![2](https://user-images.githubusercontent.com/54935204/209168902-50a8e7dd-ede6-49fc-9250-653594412e83.png)

## Создание Deployment

Запуск minikube

```apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend_pod
  template:
    metadata:
      labels:
        app: frontend_pod
    spec:
      containers:
      - name: frontend-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          value: Emil
        - name: REACT_APP_COMPANY_NAME
          value: ITMO
```
Запускаем 2 экземпляр   
kubectl create -f mn.yaml

Проверяем
kubectl get deployments

![3](https://user-images.githubusercontent.com/54935204/209171695-591424a5-f2ae-4a88-a91f-d4c1078e32da.png)


## Создание сервиса frontend-service

Сервис для доступа к развертыванию - minikube kubectl -- expose deployment frontend --port=3000 --target-port=3000 --name=frontend-service --type=LoadBalance

![4](https://user-images.githubusercontent.com/54935204/209172222-5ac92a80-0b57-4d96-b925-6dcdb5e0477c.png)

Порт -  minikube kubectl -- port-forward service/frontend-service 3000:3000

![5](https://user-images.githubusercontent.com/54935204/209172486-47fbe2c1-a402-4026-a6d3-749c98219525.png)

http://localhost:3000

![6](https://user-images.githubusercontent.com/54935204/209172752-c9fd952b-90ee-4156-98d1-7a70eb8f62fc.png)

Логи

![7](https://user-images.githubusercontent.com/54935204/209173080-496c48bd-52aa-47ea-9f04-8bc5c579d86b.png)

Удоляем безумие
kubectl delete deployments/frontend

![lab2](https://user-images.githubusercontent.com/54935204/209173492-bee94c29-57d0-4b2c-9419-1695ea6e0f3e.png)

          
