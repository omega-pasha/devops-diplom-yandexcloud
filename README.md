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

Для начала необходимо подготовить облачную инфраструктуру в ЯО при помощи [Terraform](https://www.terraform.io/).
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
  ╰─ terraform apply -auto-approve                                                                                                                                                                                  ─╯
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

  # yandex_compute_instance_group.master_nodes_group will be created
  + resource "yandex_compute_instance_group" "master_nodes_group" {
      + created_at          = (known after apply)
      + deletion_protection = false
      + folder_id           = "b1glodb3fcb408dgl593"
      + id                  = (known after apply)
      + instances           = (known after apply)
      + name                = "master-node-group-stage"
      + service_account_id  = "aje43v7g7g7b8jfa5no3"
      + status              = (known after apply)

      + allocation_policy {
          + zones = [
              + "ru-central1-a",
              + "ru-central1-b",
              + "ru-central1-c",
            ]
        }

      + deploy_policy {
          + max_creating     = 0
          + max_deleting     = 0
          + max_expansion    = 0
          + max_unavailable  = 1
          + startup_duration = 0
          + strategy         = (known after apply)
        }

      + instance_template {
          + hostname    = "master-stage-{instance.index}"
          + labels      = (known after apply)
          + metadata    = {
              + "user-data" = <<-EOT
                    #cloud-config
                    users:
                      - name: pavelpomorcev
                        groups: sudo
                        shell: /bin/bash
                        sudo: ['ALL=(ALL) NOPASSWD:ALL']
                        ssh_authorized_keys:
                          - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGarLYwHCEcdV8wGoc3DAdd0AMiWm2G9OSTmP583UWdb pavelpomorcev@MacBook-Air-Pavel.local
                EOT
            }
          + name        = "master-stage-{instance.index}"
          + platform_id = "standard-v3"

          + boot_disk {
              + device_name = (known after apply)
              + mode        = "READ_WRITE"

              + initialize_params {
                  + image_id    = "fd8oshj0osht8svg6rfs"
                  + size        = 30
                  + snapshot_id = (known after apply)
                  + type        = "network-hdd"
                }
            }

          + network_interface {
              + ip_address   = (known after apply)
              + ipv4         = true
              + ipv6         = false
              + ipv6_address = (known after apply)
              + nat          = true
              + network_id   = (known after apply)
              + subnet_ids   = (known after apply)
            }

          + resources {
              + core_fraction = 100
              + cores         = 2
              + memory        = 4
            }
        }

      + scale_policy {
          + fixed_scale {
              + size = 1
            }
        }
    }

  # yandex_compute_instance_group.worker_nodes_group will be created
  + resource "yandex_compute_instance_group" "worker_nodes_group" {
      + created_at          = (known after apply)
      + deletion_protection = false
      + folder_id           = "b1glodb3fcb408dgl593"
      + id                  = (known after apply)
      + instances           = (known after apply)
      + name                = "worker-node-group-stage"
      + service_account_id  = "aje43v7g7g7b8jfa5no3"
      + status              = (known after apply)

      + allocation_policy {
          + zones = [
              + "ru-central1-a",
              + "ru-central1-b",
              + "ru-central1-c",
            ]
        }

      + deploy_policy {
          + max_creating     = 0
          + max_deleting     = 0
          + max_expansion    = 0
          + max_unavailable  = 1
          + startup_duration = 0
          + strategy         = (known after apply)
        }

      + instance_template {
          + hostname    = "worker-stage-{instance.index}"
          + labels      = (known after apply)
          + metadata    = {
              + "user-data" = <<-EOT
                    #cloud-config
                    users:
                      - name: pavelpomorcev
                        groups: sudo
                        shell: /bin/bash
                        sudo: ['ALL=(ALL) NOPASSWD:ALL']
                        ssh_authorized_keys:
                          - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGarLYwHCEcdV8wGoc3DAdd0AMiWm2G9OSTmP583UWdb pavelpomorcev@MacBook-Air-Pavel.local
                EOT
            }
          + name        = "worker-stage-{instance.index}"
          + platform_id = "standard-v3"

          + boot_disk {
              + device_name = (known after apply)
              + mode        = "READ_WRITE"

              + initialize_params {
                  + image_id    = "fd8oshj0osht8svg6rfs"
                  + size        = 30
                  + snapshot_id = (known after apply)
                  + type        = "network-hdd"
                }
            }

          + network_interface {
              + ip_address   = (known after apply)
              + ipv4         = true
              + ipv6         = false
              + ipv6_address = (known after apply)
              + nat          = true
              + network_id   = (known after apply)
              + subnet_ids   = (known after apply)
            }

          + resources {
              + core_fraction = 100
              + cores         = 2
              + memory        = 4
            }
        }

      + scale_policy {
          + fixed_scale {
              + size = 2
            }
        }
    }

  # yandex_vpc_network.diploma_vpc will be created
  + resource "yandex_vpc_network" "diploma_vpc" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "diploma-vpc-stage"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_subnet.diploma_subnet_a will be created
  + resource "yandex_vpc_subnet" "diploma_subnet_a" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "diploma_net_a_stage"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.0.1.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

  # yandex_vpc_subnet.diploma_subnet_b will be created
  + resource "yandex_vpc_subnet" "diploma_subnet_b" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "diploma_net_b_stage"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.0.2.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-b"
    }

  # yandex_vpc_subnet.diploma_subnet_c will be created
  + resource "yandex_vpc_subnet" "diploma_subnet_c" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "diploma_net_c_stage"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.0.3.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-c"
    }

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

