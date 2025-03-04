---
title: Загрузка образа диска в облако
description: Эта инструкция описывает, как загрузить файл образа диска в хранилище {{ objstorage-name }} с ОС Linux, а также создать из него образ и виртуальную машину в сервисе {{ compute-name }}.
keywords:
  - загрузка образа
  - загрузка образа диска
  - загрузка в облако
  - образ диска
  - образ ОС Linux
---

# Загрузить свой образ диска в {{ yandex-cloud }}

Эта инструкция описывает, как загрузить в {{ objstorage-full-name }} файл образа с ОС Linux, а также создать из него [образ](../../concepts/image.md) и виртуальную машину в сервисе {{ compute-name }}.

Поддерживаются известные системы виртуализации.

{% note warning %}

В {{ compute-name }} создать образ можно только из файла, загруженного в {{ objstorage-name }}. Также вы можете мигрировать виртуальную машину с помощью [Hystax Acura](../../../tutorials/infrastructure-management/hystax-migration.md).

{% endnote %}

## Подготовьте файл с образом {#prepare-file}

{% include [image-create-requirements](../../../_includes/compute/image-create-requirements.md) %}

Инструкции по настройке см. в разделе [{#T}](custom-image.md).

## Загрузите файл образа в {{ objstorage-name }} {#upload-file}

Загрузите файл с образом в сервис {{ objstorage-name }} и получите ссылку на загруженный образ:
1. Если у вас еще нет бакета в {{ objstorage-name }}, [создайте](../../../storage/operations/buckets/create.md) его.
1. Загрузите образ в ваш бакет, например, [через консоль управления](../../../storage/operations/objects/upload.md), с помощью [AWS CLI](../../../storage/tools/aws-cli.md) или [WinSCP](../../../storage/tools/winscp.md). В терминах {{ objstorage-name }} загружаемый файл образа будет называться _объектом_.
1. [Получите ссылку](../../../storage/operations/objects/link-for-download.md) на загруженный образ. Используйте эту ссылку при создании образа в {{ compute-name }}.

## Создайте образ в {{ compute-name }} {#create-image}

Создайте новый образ по ссылке, полученной в {{ objstorage-name }}:

{% list tabs %}

- Консоль управления

  1. В консоли управления выберите каталог, в котором нужно создать образ.
  1. Выберите сервис **{{ compute-name }}**.
  1. На панели слева выберите ![image](../../../_assets/compute/image-pic.svg) **Образы**.
  1. Нажмите кнопку **Загрузить образ**.
  1. Введите имя образа.

     * Длина — от 2 до 63 символов.
     * Может содержать строчные буквы латинского алфавита, цифры и дефисы.
     * Первый символ — буква. Последний символ — не дефис.

  1. Если требуется, добавьте произвольное описание образа.
  1. Вставьте ссылку на образ, полученную в {{ objstorage-name }}.
  1. Чтобы создать [оптимизированный образ](../../concepts/image.md#images-optimized-for-deployment), включите опцию **Оптимизировать для развертывания**.
  1. Нажмите кнопку **Загрузить**.

- CLI

  Чтобы создать новый образ по ссылке, воспользуйтесь флагом `--source-uri`. Для создания [оптимизированного образа](../../concepts/image.md#images-optimized-for-deployment) используйте флаг `--pooled`.

  ```bash
  yc compute image create --name <image-name> --source-uri <image-URL> --pooled
  ```

  Где:

  * `<image-name>` — имя, которое будет присвоено образу.
  * `<image-URL>` — ссылка на образ, полученная в {{ objstorage-name }}.

  Если необходимо, добавьте описание и укажите [семейство](../../concepts/image.md#family), к которому относится этот образ:

  ```bash
  yc compute image create \
    --name ubuntu-cosmic \
    --description "Ubuntu Server 18.10 (Cosmic Cuttlefish)" \
    --family ubuntu \
    --source-uri "https://{{ s3-storage-host }}/mybucket/cosmic-server-cloudimg-amd64.vmdk"
  ```

  Если вы знаете минимальные требования к размеру диска, который будет создан из этого образа, укажите размер в гигабайтах:

  ```bash
  yc compute image create \
    --name big-image \
    --min-disk-size 20 \
    --source-uri "https://{{ s3-storage-host }}/mybucket/cosmic-server-cloudimg-amd64.vmdk"
  ```

  {% include [min-disk-size](../../_includes_service/min-disk-size.md) %}

- {{ TF }}

  Если у вас еще нет {{ TF }}, [установите его и настройте провайдер {{ yandex-cloud }}](../../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).

  Чтобы создать образ:

  1. Опишите в конфигурационном файле параметры ресурса `yandex_compute_image`.

     Пример структуры конфигурационного файла:

     ```
     resource "yandex_compute_image" "image-1" {
       name       = "ubuntu-cosmic"
       os_type    = "LINUX"
       source_url = "<ссылка на образ в {{ objstorage-name }}>"
       pooled     = "false"
     }
     ```

     Более подробную информацию о ресурсах, которые вы можете создать с помощью {{ TF }}, см. в [документации провайдера]({{ tf-provider-link }}/).

  1. Проверьте корректность конфигурационных файлов.

     1. В командной строке перейдите в папку, где вы создали конфигурационный файл.
     1. Выполните проверку с помощью команды:

        ```bash
        terraform plan
        ```

       Если конфигурация описана верно, в терминале отобразится список создаваемых ресурсов и их параметров. Если в конфигурации есть ошибки, {{ TF }} на них укажет.

  1. Разверните облачные ресурсы.

     1. Выполните команду:

        ```bash
        terraform apply
        ```

     1. Подтвердите создание ресурсов.

     После этого в указанном каталоге будут созданы все требуемые ресурсы. Проверить появление ресурсов и их настройки можно в [консоли управления]({{ link-console-main }}).

- API

  Создайте новый образ с помощью метода [ImageService/Create](../../api-ref/grpc/image_service.md#Create) gRPC API или метода [create](../../api-ref/Image/create.md) ресурса `Image` REST API. В запросе укажите ссылку на образ.

{% endlist %}

После создания образ перейдет в статус `CREATING`. Дождитесь, когда образ перейдет в статус `READY`, прежде чем его использовать.

## Удалите образ из {{ objstorage-name }} {#delete-image}

Если образ был успешно создан, вы можете [удалить файл образа](../../../storage/operations/objects/delete.md) из сервиса {{ objstorage-name }}. Также можно [удалить бакет](../../../storage/operations/buckets/delete.md), если в нем не осталось объектов.

## Создайте виртуальную машину из подготовленного образа {#create-vm-from-user-image}

{% include notitle [Как создать ВМ из своего образа](../../operations/vm-create/create-from-user-image.md#create-vm-from-image) %}

О стоимости использования {{ objstorage-name }} читайте в разделе [{#T}](../../../storage/pricing.md).