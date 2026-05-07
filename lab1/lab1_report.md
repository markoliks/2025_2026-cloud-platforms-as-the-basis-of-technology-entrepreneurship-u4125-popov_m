# Lab 1

University: [ITMO University](https://itmo.ru)  
Faculty: [FICT](https://fict.itmo.ru)  
Course:  Vibe Coding: AI-боты для бизнеса
Year: 2025/2026  
Author: Popov Mark  
Lab: Lab2  
Group: U4125
Date of create: 09.04.2026  
Date of finished: -  
# Отчет по лабораторной работе №1
  
# Обзор Google Cloud и исследование основных сервисов

## Цель работы

Ознакомиться с основными возможностями и преимуществами облачной платформы Google Cloud.

В рамках лабораторной работы были изучены сервисы IAM, Compute Engine и Cloud Storage, а также проверено влияние IAM-ролей service account на доступ к объектам в Cloud Storage.

## Ход работы

### 1. Проверка проекта Google Cloud

В начале работы был открыт Google Cloud Console и выбран учебный проект:

```text
cloud-platforms-as-the-basis
Для проверки активного проекта в Cloud Shell была выполнена команда:
gcloud config get-value project
Результат:
cloud-platforms-as-the-basis


⸻


2. Создание service account
Был создан service account с именем:
mpopov-sa-lab1
Команда создания:
gcloud iam service-accounts create mpopov-sa-lab1 \
  --display-name="Mark Popov Lab1 Service Account"
После создания service account был проверен командой:
gcloud iam service-accounts list --filter="email:mpopov-sa-lab1"
Результат:
DISPLAY NAME: Mark Popov Lab1 Service Account
EMAIL: mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com
DISABLED: False


⸻


3. Назначение роли Storage Admin
Созданному service account была назначена роль Storage Admin.
Команда:
gcloud projects add-iam-policy-binding cloud-platforms-as-the-basis \
  --member="serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com" \
  --role="roles/storage.admin"
Для проверки была выполнена команда:
gcloud projects get-iam-policy cloud-platforms-as-the-basis \
  --flatten="bindings[].members" \
  --filter="bindings.members:mpopov-sa-lab1 AND bindings.role:roles/storage.admin" \
  --format="table(bindings.role, bindings.members)"
Результат:
ROLE: roles/storage.admin
MEMBERS: serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com


⸻


4. Создание виртуальной машины Compute Engine
Была создана виртуальная машина с именем:
mpopov-vm-lab1
Параметры виртуальной машины:
Zone: europe-west1-b
Machine type: e2-micro
Provisioning model: SPOT
Image family: debian-12
Service account: mpopov-sa-lab1
Команда создания:
gcloud compute instances create mpopov-vm-lab1 \
  --zone=europe-west1-b \
  --machine-type=e2-micro \
  --provisioning-model=SPOT \
  --instance-termination-action=STOP \
  --service-account=mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com \
  --scopes=https://www.googleapis.com/auth/cloud-platform \
  --image-family=debian-12 \
  --image-project=debian-cloud
После создания виртуальная машина была проверена командой:
gcloud compute instances list --filter="name=mpopov-vm-lab1"
Результат:
NAME: mpopov-vm-lab1
ZONE: europe-west1-b
MACHINE_TYPE: e2-micro
PREEMPTIBLE: true
STATUS: RUNNING


⸻


5. Подключение к виртуальной машине
Для подключения к виртуальной машине была использована команда:
gcloud compute ssh mpopov-vm-lab1 --zone=europe-west1-b
После подключения была выполнена проверка имени хоста:
hostname
Результат:
mpopov-vm-lab1
Также была проверена учетная запись, от имени которой выполняются команды внутри VM:
gcloud config get-value account
Результат:
mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com
Дополнительно была выполнена команда:
gcloud auth list
Результат показал, что активной учетной записью является созданный service account:
ACTIVE  ACCOUNT
*       mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com


⸻


Конечно. Вот текст с 6 пункта и до конца:
### 6. Копирование файлов из Cloud Storage при роли Storage Admin

Внутри виртуальной машины был проверен доступ к bucket:

```bash
gsutil ls gs://lab1-bucket-itmo
Результат:
gs://lab1-bucket-itmo/pic1.jpg
gs://lab1-bucket-itmo/pic2.jpg
gs://lab1-bucket-itmo/pic3.jpeg
Затем была создана локальная папка:
mkdir lab1-files
После этого файлы были скопированы из bucket на виртуальную машину:
gsutil cp gs://lab1-bucket-itmo/* ./lab1-files/
Результат:
Copying gs://lab1-bucket-itmo/pic1.jpg...
Copying gs://lab1-bucket-itmo/pic2.jpg...
Copying gs://lab1-bucket-itmo/pic3.jpeg...
Operation completed over 3 objects/1.5 MiB.
Проверка содержимого папки:
ls -la lab1-files
Результат:
pic1.jpg
pic2.jpg
pic3.jpeg
Таким образом, при наличии роли Storage Admin service account успешно получил доступ к bucket и смог скопировать файлы на виртуальную машину.


⸻


7. Изменение роли service account
После успешного копирования файлов роль Storage Admin была удалена у service account.
Команда:
gcloud projects remove-iam-policy-binding cloud-platforms-as-the-basis \
  --member="serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com" \
  --role="roles/storage.admin"
Затем service account была назначена роль Compute Viewer:
gcloud projects add-iam-policy-binding cloud-platforms-as-the-basis \
  --member="serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com" \
  --role="roles/compute.viewer"
Для проверки новой роли была выполнена команда:
gcloud projects get-iam-policy cloud-platforms-as-the-basis \
  --flatten="bindings[].members" \
  --filter="bindings.members:mpopov-sa-lab1 AND bindings.role:roles/compute.viewer" \
  --format="table(bindings.role, bindings.members)"
Результат:
ROLE: roles/compute.viewer
MEMBERS: serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com


⸻


8. Повторное копирование файлов после смены роли
После смены роли было выполнено повторное подключение к виртуальной машине:
gcloud compute ssh mpopov-vm-lab1 --zone=europe-west1-b
На виртуальной машине была создана новая папка:
mkdir lab1-files-after-role-change
После этого была выполнена повторная проверка bucket:
gsutil ls gs://lab1-bucket-itmo
Результат:
gs://lab1-bucket-itmo/pic1.jpg
gs://lab1-bucket-itmo/pic2.jpg
gs://lab1-bucket-itmo/pic3.jpeg
Затем была выполнена команда копирования:
gsutil cp gs://lab1-bucket-itmo/* ./lab1-files-after-role-change/
Результат:
Copying gs://lab1-bucket-itmo/pic1.jpg...
Copying gs://lab1-bucket-itmo/pic2.jpg...
Copying gs://lab1-bucket-itmo/pic3.jpeg...
Operation completed over 3 objects/1.5 MiB.
Так как копирование после смены роли тоже завершилось успешно, была выполнена дополнительная проверка ролей service account:
gcloud projects get-iam-policy cloud-platforms-as-the-basis \
  --flatten="bindings[].members" \
  --filter="bindings.members:mpopov-sa-lab1" \
  --format="table(bindings.role, bindings.members)"
Результат:
ROLE: roles/compute.viewer
MEMBERS: serviceAccount:mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com
Это подтвердило, что роль Storage Admin действительно была удалена, а у service account осталась роль Compute Viewer.
Также была проверена IAM policy bucket:
gcloud storage buckets get-iam-policy gs://lab1-bucket-itmo
В результате была найдена следующая запись:
members:
- allUsers
role: roles/storage.objectViewer
Это означает, что объекты в bucket доступны для чтения всем пользователям. Поэтому после смены роли service account с Storage Admin на Compute Viewer доступ к объектам Cloud Storage не был потерян.


⸻


9. Удаление созданных ресурсов
После завершения лабораторной работы была удалена виртуальная машина:
gcloud compute instances delete mpopov-vm-lab1 --zone=europe-west1-b
Проверка удаления:
gcloud compute instances list --filter="name=mpopov-vm-lab1"
Результат:
Listed 0 items.
Затем был удален service account:
gcloud iam service-accounts delete mpopov-sa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com
Проверка удаления:
gcloud iam service-accounts list --filter="email:mpopov-sa-lab1"
Результат:
Listed 0 items.


⸻


Вывод
В ходе лабораторной работы были изучены базовые сервисы Google Cloud: IAM, Compute Engine и Cloud Storage.
Был создан service account mpopov-sa-lab1, которому сначала была назначена роль Storage Admin. Затем была создана виртуальная машина mpopov-vm-lab1 типа e2-micro в режиме Spot. Виртуальная машина была запущена с использованием созданного service account.
При наличии роли Storage Admin удалось получить список объектов bucket lab1-bucket-itmo и скопировать три файла на виртуальную машину с помощью утилиты gsutil.
После этого роль Storage Admin была удалена, а вместо нее была назначена роль Compute Viewer. Ожидалось, что после смены роли service account потеряет доступ к объектам Cloud Storage, так как роль Compute Viewer относится к просмотру ресурсов Compute Engine и не предоставляет прав на чтение объектов Cloud Storage.
Однако повторное выполнение команд gsutil ls и gsutil cp также завершилось успешно. Для поиска причины была проверена IAM policy bucket lab1-bucket-itmo. В политике доступа была обнаружена запись:
members:
- allUsers
role: roles/storage.objectViewer
Это означает, что bucket доступен для чтения всем пользователям. Следовательно, доступ к объектам bucket предоставлялся не только через роль Storage Admin, назначенную service account, но и через публичную политику bucket. Именно поэтому после смены роли на Compute Viewer копирование файлов продолжило работать.
В конце лабораторной работы все созданные ресурсы — виртуальная машина и service account — были удалены.
