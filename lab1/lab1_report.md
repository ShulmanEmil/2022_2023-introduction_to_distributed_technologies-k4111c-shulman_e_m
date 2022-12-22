University: ITMO University
Faculty: FICT
Course: Introduction to distributed technologies
Year: 2022/2023
Group: K4111c
Author: Shulman Emil
Lab: Lab1
Date of create: 22.12.2022
Date of finished: 


Создание контейнера vault
Скачать - docker pull vault
docker images
![1](https://user-images.githubusercontent.com/54935204/209160265-94d10745-b785-46c8-ab68-7eb49fca50ec.png)

Создаем контейнер - docker run -d --name vault vault.
vault - docker ps -a.
![2](https://user-images.githubusercontent.com/54935204/209160603-e1e38692-d786-49d1-8ade-036214993303.png)


Создание Pod
Запускаем minikube - minikube start.
kubectl get nodes
![3](https://user-images.githubusercontent.com/54935204/209160839-0a4d1fa4-ed90-4e44-bbb2-65fa7bdecdfa.png)

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
        - containerPort: 8200

kubectl create -f vault_pod.yaml  

Проверяем pod
kubectl get pods

![4](https://user-images.githubusercontent.com/54935204/209161371-a6bc95d8-bc58-4ca0-ad51-3c03eb93a429.png)


Создание сервиса
Сервис доступа к Pod - minikube kubectl -- expose pod vault --type=NodePort --port=8200.

![5](https://user-images.githubusercontent.com/54935204/209161609-1dead65d-4d52-4cbd-b1bc-d0a4ae108603.png)

Локальный - minikube kubectl -- port-forward service/vault 8200:8200.
![6](https://user-images.githubusercontent.com/54935204/209161724-919afcf8-2074-4d9b-8c54-a361b110495d.png)

Vault
![7](https://user-images.githubusercontent.com/54935204/209161760-c97bef70-fd56-4b1f-a984-87c86be3ee66.png)


Токен minikube kubectl -- logs service/vault
Root Token: hvs.M40dAEMTOLDEIHahn4hfiMQf
![8](https://user-images.githubusercontent.com/54935204/209161965-58cca131-40a6-482a-8c38-91f29c16bbf0.png)

