# Команды второй лабораторной

Небольшая шпаргалка. Выполняем команды в контексте `kind-k8s-first-lab`, из папки с `deployment.yaml`.

## Подготовка

```bash
kind get clusters
kubectl config use-context kind-k8s-first-lab
kubectl get nodes
kubectl delete pod hello-k8s --ignore-not-found
```

Если кластера нет:

```bash
kind create cluster --name k8s-first-lab --wait 180s
```

## Создание Deployment

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/hello-deployment
kubectl get deployments
kubectl get pods
```

## Проверяем ReplicaSet и владельцев

```bash
kubectl get replicasets
kubectl describe deployment hello-deployment
kubectl get rs
kubectl describe rs <имя-replicaset>
kubectl describe pod <имя-pod>
```

В командах с `<...>` замените подсказку настоящим именем из `kubectl get`.

## Удаляем Pod и наблюдаем

В одном терминале:

```bash
kubectl get pods -w
```

В другом:

```bash
kubectl get pods
kubectl delete pod <имя-pod>
kubectl get pods
```

Остановить наблюдение: `Ctrl+C`.

## Масштабируем

```bash
kubectl scale deployment hello-deployment --replicas=5
kubectl get deployments
kubectl get pods

kubectl scale deployment hello-deployment --replicas=2
kubectl get pods
```

## Меняем YAML

Измените в `deployment.yaml` значение `replicas: 3` на `replicas: 4`, сохраните и примените:

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/hello-deployment
kubectl get deployments
```

## Метки

```bash
kubectl get pods --show-labels
kubectl get pods -l app=hello-nginx
```

## Если что-то не получилось

```bash
kubectl get pods
kubectl describe pod <имя-pod>
kubectl logs <имя-pod>
kubectl describe deployment hello-deployment
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Очистка

```bash
kubectl delete -f deployment.yaml
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

Сам кластер удаляем только при необходимости:

```bash
kind delete cluster --name k8s-first-lab
```
