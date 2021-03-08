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
* Развёрнут HipsterShop в кластере GKE.
* Установлен EFK стэк и подкючён к приложению
* Развёрнут ingress-controller по одному на каждую ноду infra-pool
* Настроен мониторинг ElasticSearch через Prometheus Operator
* Построены визуализации для nginx и визуализации объединены в dashboard. Результат выгружен в файл export.ndjson
* В prometheus-operator.values.yaml добавлен datasource Loki в блок Grafana
* Построен dashboard в grafana для визуализации логов и метрик для nginx-controller.

Homework #10
* Развёрнут кластер k8s, собраны образы hipster shop и опубликованы в Docker hub. https://gitlab.com/sspyataev/microservices-demo.git
* Установлен и настроен FluxCD, проверено срабатывание функционала обновления Helm Releases как при обновлении образа, так и обновлении в репо
<details>
  <summary>строки, указывающие на механизмпроверки изменений в Helm chart</summary>
  ts=2021-03-08T07:14:22.388434602Z caller=release.go:289 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="running dry-run upgrade to compare with release version '2'" action=dry-run-compare
  ts=2021-03-08T07:14:22.39106354Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:22.395094899Z caller=helm.go:69 component=helm version=v3 info="resetting values to the chart's original version" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:22.709700634Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:22.787888469Z caller=helm.go:69 component=helm version=v3 info="dry run for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:22.816163062Z caller=release.go:311 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="no changes" phase=dry-run-compare
  ts=2021-03-08T07:14:37.48732674Z caller=release.go:79 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="starting sync run"
  ts=2021-03-08T07:14:37.801620453Z caller=release.go:353 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="running upgrade" action=upgrade
  ts=2021-03-08T07:14:37.835867071Z caller=helm.go:69 component=helm version=v3 info="preparing upgrade for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:37.842561582Z caller=helm.go:69 component=helm version=v3 info="resetting values to the chart's original version" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.1662845Z caller=helm.go:69 component=helm version=v3 info="performing update for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.230735389Z caller=helm.go:69 component=helm version=v3 info="creating upgraded release for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.243813322Z caller=helm.go:69 component=helm version=v3 info="checking 5 resources for changes" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.250561321Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Service \"frontend\"" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.261038757Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Deployment \"frontend\"" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.271116245Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for Gateway \"frontend-gateway\"" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.282010441Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for ServiceMonitor \"frontend\"" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.291557779Z caller=helm.go:69 component=helm version=v3 info="Looks like there are no changes for VirtualService \"frontend\"" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.300027058Z caller=helm.go:69 component=helm version=v3 info="updating status for upgraded release for frontend" targetNamespace=microservices-demo release=frontend
  ts=2021-03-08T07:14:38.339916155Z caller=release.go:364 component=release release=frontend targetNamespace=microservices-demo resource=microservices-demo:helmrelease/frontend helmVersion=v3 info="upgrade succeeded" revision=27e6286a0d29b81b6b2c939a74a82ebc7d79c446 phase=upgrade
</details>
* Установлен Flagger. Описан манифест для канареечного релиза
* Проверена работа canary релиза.
sergey@ubuntu-desktop  ~ kubectl get canaries -n microservices-demo
NAME       STATUS      WEIGHT   LASTTRANSITIONTIME
frontend   Succeeded   0        2021-03-08T08:58:56Z

<details>
  <summary>sergey@ubuntu-desktop  ~ kubectl describe canary frontend -n microservices-demo</summary>
  Name:         frontend
  Namespace:    microservices-demo
  Labels:       <none>
  Annotations:  helm.fluxcd.io/antecedent: microservices-demo:helmrelease/frontend
  API Version:  flagger.app/v1beta1
  Kind:         Canary
  Metadata:
    Creation Timestamp:  2021-03-08T09:32:13Z
    Generation:          1
    Resource Version:    18778
    Self Link:           /apis/flagger.app/v1beta1/namespaces/microservices-demo/canaries/frontend
    UID:                 540f68a3-1783-3533-b311-34ab884fcd89
  Spec:
    Analysis:
      Interval:    1m
      Max Weight:  100
      Metrics:
        Interval:               1m
        Name:                   request-success-rate
        Threshold:              99
      Step Weight:              50
      Threshold:                2
    Progress Deadline Seconds:  60
    Service:
      Gateways:
        frontend-gateway
      Hosts:
        frontend.35.238.83.199.xip.io
      Port:  80
      Retries:
        Attempts:         3
        Per Try Timeout:  1s
        Retry On:         gateway-error,connect-failure,refused-stream
      Target Port:        8080
      Traffic Policy:
        Tls:
          Mode:  DISABLE
    Target Ref:
      API Version:  apps/v1
      Kind:         Deployment
      Name:         frontend
  Status:
    Canary Weight:  0
    Conditions:
      Last Transition Time:  2021-03-08T09:58:22Z
      Last Update Time:      2021-03-08T09:58:22Z
      Message:               Canary analysis completed successfully, promotion finished.
      Reason:                Succeeded
      Status:                True
      Type:                  Promoted
    Failed Checks:           0
    Iterations:              0
    Last Applied Spec:       67794895c9
    Last Transition Time:    2021-03-08T09:58:22Z
    Phase:                   Succeeded
    Tracked Configs:
  Events:
    Type     Reason  Age                From     Message
    ----     ------  ----               ----     -------
    Warning  Synced  23m                flagger  frontend-primary.microservices-demo not ready: waiting for rollout to finish: observed deployment generation less then desired generation
    Normal   Synced  22m (x2 over 23m)  flagger  all the metrics providers are available!
    Normal   Synced  22m                flagger  Initialization done! frontend.microservices-demo
    Normal   Synced  8m2s               flagger  New revision detected! Scaling up frontend.microservices-demo
    Normal   Synced  7m2s               flagger  Starting canary analysis for frontend.microservices-demo
    Normal   Synced  7m2s               flagger  Advance frontend.microservices-demo canary weight 50
    Normal   Synced  6m2s               flagger  Advance frontend.microservices-demo canary weight 100
    Normal   Synced  5m2s               flagger  Copying frontend.microservices-demo template spec to frontend-primary.microservices-demo
    Normal   Synced  4m2s               flagger  Routing all traffic to primary
    Normal   Synced  3m2s               flagger  Promotion completed! Scaling down frontend.microservices-demo
</details>
