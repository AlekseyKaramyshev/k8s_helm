# Домашнее задание к занятию «Helm»

### Задание 1. Подготовить Helm-чарт для приложения

1. Необходимо упаковать приложение в чарт для деплоя в разные окружения. 
2. Каждый компонент приложения деплоится отдельным deployment’ом или statefulset’ом.
3. В переменных чарта измените образ приложения для изменения версии.
4. 
<details>
<summary>Manifests</summary>

---

### Решение

Chart.yaml
```yaml
apiVersion: v2
name: my-app
description: Helm chart for deploying nginx and wbitt/network-multitool
version: 0.1.0
appVersion: "1.0.0"
```
values.yaml
```yaml
nginx:
  image: nginx:1.25.3
  replicas: 2
  service:
    type: ClusterIP
    port: 80

networkMultitool:
  image: wbitt/network-multitool:latest
  replicas: 1
  service:
    type: ClusterIP
    port: 8080
```
values-v2.yaml
```yaml
nginx:
  image: nginx:1.24.0
  replicas: 2
  service:
    type: ClusterIP
    port: 80

networkMultitool:
  image: wbitt/network-multitool:latest
  replicas: 1
  service:
    type: ClusterIP
    port: 8080
```
templates/values-v3.yaml
```yaml
nginx:
  image: nginx:latest
  replicas: 2
  service:
    type: ClusterIP
    port: 80

networkMultitool:
  image: wbitt/network-multitool
  replicas: 1
  service:
    type: ClusterIP
    port: 8080
```
templates/nginx-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-nginx
  labels:
    app: {{ .Chart.Name }}
    component: nginx
spec:
  replicas: {{ .Values.nginx.replicas }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
      component: nginx
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
        component: nginx
    spec:
      containers:
      - name: nginx
        image: {{ .Values.nginx.image }}
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-nginx
spec:
  type: {{ .Values.nginx.service.type }}
  ports:
  - port: {{ .Values.nginx.service.port }}
    targetPort: 80
  selector:
    app: {{ .Chart.Name }}
    component: nginx
```
templates/network-multitool-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-network-multitool
  labels:
    app: {{ .Chart.Name }}
    component: network-multitool
spec:
  replicas: {{ .Values.networkMultitool.replicas }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
      component: network-multitool
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
        component: network-multitool
    spec:
      containers:
      - name: network-multitool
        image: {{ .Values.networkMultitool.image }}
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}-network-multitool
spec:
  type: {{ .Values.networkMultitool.service.type }}
  ports:
  - port: {{ .Values.networkMultitool.service.port }}
    targetPort: 8080
  selector:
    app: {{ .Chart.Name }}
    component: network-multitool
```
</details>

---

### Задание 2. Запустить две версии в разных неймспейсах

1. Подготовив чарт, необходимо его проверить. Запуститe несколько копий приложения.
2. Одну версию в namespace=app1, вторую версию в том же неймспейсе, третью версию в namespace=app2.
3. Продемонстрируйте результат.

---

### Решение

<img width="963" height="756" alt="2_1" src="https://github.com/user-attachments/assets/bb7c0002-a746-4fc1-8d08-9d4d6e972ca4" />

<img width="962" height="157" alt="2_2" src="https://github.com/user-attachments/assets/2dc40227-6cb0-4e29-b54f-56f7b9c3750c" />

<img width="1010" height="622" alt="2_3" src="https://github.com/user-attachments/assets/da861ddd-c8ce-4915-a33a-026548b1c9a7" />

<img width="1050" height="623" alt="2_4" src="https://github.com/user-attachments/assets/d26f58ea-dd9e-4748-a3e0-699420d78888" />

<img width="1221" height="938" alt="2_5" src="https://github.com/user-attachments/assets/2ce717c5-ce5b-443c-a6a6-3ca0d526e716" />
