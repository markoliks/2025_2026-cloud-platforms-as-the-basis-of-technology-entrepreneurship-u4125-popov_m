University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Cloud platforms as the basis of technology entrepreneurship](https://itmo-ict-faculty.github.io/cloud-platforms-as-the-basis-of-technology-entrepreneurship/)  
Year: 2025/2026  
Group: U4125  
Author: Popov Mark Alexandrovich  
Lab: Lab3  
Date of create: 07.05.2026  
Date of finished:  

# Лабораторная работа №3  
# Исследование Cloud Storage

## Цель работы

Ознакомиться с основными понятиями и принципами работы облачного хранилища Google Cloud Storage, изучить загрузку файлов, работу с папками, настройку публичного доступа и удаление созданных ресурсов.

## Ход работы

### 1. Создание Cloud Storage bucket

Для лабораторной работы был выбран учебный проект:
cloud-platforms-as-the-basis

В Cloud Storage был создан bucket с именем:
mpopov-lab3-bucket

Bucket был создан в регионе europe-west1 с типом хранения Standard.

Команда создания bucket:
```bash
gcloud storage buckets create gs://mpopov-lab3-bucket \
  --location=europe-west1 \
  --uniform-bucket-level-access
```
После создания bucket был проверен командой:
```bash
gcloud storage buckets list | grep mpopov-lab3-bucket
```
<img width="1113" height="185" alt="создание шаг1" src="https://github.com/user-attachments/assets/6a424be4-79ef-4f0f-9a8b-11902ea7445e" />


---

### 2. Загрузка изображений в bucket

В созданный bucket были загружены 4 изображения:
1.1.jpg
1.2.jpg
1.3.jpg
1.4.jpg

Загрузка выполнялась через веб-интерфейс Google Cloud Console с помощью кнопки Upload files.

После загрузки все изображения отображались в списке объектов bucket.

<img width="1191" height="809" alt="загрузка файлов шаг2" src="https://github.com/user-attachments/assets/f1e2507c-6ec8-45eb-9b82-eac94f4aacf0" />


---

### 3. Создание папки и перемещение файлов

Внутри bucket была создана папка:
images

Перемещение я выполнил через Cloud Shell командами:
```bash
gcloud storage mv gs://mpopov-lab3-bucket/1.1.jpg gs://mpopov-lab3-bucket/images/
gcloud storage mv gs://mpopov-lab3-bucket/1.2.jpg gs://mpopov-lab3-bucket/images/
gcloud storage mv gs://mpopov-lab3-bucket/1.3.jpg gs://mpopov-lab3-bucket/images/
gcloud storage mv gs://mpopov-lab3-bucket/1.4.jpg gs://mpopov-lab3-bucket/images/
```
В результате изображения были размещены по пути:
gs://mpopov-lab3-bucket/images/

<img width="1577" height="727" alt="папка images шаг3" src="https://github.com/user-attachments/assets/dcfd83ff-1c76-4703-94fd-548d1065577e" />


---

### 4. Настройка публичного доступа

Для файлов bucket был настроен публичный доступ. Для этого была добавлена роль Storage Object Viewer для allUsers.

Команда:
```bash
gcloud storage buckets add-iam-policy-binding gs://mpopov-lab3-bucket \
  --member=allUsers \
  --role=roles/storage.objectViewer
```
При настройке доступа было обнаружено, что bucket сначала имел включенную настройку Public Access Prevention, из-за чего публичный доступ был заблокирован.

После отключения Public Access Prevention публичный доступ стал доступен для объектов bucket.

<img width="1249" height="905" alt="публичная ссылка шаг4" src="https://github.com/user-attachments/assets/fbd1b6aa-a4c5-4972-bfa7-6cd14ec5ff6c" />


---

### 5. Проверка публичной ссылки

После настройки публичного доступа была сформирована публичная ссылка на файл:
https://storage.googleapis.com/mpopov-lab3-bucket/images/1.1.jpg

После открытия ссылки в браузере изображение успешно отобразилось. Это подтвердило, что публичный доступ к объекту был настроен корректно.

---

### 6. Удаление созданных ресурсов

После завершения лабораторной работы bucket и все файлы внутри него были удалены командой:
```bash
gcloud storage rm -r gs://mpopov-lab3-bucket
```
После удаления была выполнена проверка:
```bash
gcloud storage buckets list | grep mpopov-lab3-bucket
```
Команда не вывела результата, значит bucket был успешно удален.

<img width="1140" height="386" alt="удаление шаг5" src="https://github.com/user-attachments/assets/7af3a5d8-ec99-49e3-87bb-383e5586fd91" />

---

## Вывод

В ходе лабораторной работы был изучен сервис Google Cloud Storage.

Был создан bucket mpopov-lab3-bucket, в который были загружены 4 изображения. Затем внутри bucket была создана папка images, куда были перемещены загруженные файлы.

Также был настроен публичный доступ к объектам bucket. Для этого пользователю allUsers была выдана роль Storage Object Viewer.
При настройке было выявлено, что публичный доступ сначала блокировался параметром Public Access Prevention, поэтому данная настройка была отключена.

После настройки доступа была проверена публичная ссылка на изображение. Файл успешно открылся в браузере, что подтвердило корректность настройки публичного доступа.

В конце лабораторной работы все созданные ресурсы были удалены.
