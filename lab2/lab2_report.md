University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2022/2023
Group: K4111C
Author: Shulman Emil
Lab: Lab1
Date of create: 22.12.2022
Date of finished:


Создание контейнера vault
img
Скачиваем образ vault командой docker pull vault.
img

Создание Pod
Запускаем minikube - minikube start.
img

Манифест
apiVersion: v1
kind: Pod
metadata:
  name: vault
  labels:
    environment: dev
    tier: vault
spec:
  containers:
    - name: vault
      image: vault
      ports:
        - containerPort: 8200Проверяем, что появился узел - kubectl get nodes.

kubectl create -f vault_pod.yaml  
img

Создание сервиса
Создаем сервис для доступа к Pod - minikube kubectl -- expose pod vault --type=NodePort --port=8200.

img

Перенаправляем трафик с Pod на локальный - minikube kubectl -- port-forward service/vault 8200:8200.

img

Vault

img

Токен minikube kubectl -- logs service/vault
Root Token: hvs.M40dAEMTOLDEIHahn4hfiMQf
img
