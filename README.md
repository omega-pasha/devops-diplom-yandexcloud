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
terraform destroy                                                                                                                                                                                       
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
  terraform apply -auto-approve                                                                                                                                                                          
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
  
Результат:
![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2021.39.14.png)

---
### Создание тестового приложения

Создал html с картинкой и намеком на css, положил в [репозиторий](https://github.com/omega-pasha/diploma/tree/main/docker). Подготовил [Dockerfile](https://github.com/omega-pasha/diploma/blob/main/docker/Dockerfile) для создания образа приложения. Положил образ на [DockerHub](https://hub.docker.com/repository/docker/pomortsevpavel/diploma/general)

---
### Подготовка cистемы мониторинга и деплой приложения

Задеплоил в кластер [kube-prometheus](https://github.com/prometheus-operator/kube-prometheus), чтобы получить доступ извне, добавил ещё [манифест](https://github.com/omega-pasha/diploma/blob/main/monitoring_service.yaml)

![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2021.53.13.png)

Задеплоил приложение, с помощью манифестов:
![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2022.04.39.png)

Проверил, что сервер отвечает:
![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2022.02.40.png)

---
### Установка и настройка CI/CD

Осталось настроить ci/cd систему для автоматической сборки docker image и деплоя приложения при изменении кода.

Использовал Gitlab. Настроил shell runner. Подготовил [ci/cd](https://github.com/omega-pasha/diploma/blob/main/.gitlab-ci.yml). Приложение собирается в контейнер из Dockerfile  
и пушится в DockerHub. Деплой собирает все манифесты в один .template, в нем есть переменная окружения $DOCKER_IMAGE_NAME, чтобы получить её значение - пишем команду  
`envsubst < config.yaml.template > config.yaml` и применяем манифест.

![](https://github.com/omega-pasha/devops-diplom-yandexcloud/blob/main/snapshots/Снимок%20экрана%202023-10-03%20в%2022.19.17.png)

Ссылки:  
[GitHib](https://github.com/omega-pasha/diploma)  
[Приложение](http://51.250.45.252:30080)  
[Grafana](http://51.250.45.252:32000/)

