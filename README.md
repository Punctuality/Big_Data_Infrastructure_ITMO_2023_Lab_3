# Лабораторная работа №3

**Дисциплина:** "Инфраструктура больших данных"

**Выполнил:** Федоров Сергей, M4150 

**Вариант:** HDFS (#11)

**Репозиторий с исходным кодом:** [Репозиторий](https://github.com/Punctuality/Big_Data_Infrastructure_ITMO_2023_Lab_3)

**Статус автоматической проверки:** [![CI/CD Pipeline](https://github.com/Punctuality/Big_Data_Infrastructure_ITMO_2023_Lab_3/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/Punctuality/Big_Data_Infrastructure_ITMO_2023_Lab_3/actions/workflows/ci-cd.yml)

## Это продолжение лабораторной работы №1 и №2

## Описания выполнения:

1. Добавить интеграцию с указанным источником данных
2. Правильно использовать секреты
3. Модификация CI/CD

## Добавить интеграцию с указанным источником данных

В моем случае источник данных - HDFS. Отличительной особенностью по сравнению с другими вариантами - черезмерная сложность настройки, условно удобное использование.

В нашем случае источник данных должен быть контейнеризован, однако в данном случая этого достичь не смог ввиду того как работает взаимодействие Namenode и Datanode в HDFS и факт того что hostname контейнеризированных нод не будут резолвится на клиенте, в момент загрузки или отправки данных. Поэтому HDFS был настроен на удаленном VPS сервере.

Поскольку HDFS это файловая система а не база данных, мы будем использовать ее как файловую систему, а именно переиспользуем механизм загрузки данных через DVC при старте проекта, однако будем использовать HDFS вместо S3.

## Правильно использовать секреты

Адрес VPS сервера как и прочие *чувствительные* параметры конфигурации хранятся в Github Secrets. Пример:

```yaml
- name: Prepare DVC for remote storage
  run: dvc remote add --local remote_webhdfs ${{ secrets.WEBHDFS_REMOTE_URL }}
```

## Модификация CI/CD

В виду дополнения лабораторной работы №1, модификации CI/CD были минимальны:

```yaml
      - name: Install DVC
        run: pip install dvc dvc-s3 dvc-webhdfs

      - name: Prepare DVC for remote storage
        run: dvc remote add --local remote_webhdfs ${{ secrets.WEBHDFS_REMOTE_URL }}

      - name: Pull data and model from DVC remote storage
        run: dvc pull -r remote_webhdfs
```