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

Homework #11
* Развёрнуты consul и vault. 
```shell
helm status vault

NAME: vault
LAST DEPLOYED: Sat Mar 20 10:44:55 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://www.vaultproject.io/docs/


Your release is named vault. To learn more about the release, try:

  $ helm status vault
  $ helm get manifest vault
```
* Инициализирован vault:
Unseal Key 1: ymoo/MebLD9dQKPxrn6Ro589+dPUni70yXqZjMefFaU=

Initial Root Token: s.mtSCU08DKfoBiZauoG483Kwu

* Распечатали Vault на каждом поде.
```shell
kubectl exec -it vault-0 -- vault status 

Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         1.6.2
Storage Type    consul
Cluster Name    vault-cluster-6c4821e0
Cluster ID      a96ad73d-e89a-4d36-f5cd-9d697b8e9df3
HA Enabled      true
HA Cluster      https://vault-0.vault-internal:8201
HA Mode         active
```
* Залогинились в Vault 
```shell
kubectl exec -it vault-0 -- vault login:

Token (will be hidden): 
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.mtSCU08DKfoBiZauoG483Kwu
token_accessor       6irqxw6JbtrOOL3flWjo4Bms
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```
* Посмотрели список доступных авторизаций
```shell
kubectl exec -it vault-0 -- vault auth list:

Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_7e068722    token based credentials
```
```shell
* kubectl exec -it vault-0 -- vault read otus/otus-ro/config:

Key                 Value
---                 -----
refresh_interval    768h
password            asajkjkahs
username            otus
```
* Включили авторизацию черерз k8s. Теперь список доступных авторизаций выглядит так:
```shell
kubectl exec -it vault-0 -- vault auth list

Path           Type          Accessor                    Description
----           ----          --------                    -----------
kubernetes/    kubernetes    auth_kubernetes_3387993c    n/a
token/         token         auth_token_7e068722         token based credentials
```

* Создали политику и роль. Применили политику. Проверили политику))

Смогли записать otus-rw/config1, но не смогли otus-rw/config, потому что для otus-rw не было capabilities "update".

Чтобы обновить запись в otus-rw/config политика выглядит так:

```shell
path "otus/otus-rw/*" {
  capabilities = ["read", "create", "list", "update"]
}
```

* Рассмотрели Use case использования авторизации через кубер
```shell
  cat index.html

<html>
<body>
<p>Some secrets:</p>
<ul>
<li><pre>username: otus</pre></li>
<li><pre>password: asajkjkahs</pre></li>
</ul>

</body>
</html>
```
* Создали CA на базе vault

