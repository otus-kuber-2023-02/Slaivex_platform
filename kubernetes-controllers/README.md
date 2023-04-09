# Выполнено ДЗ № 2

- [x] Основное ДЗ
- [x] Задание со *

## В процессе сделано

- Подготовлены следующие файлы манифестов:
  - kind-config.yaml - Манифест определяющий конфигурацию кластера kind
  - frontend-replicaset.yaml - Манифест определяющий ReplicaSet для frontend приложения из прошлого занятия.
- Получен ответ на вопрос: Почему обновление ReplicaSet не повлекло обновление запущенных podов?
  - Это связано с тем, что ReplicaSet используется для поддержания необходимого количества реплик в соответствии с манифестом и лейблами в селекторе, однако он не предназначен для обновления самих подов, для этого служит концепция более высокого уровня - Deployment

## Как запустить проект

- Создать kind кластер с использованием манифеста kind-config.yaml

  ```bash
  kind create cluster --config kind-config.yaml
  ```

- Применить манифест frontend приложения с помощью команды:

  ```bash
  kubectl apply -f frontend-replicaset.yaml
  ```

- Применить манифест paymentservice-deployment.yaml с помощью команды:

  ```bash
  kubectl apply -f paymentservice-deployment.yaml
  ```

- Применить манифест paymentservice-deployment-bg.yaml для реализации Blue Green Deploy с помощью команды:

  ```bash
  kubectl apply -f paymentservice-deployment-bg.yaml | kubectl get pods -l app=paymentservice -w
  ```

- Применить манифест paymentservice-deployment-bg.yaml для реализации Reverse Rolling Update с помощью команды:

  ```bash
  kubectl apply -f paymentservice-deployment-reverse.yaml | kubectl get pods -l app=paymentservice -w
  ```

- Применить манифест frontend-deployment.yaml для создания Deployment приложения frontend с помощью команды:

  ```bash
  kubectl apply -f frontend-deployment.yaml | kubectl get pods -l app=frontend -w
  ```

- Применить манифест node-exporter-daemonset.yaml для развертывания DaemonSet на всех нодах включая contol-plane с помощью команды:

  ```bash  
  kubectl apply -f node-exporter-daemonset.yaml
  ```

## Как проверить работоспособность

- После создания кластера убедиться, что его запуск прошел успешно с помощью команды:

  ```bash
  kubectl get nodes
  ```

- После создания ReplicaSet приложения frontend, убедиться, что количество реплик соответствует указанным в манифесте

  ```bash
  kubectl get pods -l app=frontend
  ```

- После создания Deployment приложения paymentservice, убедиться, что количество реплик соответствует 3-ем используя команды

  ```bash
  kubectl get pods -l app=paymentservice
  # Or
  kubectl get deploy -l app=paymentservice
  ```

- Проверка работы Blue Green Deploy для приложения paymentservice может быть выполнена путем применения соответствующего манифеста paymentservice-deployment-bg.yaml c использованием новой версии приложения и проверкой того, что вначале создаются 3 Pod с обновленной версией приложения, а затем старые версии приложения завершаются

  ```bash
  kubectl get pods -l app=paymentservice -w
  ```

- Проверка реализации Reverse Rolling Update может быть выполнена аналогичным образом с применением соответствующего манифеста paymentservice-deployment-reverse.yaml и проверкой того, что Pods обновляются последовательно one by one.

  ```bash
  kubectl get pods -l app=paymentservice -w
  ```

- Убедиться в наличии Probes и результатов их работы можно с помощью команды:

  ```bash
  kubectl describe pod <pod_name>
  ```

  где **pod_name** имя любого Pod контролируемого Deployment

- Успешный запуск и работу Node Exporter на всех узлах кластера можно проверить с помощью команд:

  ```bash
  kubectl get pods -n monitoring -o wide
  kubectl port-forward -n monitoring <any_node-exporter_pod> 9100:9100
  curl localhost:9100/metrics
  ```

  где **any_node-exporter_pod** любой из подов развернутых при помощи DaemonSet.

## PR checklist

- [x] Выставлен label с темой домашнего задания
