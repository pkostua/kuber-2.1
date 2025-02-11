#  Решение домашнего задания к занятию «Хранение в K8s. Часть 1»
https://github.com/netology-code/kuber-homeworks/blob/main/2.1/2.1.md
## Задание 1. Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.
### Манифест деплоймента
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: depl-volume
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multitool
  template:
    metadata:
      labels:
        app: multitool
    spec:
      containers:
        - name: multitool
          image: praqma/network-multitool:alpine-extra
          volumeMounts:
            - name: file
              mountPath: "/data-mt"
        - name: busybox
          image: busybox:1.36.1
          command: ['sh', '-c', "until false; do date >> /data-bb/test.txt; sleep 5; done"]
          volumeMounts:
            - name: file
              mountPath: "/data-bb"
      volumes:
        - name: file
          emptyDir: {}
```
### Проверка содержимого файла из контейнера multitool
![image](https://github.com/user-attachments/assets/29292288-b9d8-4e97-8615-c5370f008166)

## Задание 2. Создать DaemonSet приложения, которое может прочитать логи ноды.
### Манифест деплоймента
```
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: depl-demonset
spec:
  selector:
    matchLabels:
      app: daemon-with-volume
  template:
    metadata:
      labels:
        app: daemon-with-volume
    spec:
      containers:
      - name: multitool
        image: praqma/network-multitool:alpine-extra
        volumeMounts:
        - name: syslog
          mountPath: /syslog
          readOnly: true
      volumes:
      - name: syslog
        hostPath:
          path: /var/log/syslog
```
### Проверка логов из контейнера multitool
![image](https://github.com/user-attachments/assets/389fc508-8874-4af4-81cd-ceb3bc0908df)