```shell
kubectl exec -it vault-0 -- vault write pki_int/issue/example-dot-ru common_name="gitlab.example.ru" ttl="24h"

Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUbeu3MlqgcdZevfzffDV0DKviWEswDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMTAzMjAxMTM5MDJaFw0yNjAz
MTkxMTM5MzJaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAK5ulOSXUsIR
rcAO/reEq5loOyovTnGQB6vWq2JBbv0UQnpdhUkpodDaAlu2eYSgXBM5ZWwAMSrO
yeUs+CgF5KEIESviLa2pj0Tii5H4DzB9MNUwiMLRzwjD2uSAmZ6HNLzWi6sylix6
KD1oJ7ZS7blQJ3mQZVGNeQ0Vzr1/DswxhKsGf2J2ECzA4jKv17C64jud4NDhvbkx
/MbBH13SN3mzWflRBlPOG8Y9HvuyUbPMVdJcGo5D37cOOPQHvEGoUMckfHMNpmG7
tt67LKonMpVKYruyknaCN+lj7QJNEkgYGiDEjrtxtJMkRhBOI9NN7AeBhPtpXul9
dprVwuD/5hUCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUj6F8h9QrJCqGwX10d4lW6UFgZW4wHwYDVR0jBBgwFoAU
RMdrzphhOLbfubRaESsYDUskHLYwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
E3AllWt06b5OC7ULhJL1j0trvfTPhF82o+3q4jWnhSDUuDebruq30lPiSDLMsdWz
RQ+nqYP/DJkzaZW8dVYva/SHXsUhdZ1tomdrWLjESB2Ef/yatZ3wxk2q13EnCj/L
kWnbN4rAfOm9djDZSqOefasjPYSAeNE4vWaxmPGVwRbNed7UCDySOsW8q3aN0iRG
0AvlRaQnVENWxOaSqc9b9UV/A6Ag/9kD89pEMmk9mna4LazBD+iuy6Jwy7Z6yDOr
UFENKvCzc/P3nUdMj/qzHNEDKQvEJXwK8VX/Y5sM5srwdCV2tR9sr/ZPMOs640om
pkhK1OLg/G2rrlof5XoTOg==
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZzCCAk+gAwIBAgIUJR88DPIE0UbidvQGpSfKGtxlX/IwDQYJKoZIhvcNAQEL
BQAwLDEqMCgGA1UEAxMhZXhhbXBsZS5ydSBJbnRlcm1lZGlhdGUgQXV0aG9yaXR5
MB4XDTIxMDMyMDExNDEzNFoXDTIxMDMyMTExNDIwM1owHDEaMBgGA1UEAxMRZ2l0
bGFiLmV4YW1wbGUucnUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDK
ku83/kg94SJ5lzUUJdwzqUHuUdOjulzRyScvpfIF9CuxUiBJBZxzapR9wg16MELB
HtoXllT552HJHGsbfW+jHAPSJ5nlDAzFk6iRdLr14mnF0xnzHov6OG53XCSnsnsd
rJxpruBJVwh+Oa6/6n2Hmn0LxOrnKt6y9qIx1JZek/5uI6Q1LHXe4WkwjtGm6+Y+
hrCxhHB9ePr5DEjJKU9NsNNihJyUbhL/d91Ul0mJvtZKuVXYMbNKCE0e6ov8xDuT
Wxc8PYYwur4SaJLxpU73EmwmyN9VKWrsmV5FGsOt4HDXnIMfiV7ViO4oyoKeH/ad
gleR8a59Dy9ru7ao9NkbAgMBAAGjgZAwgY0wDgYDVR0PAQH/BAQDAgOoMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQU2suSqQReMA+AWGOT
9MMHwWExfPswHwYDVR0jBBgwFoAUj6F8h9QrJCqGwX10d4lW6UFgZW4wHAYDVR0R
BBUwE4IRZ2l0bGFiLmV4YW1wbGUucnUwDQYJKoZIhvcNAQELBQADggEBAAlmbNDv
28KJeLfKrUCPWGAoOOdenbjvxARUOKOVoMewjKYzYsCHBF4aKbXgkZQb0UChmDQx
ZCT79TinBvjTx4k5u9D9SFO7P7uhnNpcYCEESB/Q40zE0BPQ7eeoMBbQX6ce9DUY
TDYN+Q8PHtj3KAie5yVqw27hyGNt0QZ+xgX9K2TPlIbzT64elqrws/GxiDFqgCNX
nARuo6h/rvUfAnTZPUnwc6mCKUKwqRGUpXKR0SBhJ8rAdsCy3mtmRtgJUDi/JQhE
oLC9ZLrbJqmXxk5U1gHS6xyaWdVFa0AqKa9Ycw9FwSCOR0MutywfvLX/Bz2z1Rm/
Zrc+hIAhXgn88oI=
-----END CERTIFICATE-----
expiration          1616326923
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUbeu3MlqgcdZevfzffDV0DKviWEswDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0yMTAzMjAxMTM5MDJaFw0yNjAz
MTkxMTM5MzJaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAK5ulOSXUsIR
rcAO/reEq5loOyovTnGQB6vWq2JBbv0UQnpdhUkpodDaAlu2eYSgXBM5ZWwAMSrO
yeUs+CgF5KEIESviLa2pj0Tii5H4DzB9MNUwiMLRzwjD2uSAmZ6HNLzWi6sylix6
KD1oJ7ZS7blQJ3mQZVGNeQ0Vzr1/DswxhKsGf2J2ECzA4jKv17C64jud4NDhvbkx
/MbBH13SN3mzWflRBlPOG8Y9HvuyUbPMVdJcGo5D37cOOPQHvEGoUMckfHMNpmG7
tt67LKonMpVKYruyknaCN+lj7QJNEkgYGiDEjrtxtJMkRhBOI9NN7AeBhPtpXul9
dprVwuD/5hUCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUj6F8h9QrJCqGwX10d4lW6UFgZW4wHwYDVR0jBBgwFoAU
RMdrzphhOLbfubRaESsYDUskHLYwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
E3AllWt06b5OC7ULhJL1j0trvfTPhF82o+3q4jWnhSDUuDebruq30lPiSDLMsdWz
RQ+nqYP/DJkzaZW8dVYva/SHXsUhdZ1tomdrWLjESB2Ef/yatZ3wxk2q13EnCj/L
kWnbN4rAfOm9djDZSqOefasjPYSAeNE4vWaxmPGVwRbNed7UCDySOsW8q3aN0iRG
0AvlRaQnVENWxOaSqc9b9UV/A6Ag/9kD89pEMmk9mna4LazBD+iuy6Jwy7Z6yDOr
UFENKvCzc/P3nUdMj/qzHNEDKQvEJXwK8VX/Y5sM5srwdCV2tR9sr/ZPMOs640om
pkhK1OLg/G2rrlof5XoTOg==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAypLvN/5IPeEieZc1FCXcM6lB7lHTo7pc0cknL6XyBfQrsVIg
SQWcc2qUfcINejBCwR7aF5ZU+edhyRxrG31voxwD0ieZ5QwMxZOokXS69eJpxdMZ
8x6L+jhud1wkp7J7Haycaa7gSVcIfjmuv+p9h5p9C8Tq5yresvaiMdSWXpP+biOk
NSx13uFpMI7RpuvmPoawsYRwfXj6+QxIySlPTbDTYoSclG4S/3fdVJdJib7WSrlV
2DGzSghNHuqL/MQ7k1sXPD2GMLq+EmiS8aVO9xJsJsjfVSlq7JleRRrDreBw15yD
H4le1YjuKMqCnh/2nYJXkfGufQ8va7u2qPTZGwIDAQABAoIBADMRu/E3z+qZuWFB
94WuzcbQYui8BEkAkKnqtlBS26MYnXNEqxL9sSV/txPFOjSVuh6Jsp3DroSaCpLy
8SWrB9vtEiGHDksqMIYW5aZV8VRP0i6nO6GJD+zzERZSSoNkgZlHjN8v0SdsI53+
2MlVSnRHREMVT8sbia1AdD9vwsDwxw9gfUmK7Mv4V+BnXhaejyjbY+VCFRlpYV4T
wPP5+r290+RpffiixvXJ/YSWfJldOr6iCl2909dfDGZxaCR5jqa/+redxppQE9Cd
nUgsx943xvgE5OmT+jINPXbJski9WdDWs633UZuFEsX2Ly6nfS0rhaA2XL2edP4p
v7UltjECgYEAzkMfdfzQCXRLK0B/zaoPnKK3j/K0Bb9dA4pWNq3h6q0ftpsqjSot
iohs4yKOKDEsdDAqu30JbEfrt5bMGlrNsZH0F9MGokh/MROHmOXqRnNy0f7Vndxn
I1RWM+KIXIo/LVfqXJqoxqm5vgjvw1rkfoj/mEAtpVnZMyupxbjmKP8CgYEA+2wh
lCxlX8JMjXGio7SFrT9MZ5ts0BWdXU05yffn75YNggQZiQOLAX/MTB8SAXXryOV1
84PH6HkoVR0OEpdelSgg+Imm+VmkSqMw9f1R2yF8oNJkSgRxhqA8O0YLRjjc8Jmw
CQHXJj4c/y1CBAOh69hB4ARTEOqDcsYGIui40+UCgYEAyq/ZLaeGg5Pc/h6+uEqC
VujrOzBDyVYYQA9j0w2h8Gu5u0bVVKz63aRcZAMj8MkJpw9iHqWradVvBBTScp+C
fBkx5WuAnF5jZsWLPSvJwPtX/JXQMvVQAL6yiv/0AgP0O0mmSuPMMJS+qsi7W5xo
5xMXH/UJJfCZ6JfimCKvQd8CgYEAkpJBKR6Qorik5DiA9irBW3RxWF01nEFdkgz5
SZLqdbPmgAtfz45vNRqJwT7DwnI6WM3ca3BB1Hb9WlEr6Q6xpwbT4dBttSPbMV/d
pSpe0/67pw3ARZ49iJxVQMDexbtUojcWdsnJ4ZOIWALMX4a2mMVj8fLTciMlKn3j
CereBTkCgYAb9HJZxAunveua1FnI/P0hawD8RKNwR9IdbNGjDDDKsS9ksC623GTB
iVD4gPvonL38cGIQYE3QIJ7zG7u+sF9r8FxMqDkDocTVdYqmi3MwJB6RgN8c+Wh6
/iiXewxJDBoeC6l/iI+GRoIpMAIXihWkZs+he052iq7kSjC9j9HOqQ==
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       25:1f:3c:0c:f2:04:d1:46:e2:76:f4:06:a5:27:ca:1a:dc:65:5f:f2
```

