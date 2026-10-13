# Решения и объяснения - лабораторная №2

Если уже попробовали задания самостоятельно - давайте сверим результаты. Имена Pods и ReplicaSet у вас будут своими.

## Задание 1. Все уровни

```bash
kubectl get deployments
kubectl get replicasets
kubectl get pods
```

После применения исходного манифеста обычно видим один Deployment `hello-deployment`, один ReplicaSet и три Pod. Deployment создаёт ReplicaSet, а ReplicaSet поддерживает Pods.

## Задание 2. Желаемое состояние

```bash
kubectl get deployment hello-deployment
kubectl get rs
```

У Deployment `READY` в норме будет `3/3`. У ReplicaSet - `DESIRED 3`, `CURRENT 3`, `READY 3`. Если Pods ещё запускаются, значения могут временно различаться.

## Задание 3. Удаление Pod

Первый терминал:

```bash
kubectl get pods -w
```

Второй терминал:

```bash
kubectl get pods
kubectl delete pod <имя-pod>
kubectl get pods
```

Вместо удалённого Pod ReplicaSet создаёт новый. Это **другой** Pod, с новым именем. Причина проста: желаемое число реплик по-прежнему равно трём.

## Задание 4. Масштабирование

В `deployment.yaml` меняем:

```yaml
replicas: 4
```

Применяем:

```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/hello-deployment
kubectl get deployments
```

После готовности увидим `4/4`.

Теперь:

```bash
kubectl scale deployment hello-deployment --replicas=2
kubectl get deployments
```

Deployment стремится к двум репликам. Но `kubectl scale` **не переписывает файл** `deployment.yaml`. В нём остаётся `replicas: 4`, пока вы сами не измените файл. Это полезный пример того, как конфигурация в кластере может разойтись с локальным YAML.

## Задание 5. Метки

```bash
kubectl get pods --show-labels
kubectl get pods -l app=hello-nginx
```

Полные имена Pods меняются при пересоздании. Метка `app=hello-nginx` остаётся в шаблоне Deployment и помогает выбирать нужную группу объектов.

## Задание со звёздочкой. Владельцы

```bash
kubectl get rs
kubectl describe rs <имя-replicaset>
kubectl get pods
kubectl describe pod <имя-pod>
```

Ищем:

```text
Controlled By: Deployment/hello-deployment
Controlled By: ReplicaSet/hello-deployment-...
```

Цепочка управления: `Deployment → ReplicaSet → Pod`. Это **не** схема прохождения сетевого трафика.

## Дополнительный эксперимент

При изменении шаблона Pod (например, поля `image`) Deployment обычно создаёт **новый ReplicaSet** и постепенно переводит реплики на новую версию. Посмотреть можно так:

```bash
kubectl get rs
kubectl rollout status deployment/hello-deployment
```

Важно: новый ReplicaSet появляется именно из-за изменения шаблона Pod, а не просто из-за изменения `replicas`.

## Главный вопрос

`kubectl run` из первой лабы создавал отдельный Pod. Если удалить такой Pod, ReplicaSet не будет восстанавливать его - ведь ReplicaSet там не было.

Deployment описывает приложение, его шаблон и желаемое число реплик. Он управляет ReplicaSet, а ReplicaSet следит за количеством Pods. Поэтому удаление одного Pod не отменяет наше требование иметь три.

## Вернуть всё к исходному состоянию

Верните в `deployment.yaml` `replicas: 3` и `image: nginx:stable-alpine`, затем:

```bash
kubectl apply -f deployment.yaml
```

Или удалите ресурсы лабораторной:

```bash
kubectl delete -f deployment.yaml
```
