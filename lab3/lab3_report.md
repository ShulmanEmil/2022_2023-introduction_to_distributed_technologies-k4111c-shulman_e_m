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

Создаем kubectl create -f configmap.yaml.

Проверяем kubectl get configmaps

