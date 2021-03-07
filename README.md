# sspyataev_platform
sspyataev Platform repository

Homework #1
Было сделано:
* Установлен minikube
* Написан манифест для пода и запущен под в кластере minikube
* В манифест добавлен init контейнер
* Собран и запущен под с фронтом приложения Hipster Shop
* Обнаружена и исправлена ошибка запуска фронта. Создан корректный манифест для запуска. Был добавлен блок env.


Вопросы.
"Разберитесь почему все pod в namespace kube-system восстановились после удаления."

etcd, kube-apiserver, kube-controller-manager, kube-scheduler - static pods, управляются демоном kubelet без необходимости взаимодействия с API. Конфигурация запуска описана в манифестах /etc/kubernetes/manifests

для coredns описан deployment, восстанавливается через ReplicaSet

```
kubectl get deployments --all-namespaces  
NAMESPACE     NAME      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   coredns   1/1     1            1           95m

kubectl get rs --all-namespaces
NAMESPACE     NAME                DESIRED   CURRENT   READY   AGE
kube-system   coredns-f9fd979d6   1         1         1       110m
```

kube-proxy - восстанавливается через DaemonSet

```
kubectl get ds --all-namespaces
NAMESPACE     NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   110m
```

Homework #2
Было сделано:
* Установлен kind и поднят тестовый кластер
* Создан и изучен ReplicaSet для frontend (скалирование, обновление)
* Собран сервис paymentService
* Создан и изучен Deployment для paymentService (обновление, стратегии обновления)
* Изучены probes. Добавлена readinessProbe для frontend.
* Рассмотрен контроллер DaemonSet

Homework #3
Было сделано:
* Изучена авторизация в kubernetes на основе RBAC.
* Созданы манифесты для создания объектов в kubernetes.
* Созданы манифесты для управления доступом к namespaces и к кластеру в целом.

Homework #4
Было сделано:
* Рассмотрены различные варианты сетевого взаимодействия с приложением в кластере
* Написаны манифесты для ClusterIP, LoadBalancer
* Рассмотрена работа Ingress

Homework #5
Было сделано:
* Рассмотрено создание локального S3 хранилища MinIO
* Развёрнут StatefulSet MinIO
* Создан Headless Service minio и проверена работа MinIO

Homework #6
Было сделано:
* Регистрация в GCP, разворачивание кластера
* Установка Helm, установка charts nginx-ingress, cert-manager, chartmuseum, harbor
* Создан кастомный helm chart для hipster-shop
* Рассмотрены подходы к шаблонизации манифестов Kubecfg, Kustomize

Homework #7
Было сделано:
* Рассмотрено создание Custom Resource и Custom Resource Definition
* Рассмотрена создание оператора. Опубликован кастомный оператор в докерхаб
* Задеплоен оператор и проверена его работа
* kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           6s         26m
restore-mysql-instance-job   1/1           55m        55m
* kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+

Homework #8
Было сделано:
* Создан кастомный образ nginx, который по пути /basic_status отдаёт stub_status, опубликован в DockerHub
* Описаны манифесты для запуска кастомного образа в k8s в количестве трёх реплик (Deployment & Service)
* Описаны манифесты для nginx exporter, который преобразует информацию из stub_status в prom-like формат (Deployment & Service)
* Добавлены CRD для prometheus-operator и создан ServiceMonitor
* Запущен prometheus server и Grafana для проверки корректности работы мониторинга
* Скриншот из grafana для nginx-exporter:
![Снимок экрана от 2021-02-07 15-25-30](https://user-images.githubusercontent.com/26296907/107145343-42958380-695a-11eb-9070-b68136e1465a.png)

Homework #9
* Развёрнут HipsterShop в кластере GKE
* Установлен EFK стэк и подкючён к приложению
* Развёрнут ingress-controller по одному на каждую ноду infra-pool
* Настроен мониторинг ElasticSearch через Prometheus Operator
* Построены визуализации для nginx и визуализации объединены в dashboard. Результат выгружен в файл export.ndjson
* В prometheus-operator.values.yaml добавлен datasource Loki в блок Grafana
* Построен dashboard в grafana для визуализации логов и метрик для nginx-controller.
