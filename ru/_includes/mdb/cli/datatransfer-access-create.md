Чтобы разрешить доступ к кластеру из сервиса [{{ data-transfer-full-name }}](../../../data-transfer/index.yaml) в Serverless-режиме, передайте параметр `--datatransfer-access`.

Это позволит через специальную сеть подключаться к {{ data-transfer-full-name }}, запущенному в {{ k8s }}. В результате будут быстрее выполняться, например, запуск и деактивация трансфера.
