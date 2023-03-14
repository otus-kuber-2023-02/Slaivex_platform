# Выполнено ДЗ № 1

- [x] Основное ДЗ
- [x] Задание со *

## В процессе сделано

- Определены причины восстановления подов в namespace kube-system:
  - **coredns** - Запускается и управляется как ReplicaSet, соответственно если pod умирает, то он должен быть восстановлен в соответствии с декларативным описанием. В данном случае обязательно должен быть запущен один под:

    ```yaml
    Replicas:       1 current / 1 desired
    ```

  - **kube-proxy** - Запускается как DaemonSet. Согласно описанию Podы указанные в DaemonSet должны запускаться на всех узлах кластера, либо конкретных, которые соответствуют меткам в селекторе. В данном случае, под запускается после удаления т.к. на узле соответствующем условию в селекторе, не остается ни одного запущенного пода:

    ```yaml
    nodeSelector:
      kubernetes.io/os: linux
    ```

  - Все остальные поды (**kube-apiserver**, **kube-controller-manager-minikube**, **kube-scheduler-minikube**) пересоздаются так как livenessProbes завершаются с результатом Failure.

    ```yaml
    Events:
    Type     Reason     Age   From     Message
    ----     ------     ----  ----     -------
    Warning  Unhealthy  17m   kubelet  Liveness probe failed: Get "https://127.0.0.1:10259/healthz": dial tcp 127.0.0.1:10259: connect: connection refused
    Normal   Pulled     17m   kubelet  Container image "registry.k8s.io/kube-scheduler:v1.26.1" already present on machine
    Normal   Created    17m   kubelet  Created container kube-scheduler
    Normal   Started    17m   kubelet  Started container kube-scheduler
    ```

- Создал Dockerfile с описанием создания образа Web сервера nginx отображающим статическое содержимое папки app
- Создал манифест podа **web-pod.yaml**
  - Добавлен init контейнер
  - Проверен статус пода и обработка статической страницы.
- Создан исправленный манифест frontend микросервиса Hipster Shop

## Как запустить проект

- Применить манифест веб сервера и выполнить перенаправление портов с помощью команд:
  
  ```bash
  kubectl apply -f web-pod.yaml
  kubectl apply -f frontend-pod-healthy.yaml
  ```

## Как проверить работоспособность

- Для проверки работы пода с именем web выполнить:
  - Перенаправление портов

    ```bash
    kubectl port-forward --address 0.0.0.0 pod/web 8000:8000
    ```

  - Открыть ссылку <http://localhost:8000/index.html>

## PR checklist

- [x] Выставлен label с темой домашнего задания
