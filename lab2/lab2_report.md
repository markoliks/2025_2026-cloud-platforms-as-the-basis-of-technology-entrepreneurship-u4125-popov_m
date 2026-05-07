# Lab 2

University: [ITMO University](https://itmo.ru)  
Faculty: [FICT](https://fict.itmo.ru)  
Course:  [Cloud platforms as the basis of technology entrepreneurship](https://itmo-ict-faculty.github.io/cloud-platforms-as-the-basis-of-technology-entrepreneurship/)   
Year: 2025/2026  
Author: Popov Mark  
Lab: Lab2  
Group: U4125 
Date of create: 07.05.2026   
Date of finished: -  
# Отчет по лабораторной работе №2
# Исследование Cloud Run

## Цель работы

Ознакомиться с работой сервиса Cloud Run в Google Cloud.

## Ход работы

### 1. Включение необходимых API

Перед созданием Cloud Run сервиса были включены необходимые API:

```bash
gcloud services enable run.googleapis.com artifactregistry.googleapis.com cloudbuild.googleapis.com
```
После выполнения команды сервисы были успешно активированы.
<img width="1215" height="519" alt="шаг3" src="https://github.com/user-attachments/assets/e8e7780a-f1f9-4cad-908c-1bed89a9ca4e" />

---

### 2. Создание Cloud Run сервиса

Для лабораторной работы был создан Cloud Run сервис с именем:
mpopov-run-lab2

Для деплоя использовался готовый контейнер Hello от Google Cloud Run:
```bash
gcloud run deploy mpopov-run-lab2 \
  --image=us-docker.pkg.dev/cloudrun/container/hello \
  --region=europe-west1 \
  --platform=managed \
  --allow-unauthenticated \
  --memory=128Mi \
  --cpu=1 \
  --min-instances=0 \
  --max-instances=1
```
В результате был создан сервис mpopov-run-lab2 в регионе europe-west1.

Service URL:
https://mpopov-run-lab2-307056602443.europe-west1.run.app

---

### 3. Проверка работы сервиса

После деплоя был открыт URL созданного Cloud Run сервиса.

В браузере отобразилась стандартная страница Cloud Run с сообщением:
It's running!

Это означает, что контейнер успешно развернут и сервис принимает HTTP-запросы.

<img width="1249" height="936" alt="шаг4" src="https://github.com/user-attachments/assets/c05951f7-052d-4d8e-bf52-f88d72f23c28" />


---

### 4. Просмотр логов Cloud Run

Для просмотра логов сервиса была выполнена команда:
```bash
gcloud run services logs read mpopov-run-lab2 \
  --region=europe-west1 \
  --limit=10
```
В логах были видны успешные HTTP-запросы к сервису со статусом GET 200.

<img width="1237" height="190" alt="логи шаг5" src="https://github.com/user-attachments/assets/5be83133-796f-464b-acbe-11dda1c269d8" />

---

### 5. Изменение порта сервиса

Далее был изменен порт Cloud Run сервиса на 8090:
```bash
gcloud run services update mpopov-run-lab2 \
  --region=europe-west1 \
  --port=8090
```
После изменения порта была создана новая revision:
mpopov-run-lab2-00002-x2j

После обновления сервис был снова открыт в браузере. Страница продолжила корректно работать.

Это связано с тем, что контейнер Cloud Run использует переменную окружения PORT, которую Cloud Run передает приложению автоматически.

<img width="1229" height="943" alt="шаг смена порта" src="https://github.com/user-attachments/assets/a719a471-0915-4c35-9185-d6e8b7b08fbe" />


---

### 6. Просмотр revisions

Для просмотра созданных revisions была выполнена команда:
```bash
gcloud run revisions list \
  --service=mpopov-run-lab2 \
  --region=europe-west1
```
В результате были отображены две версии сервиса:
mpopov-run-lab2-00001-jj2
mpopov-run-lab2-00002-x2j

Первая revision была создана при первоначальном деплое, вторая — после изменения порта на 8090.

<img width="882" height="303" alt="версии" src="https://github.com/user-attachments/assets/28e5836d-aad6-4625-a7b0-3022be2268e6" />


---

### 7. Переключение трафика между revisions

Для проверки работы с версиями Cloud Run трафик был переключен на первую revision:
```bash
gcloud run services update-traffic mpopov-run-lab2 \
  --region=europe-west1 \
  --to-revisions=mpopov-run-lab2-00001-jj2=100
```
После этого была выполнена проверка распределения трафика:
```bash
gcloud run services describe mpopov-run-lab2 \
  --region=europe-west1 \
  --format="table(status.traffic.revisionName,status.traffic.percent)"
```
Затем трафик был возвращен на актуальную revision:
```bash
gcloud run services update-traffic mpopov-run-lab2 \
  --region=europe-west1 \
  --to-revisions=mpopov-run-lab2-00002-x2j=100
```
Таким образом была проверена возможность переключения трафика между разными версиями Cloud Run сервиса.

<img width="1098" height="119" alt="шаг 9" src="https://github.com/user-attachments/assets/5abafd2a-01ec-4f52-be80-d8ba56771723" />


<img width="1101" height="247" alt="трафик шаг8" src="https://github.com/user-attachments/assets/e5c05999-7c62-4491-ae0f-da5a34acd6c4" />


---

### 8. Удаление Cloud Run сервиса

После завершения лабораторной работы созданный Cloud Run сервис был удален:
```bash
gcloud run services delete mpopov-run-lab2 \
  --region=europe-west1
```
  После удаления была выполнена проверка списка Cloud Run сервисов:
```bash
gcloud run services list --region=europe-west1
```
Сервис mpopov-run-lab2 отсутствовал в списке, что подтверждает его успешное удаление.

<img width="1167" height="149" alt="удаление" src="https://github.com/user-attachments/assets/ec8a6170-dc2c-454e-b356-c8301e22d130" />


---

## Вывод

В ходе лабораторной работы был изучен сервис Cloud Run в Google Cloud.

Был создан Cloud Run сервис mpopov-run-lab2 на основе готового контейнера Hello. После деплоя сервис был проверен в браузере, где успешно отобразилась стандартная страница Cloud Run.

Также были просмотрены логи сервиса, в которых отображались успешные HTTP-запросы со статусом GET 200.

После этого был изменен порт сервиса на 8090, в результате чего Cloud Run создал новую revision. Сервис продолжил работать корректно, так как Cloud Run передает приложению порт через переменную окружения PORT.

Дополнительно были изучены revisions и выполнено переключение трафика между двумя версиями сервиса. В конце работы созданный Cloud Run сервис был удален.