Homework #12 (#5)
Было сделано:
* Созданы CRD для CSI VolumeSnapshot
```shell
  $ kubectl apply -f snapshot.storage.k8s.io_volumesnapshotclasses.yaml
  $ kubectl apply -f snapshot.storage.k8s.io_volumesnapshotcontents.yaml
  $ kubectl apply -f snapshot.storage.k8s.io_volumesnapshots.yaml
```
* Создан snapshot controller
```shell
  $ kubectl apply -f rbac-snapshot-controller.yaml
  $ kubectl apply -f setup-snapshot-controller.yaml
```
* Установлен CSI driver HostPath по инструкции из https://github.com/kubernetes-csi/csi-driver-host-path/blob/master/docs/deploy-1.17-and-later.md и проверен deploy
```shell
  $ kubectl get pods
  NAME                              READY   STATUS      RESTARTS   AGE
  backup-mysql-instance-job-7v7dz   0/1     Completed   0          53d
  csi-hostpath-attacher-0           1/1     Running     0          6m22s
  csi-hostpath-provisioner-0        1/1     Running     0          6m19s
  csi-hostpath-resizer-0            1/1     Running     0          6m18s
  csi-hostpath-snapshotter-0        1/1     Running     0          6m17s
  csi-hostpath-socat-0              1/1     Running     0          6m16s
  csi-hostpathplugin-0              5/5     Running     0          6m20s
  mysql-operator-88f997b97-tzzss    1/1     Running     1          53d
```
* Установлен StorageClass, PVC, pod
```shell
  $ kubectl apply -f csi-storageclass.yaml
  $ kubectl apply -f csi-pvc.yaml
  $ kubectl apply -f csi-app.yaml
```
* Проверена корректность установки компонентов:
```shell
  $ kubectl get pv
  NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                               STORAGECLASS      REASON   AGE
  pvc-af4138bc-8872-412b-a568-19c551956c53   1Gi        RWO            Delete           Bound       default/storage-pvc                 csi-hostpath-sc            22m

  $ kubectl get pvc
  NAME                        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
  storage-pvc                 Bound    pvc-af4138bc-8872-412b-a568-19c551956c53   1Gi        RWO            csi-hostpath-sc   24m
```
* Убедились, что в приложении storage-pod примонитировался Hostpath volume
```shell
  $ kubectl describe pods/my-csi-app
  Name:         storage-pod
  Namespace:    default
  Priority:     0
  Node:         minikube/192.168.49.2
  Start Time:   Sun, 21 Mar 2021 13:28:26 +0400
  Labels:       <none>
  Annotations:  <none>
  Status:       Running
  IP:           172.17.0.12
  IPs:
    IP:  172.17.0.12
  Containers:
    my-frontend:
      Container ID:  docker://f7758e3f10b2480aeb33cb989059393a6f929cfecaf448694200646e07873854
      Image:         busybox
      Image ID:      docker-pullable://busybox@sha256:ce2360d5189a033012fbad1635e037be86f23b65cfd676b436d0931af390a2ac
      Port:          <none>
      Host Port:     <none>
      Command:
        sleep
        1000000
      State:          Running
        Started:      Sun, 21 Mar 2021 13:28:47 +0400
      Ready:          True
      Restart Count:  0
      Environment:    <none>
      Mounts:
        /data from my-csi-volume (rw)
        /var/run/secrets/kubernetes.io/serviceaccount from default-token-qj8rr (ro)
  Conditions:
    Type              Status
    Initialized       True 
    Ready             True 
    ContainersReady   True 
    PodScheduled      True 
  Volumes:
    my-csi-volume:
      Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
      ClaimName:  storage-pvc
      ReadOnly:   false
    default-token-qj8rr:
      Type:        Secret (a volume populated by a Secret)
      SecretName:  default-token-qj8rr
      Optional:    false
  QoS Class:       BestEffort
  Node-Selectors:  <none>
  Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                  node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
  Events:
    Type     Reason                   Age                  From                                      Message
    ----     ------                   ----                 ----                                      -------
    Normal   Scheduled                26m                  default-scheduler                         Successfully assigned default/storage-pod to minikube
    Warning  VolumeConditionAbnormal  26m (x10 over 26m)   csi-pv-monitor-agent-hostpath.csi.k8s.io  The volume isn't mounted
    Normal   SuccessfulAttachVolume   26m                  attachdetach-controller                   AttachVolume.Attach succeeded for volume "pvc-af4138bc-8872-412b-a568-19c551956c53"
    Normal   Pulling                  26m                  kubelet                                   Pulling image "busybox"
    Normal   Pulled                   26m                  kubelet                                   Successfully pulled image "busybox" in 10.408727233s
    Normal   Created                  26m                  kubelet                                   Created container my-frontend
    Normal   Started                  26m                  kubelet                                   Started container my-frontend
    Normal   VolumeConditionNormal    89s (x241 over 25m)  csi-pv-monitor-agent-hostpath.csi.k8s.io  The Volume returns to the healthy state
```
* Проверили работу Hostpath driver. Создали файл /data/hello-world в storage-pod
```shell
  $ kubectl exec -it storage-pod /bin/sh
  / # touch /data/hello-world
  / # exit

  $ kubectl exec -it $(kubectl get pods --selector app=csi-hostpathplugin -o jsonpath='{.items[*].metadata.name}') -c hostpath /bin/sh
  / # find / -name hello-world
  /var/lib/kubelet/pods/75ca26d8-fff8-4331-a0de-4dbd6e455625/volumes/kubernetes.io~csi/pvc-af4138bc-8872-412b-a568-19c551956c53/mount/hello-world
  /csi-data-dir/ca125560-8a27-11eb-b6ad-0242ac110009/hello-world
  / # exit
```
* Также, корректность работы драйвера можно проверить через VolumeAttachment API
```shell
  $ kubectl describe volumeattachment
  Name:         csi-5802467891ab103009f1f6a3f326a3eb7463c05314cd82de98eec7e5eb697829
  Namespace:    
  Labels:       <none>
  Annotations:  <none>
  API Version:  storage.k8s.io/v1
  Kind:         VolumeAttachment
  Metadata:
    Creation Timestamp:  2021-03-21T09:28:26Z
    Managed Fields:
      API Version:  storage.k8s.io/v1
      Fields Type:  FieldsV1
      fieldsV1:
        f:status:
          f:attached:
      Manager:      csi-attacher
      Operation:    Update
      Time:         2021-03-21T09:28:26Z
      API Version:  storage.k8s.io/v1
      Fields Type:  FieldsV1
      fieldsV1:
        f:spec:
          f:attacher:
          f:nodeName:
          f:source:
            f:persistentVolumeName:
      Manager:         kube-controller-manager
      Operation:       Update
      Time:            2021-03-21T09:28:26Z
    Resource Version:  11848
    UID:               048c0bca-372b-43cc-97ee-aca32da1f111
  Spec:
    Attacher:   hostpath.csi.k8s.io
    Node Name:  minikube
    Source:
      Persistent Volume Name:  pvc-af4138bc-8872-412b-a568-19c551956c53
  Status:
    Attached:  true
  Events:      <none>
```
