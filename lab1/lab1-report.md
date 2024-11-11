University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024/2025
Group: K4110c
Author: [Knyshev Ivan Alekseevich](https://github.com/lowskillniy)
Lab: [Laboratory Work №1 "Installing Docker and Minikube, My First Manifest"](https://itmo-ict-faculty.github.io/introduction-to-distributed-technologies/education/labs2023_2024/lab1/lab1/#_5:~:text=c%20%D1%80%D0%B5%D0%B7%D1%83%D0%BB%D1%8C%D1%82%D0%B0%D1%82%D0%B0%D0%BC%D0%B8%20%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B.-,Laboratory%20Work%20No.%201%20%22Installing%20Docker%20and%20Minikube%2C%20My%20First%20Manifest%22,-Description)
Date of create: 05.11.2024
Date of finished: TBD

### Ход работы
---
1. Установка Docker Desktop 4.34.3 на Windows 11. После установки проверка версии клиента и сервера:
![Скриншот установки Docker desktop – проверка версии клиента и сервера](.\img\protocol\1-docker-client-server-version.png)
2. Установка образа `hashicorp/vault` из реестра Docker Hub:
![Подтягивание образа с реестра Docker Hub](.\img\protocol\2-docker-pull-hashicorp-vault.png)
3. Запуск контейнера из образа `hashicorp/vault` и проверка списка активных контейнеров:
![Запуск и проверка контейнера из образа](.\img\protocol\3-docker-run-vault.png)
4. Запуск minikube командой `minikube start`, проверка формирования контейнера с узлом:
![Запуск minikube и проверка контейнера с узлом](.\img\protocol\4-minikube-start.png)
5. Формирование [манифеста](.\lab1-pod.yaml) для пода:
```yaml
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
      image: hashicorp/vault
      resources: 
        limits:
          memory: "512M"
          cpu: ".5m"
      ports:
        - containerPort: 8200
```
6. На основе манифеста создается под. Для этого в терминале осуществляется переход в каталог с манифестом и выполняется команда `kubectl create -f lab1-pod.yaml`, и затем осуществляется проверка создания пода:
![Создание пода на основе манифеста и проверка](.\img\protocol\5-pod-deployment.png)
7. Создание сервиса для доступа к поду командой `minikube kubectl -- expose pod vault --type=NodePort --port=8200`:
![Создание сервиса для доступа к поду](.\img\protocol\6-pod-expose.png)
8. Проброс портов к сервису командой `minikube kubectl -- port-forward service/vault 8200:8200`, чтобы к нему можно было обратиться из браузера на хосте:
![Проброс портов](.\img\protocol\7-port-forwarding.png)
9. Доступ к поду из браузера по адресу `http://localhost:8200/`:
![Подключение к поду из браузера](.\img\protocol\8-vault-login.png)
10. Поиск корневого токена в логах сервиса с помощью команды `minikube kubectl -- logs service/vault | findstr Root`:
![Корневой токен в логах](.\img\protocol\9-root-token.png)
11. Вход с помощью корневого токена:
![Успешный вход](.\img\protocol\10-vault-logon.png)
### Схема организации контейнеров и сервисов
![Схема узла для лабораторной работы](.\img\lab1-scheme.drawio.png)
### Возникшие ошибки
---
1. Выполнил команду `minikube start` в процессе установки Docker Desktop, кластер попытался развернуться с помощью драйвера virtualbox, но что-то пошло не так и ничего не получилось.  
**Решение**: удалил появившуюся виртуальную машину в интерфейсе VirtualBox, дождался пока установится Docker Desktop, выполнил команду `minikube start`, драйвер автоматически поменялся на `docker` и контейнер с кластером успешно создался.
2. Не смог подтянуть образ `vault` при выполнении команды `docker pull vault`:
![Скриншот установки Docker desktop – интерфейс Docker Desktop](.\img\error\1-docker-pull-vault.png)
**Решение**: выполнил команду `docker pull hashicorp/vault`, образ успешно подтянулся из Docker Hub.
3. Попытался повторно выполнить команду `minikube start` при удаленном в Docker Hub контейнере `minikube`. Терминал начал сыпать выводом из `stderr` и завис.
**Решение** Прервал команду `minikube start`, выполнил команду `minikube delete`, после чего `minikube start` успешно выполнилась.