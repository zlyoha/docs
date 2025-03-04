Создайте триггер для [бюджетов](../../billing/concepts/budget.md), который будет вызывать [функцию](../../functions/concepts/function.md) {{ sf-name }} или [контейнер](../../serverless-containers/concepts/container.md) {{ serverless-containers-name }} при превышении пороговых значений.

## Перед началом работы {#before-you-begin}

Для создания триггера вам понадобятся:

* Функция или контейнер, которые триггер будет запускать.

	* Если у вас нет функции:

	    * [Создайте функцию](../../functions/operations/function/function-create.md).
	    * [Создайте версию функции](../../functions/operations/function/version-manage.md#func-version-create).

	* Если у вас нет контейнера:

	    * [Создайте контейнер](../../serverless-containers/operations/create.md).
	    * [Создайте ревизию контейнера](../../serverless-containers/operations/manage-revision.md#create).

* Бюджет, при превышении порога которого триггер будет запускаться. Если у вас нет бюджета, [создайте его](../../billing/operations/budgets.md).

* (опционально) Очередь [Dead Letter Queue](../../functions/concepts/dlq.md), куда будут перенаправляться сообщения, которые не смогли обработать функция или контейнер. Если у вас нет очереди, [создайте ее](../../message-queue/operations/message-queue-new-queue.md).

* [Сервисные аккаунты](../../iam/concepts/users/service-accounts.md) с правами на вызов функции или контейнера и (опционально) запись в очередь [Dead Letter Queue](../../functions/concepts/dlq.md). Вы можете использовать один и тот же сервисный аккаунт или разные. Если у вас нет сервисного аккаунта, [создайте его](../../iam/operations/sa/create.md).

## Создать триггер {#trigger-create}

{% include [trigger-time](trigger-time.md) %}

{% list tabs %}

- Консоль управления

    1. В [консоли управления]({{ link-console-main }}) перейдите в каталог, в котором хотите создать триггер.

    1. Выберите сервис **{{ sf-name }}**.

    1. На панели слева выберите ![image](../../_assets/functions/triggers.svg) **Триггеры**.

    1. Нажмите кнопку **Создать триггер**.

    1. В блоке **Базовые параметры**:

        * Введите имя и описание триггера.
        * В поле **Тип** выберите **Бюджет**.
        * Выберите, что будет запускать триггер — функцию или контейнер.

    1. В блоке **Настройки бюджета** выберите платежный аккаунт и бюджет. Можно выбрать **Все бюджеты**.

    1. Если триггер будет запускать:

	    * функцию, в блоке **Настройки функции** выберите ее и укажите:

	        * [тег версии функции](../../functions/concepts/function.md#tag);
	        * [сервисный аккаунт](../../iam/concepts/users/service-accounts.md), от имени которого будет вызываться функция.

	    * контейнер, в блоке **Настройки контейнера** выберите его и укажите:

	        * [ревизию контейнера](../../serverless-containers/concepts/container.md#revision);
	        * [сервисный аккаунт](../../iam/concepts/users/service-accounts.md), от имени которого будет вызываться контейнер.

    1. (опционально) В блоке **Настройки повторных запросов**:

        * В поле **Интервал** укажите время, через которое будет сделан повторный вызов функции или контейнера, если текущий завершился неуспешно. Допустимые значения — от 10 до 60 секунд, значение по умолчанию — 10 секунд.
        * В поле **Количество попыток** укажите количество повторных вызовов функции или контейнера, которые будут сделаны, прежде чем триггер отправит сообщение в [Dead Letter Queue](../../functions/concepts/dlq.md). Допустимые значения — от 1 до 5, значение по умолчанию — 1.

    1. (опционально) В блоке **Настройки Dead Letter Queue** выберите очередь [Dead Letter Queue](../../functions/concepts/dlq.md) и сервисный аккаунт с правами на запись в нее.

    1. Нажмите кнопку **Создать триггер**.

- CLI

    {% include [cli-install](../cli-install.md) %}

    {% include [default-catalogue](../default-catalogue.md) %}

    Чтобы создать триггер, который запускает функцию, выполните команду:

    ```
    yc serverless trigger create billing-budget \
      --name <имя триггера> \
      --billing-account-id <идентификатор платежного аккаунта> \
      --budget-id <идентификатор бюджета> \
      --invoke-function-id <идентификатор функции> \
      --invoke-function-service-account-id <идентификатор сервисного аккаунта> \
      --retry-attempts 1 \
      --retry-interval 10s \
      --dlq-queue-id <идентификатор очереди Dead Letter Queue> \
      --dlq-service-account-id <идентификатор сервисного аккаунта>
    ```

    Где:

    * `--name` — имя триггера.
    * `--billing-account-id` — идентификатор платежного аккаунта.
    * `--budget-id` — идентификатор бюджета.
    * `--invoke-function-id` — идентификатор функции.
    * `--invoke-function-service-account-id` — идентификатор сервисного аккаунта, у которого есть права на вызов функции.
    * `--retry-attempts` — время, через которое будет сделан повторный вызов функции, если текущий завершился неуспешно. Необязательный параметр. Допустимые значения — от 10 до 60 секунд, значение по умолчанию — 10 секунд.
    * `--retry-interval` — количество повторных вызовов, которые будут сделаны, прежде чем триггер отправит сообщение в [Dead Letter Queue](../../message-queue/concepts/dlq.md). Необязательный параметр. Допустимые значения — от 1 до 5, значение по умолчанию — 1.
    * `--dlq-queue-id` — идентификатор очереди [Dead Letter Queue](../../functions/concepts/dlq.md). Необязательный параметр.
    * `--dlq-service-account-id` — сервисный аккаунт с правами на запись в очередь [Dead Letter Queue](../../functions/concepts/dlq.md). Необязательный параметр.

    Результат:

    ```
    id: a1sfe084v4se4morbu2i
    folder_id: b1g88tflru0ek1omtsu0
    created_at: "2019-12-04T08:45:31.131391Z"
    name: budget-trigger
    rule:
      billing-budget:
        billing-account-id: dn2char50j**********
        budget-id: dn2jnshmdlc1********
        invoke_function:
          function_id: d4eofc7n0m03********
          function_tag: $latest
          service_account_id: aje3932acd0c********
          retry_settings:
            retry_attempts: "1"
            interval: 10s
          dead_letter_queue:
            queue-id: yrn:yc:ymq:{{ region-id }}:aoek49ghmknn********:dlq
            service-account-id: aje3932acd0c********
    status: ACTIVE
    ```

- API

    Создать триггер можно с помощью метода API [create](../../functions/triggers/api-ref/Trigger/create.md).

{% endlist %}

## Проверить результат {#check-result}

{% include [check-result](check-result.md) %}
