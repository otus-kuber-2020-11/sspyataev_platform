# sspyataev_platform
sspyataev Platform repository

Homework #1
Было сделано:
* Установлен minikube
* Написан манифест для пода и запущен под в кластере minikube
* В манифест добавлен init контейнер
* Собран и запущен под с фронтом приложения Hipster Shop
* Обнаружена и исправлена ошибка запуска фронта. Создан корректный манифест для запуска.


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
