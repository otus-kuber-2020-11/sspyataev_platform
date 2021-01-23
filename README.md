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