Outputs:
ansible_inventory = <<-EOT
        [masters]
        master1 ansible_host=158.160.4.113 ansible_user=root
        
        [workers]
        worker1 ansible_host=158.160.83.141 ansible_user=root
        worker2 ansible_host=51.250.45.252 ansible_user=root
        
        [all:vars]
        ansible_python_interpreter=/usr/bin/python3
    EOT
  ```
7. В случае использования [Terraform Cloud](https://app.terraform.io/) в качестве [backend](https://www.terraform.io/docs/language/settings/backends/index.html) убедитесь, что применение изменений успешно проходит, используя web-интерфейс Terraform cloud.

Ожидаемые результаты:

1. Terraform сконфигурирован и создание инфраструктуры посредством Terraform возможно без дополнительных ручных действий.
2. Полученная конфигурация инфраструктуры является предварительной, поэтому в ходе дальнейшего выполнения задания возможны изменения.

---
### Создание Kubernetes кластера

На этом этапе необходимо создать [Kubernetes](https://kubernetes.io/ru/docs/concepts/overview/what-is-kubernetes/) кластер на базе предварительно созданной инфраструктуры.   Требуется обеспечить доступ к ресурсам из Интернета.

Это можно сделать двумя способами:

1. Рекомендуемый вариант: самостоятельная установка Kubernetes кластера.  
   а. При помощи Terraform подготовить как минимум 3 виртуальных машины Compute Cloud для создания Kubernetes-кластера. Тип виртуальной машины следует выбрать самостоятельно с учётом требовании к производительности и стоимости. Если в дальнейшем поймете, что необходимо сменить тип инстанса, используйте Terraform для внесения изменений.  
   б. Подготовить [ansible](https://www.ansible.com/) конфигурации, можно воспользоваться, например [Kubespray](https://kubernetes.io/docs/setup/production-environment/tools/kubespray/)  
   в. Задеплоить Kubernetes на подготовленные ранее инстансы, в случае нехватки каких-либо ресурсов вы всегда можете создать их при помощи Terraform.
2. Альтернативный вариант: воспользуйтесь сервисом [Yandex Managed Service for Kubernetes](https://cloud.yandex.ru/services/managed-kubernetes)  
  а. С помощью terraform resource для [kubernetes](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_cluster) создать региональный мастер kubernetes с размещением нод в разных 3 подсетях      
  б. С помощью terraform resource для [kubernetes node group](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_node_group)
  
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
