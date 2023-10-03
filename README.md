# Дипломный практикум в Yandex.Cloud
  * [Этапы выполнения:](#этапы-выполнения)
     * [Создание облачной инфраструктуры](#создание-облачной-инфраструктуры)
     * [Создание Kubernetes кластера](#создание-kubernetes-кластера)
     * [Создание тестового приложения](#создание-тестового-приложения)
     * [Подготовка cистемы мониторинга и деплой приложения](#подготовка-cистемы-мониторинга-и-деплой-приложения)
     * [Установка и настройка CI/CD](#установка-и-настройка-cicd)
  * [Что необходимо для сдачи задания?](#что-необходимо-для-сдачи-задания)
  * [Как правильно задавать вопросы дипломному руководителю?](#как-правильно-задавать-вопросы-дипломному-руководителю)

---
## Этапы выполнения:


### Создание облачной инфраструктуры

Для начала необходимо подготовить облачную инфраструктуру в ЯО при помощи.
Предварительная подготовка к установке и запуску Kubernetes кластера.

1. Создал сервисный аккаунт, который будет в дальнейшем использоваться Terraform для работы с инфраструктурой с необходимыми и достаточными правами. Создал новый ключ RSA_2048 и выгрузил креденшелы в виде json-файла
   ![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2020.39.02.png)
2. Подготовил backend для Terraform с помощью Terraform Cloud:
   ![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2020.56.29.png)
3. Настроил 2 workspace, но по факту использовал только stage.
4. Создал VPC с подсетями в разных зонах доступности.
   
   [Репозиторий с terraform манифестами](https://github.com/omega-pasha/diploma/tree/main/terraform)
   
5. Убедился, что теперь могу выполнить команды `terraform destroy` и `terraform apply` без дополнительных ручных действий.
  ```
terraform destroy                                                                                                                                                                                              ─╯
Running apply in Terraform Cloud. Output will stream here. Pressing Ctrl-C
will cancel the remote apply if it's still pending. If the apply started it
will stop streaming the logs, but will not stop the apply running remotely.

Preparing the remote apply...

To view this run in a browser, visit:
https://app.terraform.io/app/netology_diploma/stage/runs/run-yxxsRWCGWGhHi9QA

Waiting for the plan to start...

Terraform v1.5.7
on linux_amd64
Initializing plugins and modules...
yandex_vpc_network.diploma_vpc: Refreshing state... [id=enplshd2kv9lp46lghpo]
yandex_vpc_subnet.diploma_subnet_a: Refreshing state... [id=e9b49fs08omb7b6e0q08]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

.....

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources in workspace "stage"?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

yandex_vpc_subnet.diploma_subnet_a: Destroying... [id=e9b49fs08omb7b6e0q08]
yandex_vpc_subnet.diploma_subnet_a: Destruction complete after 6s
yandex_vpc_network.diploma_vpc: Destroying... [id=enplshd2kv9lp46lghpo]
yandex_vpc_network.diploma_vpc: Destruction complete after 1s

Apply complete! Resources: 0 added, 0 changed, 2 destroyed.
  ```
  ```
  terraform apply -auto-approve                                                                                                                                                                                  ─╯
Running apply in Terraform Cloud. Output will stream here. Pressing Ctrl-C
will cancel the remote apply if it's still pending. If the apply started it
will stop streaming the logs, but will not stop the apply running remotely.

Preparing the remote apply...

To view this run in a browser, visit:
https://app.terraform.io/app/netology_diploma/stage/runs/run-SeFcGVx145yqbQdY

Waiting for the plan to start...

Terraform v1.5.7
on linux_amd64
Initializing plugins and modules...

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

.....

Plan: 6 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + ansible_inventory = (known after apply)

yandex_vpc_network.diploma_vpc: Creating...
yandex_vpc_network.diploma_vpc: Creation complete after 5s [id=enp65n1deiv07721ural]
yandex_vpc_subnet.diploma_subnet_a: Creating...
yandex_vpc_subnet.diploma_subnet_c: Creating...
yandex_vpc_subnet.diploma_subnet_b: Creating...
yandex_vpc_subnet.diploma_subnet_b: Creation complete after 1s [id=e2l4nki1dc2aks323e8e]
yandex_vpc_subnet.diploma_subnet_c: Creation complete after 2s [id=b0c65pj6568n87cfcadc]
yandex_vpc_subnet.diploma_subnet_a: Creation complete after 3s [id=e9bbkhgb4k3kj0mrub3n]
yandex_compute_instance_group.master_nodes_group: Creating...
yandex_compute_instance_group.worker_nodes_group: Creating...
yandex_compute_instance_group.worker_nodes_group: Still creating... [10s elapsed]
yandex_compute_instance_group.master_nodes_group: Still creating... [10s elapsed]
yandex_compute_instance_group.master_nodes_group: Still creating... [20s elapsed]
yandex_compute_instance_group.worker_nodes_group: Still creating... [20s elapsed]
yandex_compute_instance_group.master_nodes_group: Still creating... [30s elapsed]
yandex_compute_instance_group.worker_nodes_group: Still creating... [30s elapsed]
yandex_compute_instance_group.worker_nodes_group: Still creating... [40s elapsed]
yandex_compute_instance_group.master_nodes_group: Still creating... [40s elapsed]
yandex_compute_instance_group.master_nodes_group: Still creating... [50s elapsed]
yandex_compute_instance_group.worker_nodes_group: Still creating... [50s elapsed]
yandex_compute_instance_group.master_nodes_group: Creation complete after 51s [id=cl1hl7ft3kg5465breos]
yandex_compute_instance_group.worker_nodes_group: Creation complete after 55s [id=cl13ld5mjquch0vrfule]

Apply complete! Resources: 6 added, 0 changed, 0 destroyed.
  ```

6. Убедился, что применение изменений успешно проходит, используя web-интерфейс Terraform cloud.
   ![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2021.13.50.png)

---
### Создание Kubernetes кластера

На этом этапе необходимо создать кластер на базе предварительно созданной инфраструктуры.   Требуется обеспечить доступ к ресурсам из Интернета.

1. При помощи Terraform подготовил как минимум 3 виртуальных машины Compute Cloud для создания Kubernetes-кластера. Количество создавайемых ВМ определяется [переменными](https://github.com/omega-pasha/diploma/blob/main/terraform/variables.tf), которые вынесены в отдельный файл.  
Так же в отделные файлы вынес создание [Control-node](https://github.com/omega-pasha/diploma/blob/main/terraform/master-instanses.tf) и [Worker-node](https://github.com/omega-pasha/diploma/blob/main/terraform/worker-instances.tf). Написал манифест, для получения output по [шаблону](https://github.com/omega-pasha/diploma/blob/main/terraform/templates/inventory.tpl). С помощью него генерируется ansible inventory.
2. Подготовил ansible роль

[Ansible role, playbook](https://github.com/omega-pasha/diploma/tree/main/ansible)

Она разворачивает кластер с помощью kubeadm по этим [tasks](https://github.com/omega-pasha/diploma/blob/main/ansible/install_k8s_kubeadm/tasks/main.yml) 
  
Ожидаемый результат:

1. Работоспособный Kubernetes кластер.
2. В файле `~/.kube/config` находятся данные для доступа к кластеру.
3. Команда `kubectl get pods --all-namespaces` отрабатывает без ошибок.

---
### Создание тестового приложения

Для перехода к следующему этапу необходимо подготовить тестовое приложение, эмулирующее основное приложение разрабатываемое вашей компанией.

Способ подготовки:

1. Рекомендуемый вариант:  
   а. Создайте отдельный git репозиторий с простым nginx конфигом, который будет отдавать статические данные.  
   б. Подготовьте Dockerfile для создания образа приложения.  
2. Альтернативный вариант:  
   а. Используйте любой другой код, главное, чтобы был самостоятельно создан Dockerfile.

Ожидаемый результат:

1. Git репозиторий с тестовым приложением и Dockerfile.
2. Регистр с собранным docker image. В качестве регистра может быть DockerHub или [Yandex Container Registry](https://cloud.yandex.ru/services/container-registry), созданный также с помощью terraform.

---
### Подготовка cистемы мониторинга и деплой приложения

Уже должны быть готовы конфигурации для автоматического создания облачной инфраструктуры и поднятия Kubernetes кластера.  
Теперь необходимо подготовить конфигурационные файлы для настройки нашего Kubernetes кластера.

Цель:
1. Задеплоить в кластер [prometheus](https://prometheus.io/), [grafana](https://grafana.com/), [alertmanager](https://github.com/prometheus/alertmanager), [экспортер](https://github.com/prometheus/node_exporter) основных метрик Kubernetes.
2. Задеплоить тестовое приложение, например, [nginx](https://www.nginx.com/) сервер отдающий статическую страницу.

Рекомендуемый способ выполнения:
1. Воспользовать пакетом [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus), который уже включает в себя [Kubernetes оператор](https://operatorhub.io/) для [grafana](https://grafana.com/), [prometheus](https://prometheus.io/), [alertmanager](https://github.com/prometheus/alertmanager) и [node_exporter](https://github.com/prometheus/node_exporter). При желании можете собрать все эти приложения отдельно.
2. Для организации конфигурации использовать [qbec](https://qbec.io/), основанный на [jsonnet](https://jsonnet.org/). Обратите внимание на имеющиеся функции для интеграции helm конфигов и [helm charts](https://helm.sh/)
3. Если на первом этапе вы не воспользовались [Terraform Cloud](https://app.terraform.io/), то задеплойте в кластер [atlantis](https://www.runatlantis.io/) для отслеживания изменений инфраструктуры.

Альтернативный вариант:
1. Для организации конфигурации можно использовать [helm charts](https://helm.sh/)

Ожидаемый результат:
1. Git репозиторий с конфигурационными файлами для настройки Kubernetes.
2. Http доступ к web интерфейсу grafana.
3. Дашборды в grafana отображающие состояние Kubernetes кластера.
4. Http доступ к тестовому приложению.

---
### Установка и настройка CI/CD

Осталось настроить ci/cd систему для автоматической сборки docker image и деплоя приложения при изменении кода.

Цель:

1. Автоматическая сборка docker образа при коммите в репозиторий с тестовым приложением.
2. Автоматический деплой нового docker образа.

Можно использовать [teamcity](https://www.jetbrains.com/ru-ru/teamcity/), [jenkins](https://www.jenkins.io/), [GitLab CI](https://about.gitlab.com/stages-devops-lifecycle/continuous-integration/) или GitHub Actions.

Ожидаемый результат:

1. Интерфейс ci/cd сервиса доступен по http.
2. При любом коммите в репозиторие с тестовым приложением происходит сборка и отправка в регистр Docker образа.
3. При создании тега (например, v1.0.0) происходит сборка и отправка с соответствующим label в регистр, а также деплой соответствующего Docker образа в кластер Kubernetes.

---
## Что необходимо для сдачи задания?

1. Репозиторий с конфигурационными файлами Terraform и готовность продемонстрировать создание всех ресурсов с нуля.
2. Пример pull request с комментариями созданными atlantis'ом или снимки экрана из Terraform Cloud.
3. Репозиторий с конфигурацией ansible, если был выбран способ создания Kubernetes кластера при помощи ansible.
4. Репозиторий с Dockerfile тестового приложения и ссылка на собранный docker image.
5. Репозиторий с конфигурацией Kubernetes кластера.
6. Ссылка на тестовое приложение и веб интерфейс Grafana с данными доступа.
7. Все репозитории рекомендуется хранить на одном ресурсе (github, gitlab)

---
## Как правильно задавать вопросы дипломному руководителю?

Что поможет решить большинство частых проблем:

1. Попробовать найти ответ сначала самостоятельно в интернете или в 
  материалах курса и ДЗ и только после этого спрашивать у дипломного 
  руководителя. Навык поиска ответов пригодится вам в профессиональной 
  деятельности.
2. Если вопросов больше одного, присылайте их в виде нумерованного 
  списка. Так дипломному руководителю будет проще отвечать на каждый из 
  них.
3. При необходимости прикрепите к вопросу скриншоты и стрелочкой 
  покажите, где не получается.

Что может стать источником проблем:

1. Вопросы вида «Ничего не работает. Не запускается. Всё сломалось». 
  Дипломный руководитель не сможет ответить на такой вопрос без 
  дополнительных уточнений. Цените своё время и время других.
2. Откладывание выполнения курсового проекта на последний момент.
3. Ожидание моментального ответа на свой вопрос. Дипломные руководители - практикующие специалисты, которые занимаются, кроме преподавания, 
  своими проектами. Их время ограничено, поэтому постарайтесь задавать правильные вопросы, чтобы получать быстрые ответы :)
