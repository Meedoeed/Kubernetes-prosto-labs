# Лабораторная работа №2 - Deployment и ReplicaSet

Продолжаем серию **«Kubernetes просто лабы»**. В первой работе мы подняли кластер через kind и запустили отдельный Pod `hello-k8s`. Теперь посмотрим, что изменится, если вместо Pod создать Deployment.

**Задача лабы** - создать три реплики Nginx, найти управляющий ими ReplicaSet, удалить один Pod и убедиться, что Kubernetes создаст замену. Заодно попробуем масштабирование и выбор Pods по меткам.

Статья с подробными объяснениями: *«Kubernetes просто лабы, часть 2: создаём Deployment и проверяем, как Kubernetes управляет Pod»* (ссылку добавим после публикации).

## Что понадобится

- Docker, kind и kubectl, как в [первой лабораторной](https://habr.com/ru/articles/1089958/).
- Кластер `k8s-first-lab` (если его нет, создадим).
- Терминал и текстовый редактор.

## Быстрый старт

Проверьте кластер:

```bash
kind get clusters
```

Если `k8s-first-lab` отсутствует:

```bash
kind create cluster --name k8s-first-lab --wait 180s
```

Переключитесь на него и проверьте ноду:

```bash
kubectl config use-context kind-k8s-first-lab
kubectl get nodes
```

Если после первой лабы остался `hello-k8s`, уберём его:

```bash
kubectl delete pod hello-k8s --ignore-not-found
```

Из папки `labs/02-deployment/` примените манифест:

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/hello-deployment
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

В норме увидим один Deployment, один ReplicaSet и три готовых Pod. Имена ReplicaSet и Pod будут отличаться от примеров в статье - это ожидаемо.

## Что дальше

- [commands.md](commands.md) - все команды лабы в одном месте.
- [tasks.md](tasks.md) - самостоятельные задания.
- [solutions.md](solutions.md) - решения и объяснения. Лучше заглядывать после своих попыток.
- [deployment.yaml](deployment.yaml) - исходный манифест с `replicas: 3`.

> Важно: во время лабы мы будем менять число реплик. Исходный файл в репозитории оставлен с тремя репликами, чтобы можно было повторить эксперимент с начала.

## Очистка

```bash
kubectl delete -f deployment.yaml
```

Эта команда удаляет Deployment и его зависимые ресурсы. Сам kind-кластер можно оставить для следующих лабораторных. Если нужно удалить **весь кластер**:

```bash
kind delete cluster --name k8s-first-lab
```

## Документация

- [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [kubectl scale](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_scale/)
