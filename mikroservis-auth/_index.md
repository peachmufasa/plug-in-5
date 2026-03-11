---
order: 2
title: микросервис auth
---

-  [1\. Общая информация о сервисе](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-1.%D0%9E%D0%B1%D1%89%D0%B0%D1%8F%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%86%D0%B8%D1%8F%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D0%B5)

-  [2\. Общие требования к реализации сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-2.%D0%9E%D0%B1%D1%89%D0%B8%D0%B5%D1%82%D1%80%D0%B5%D0%B1%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F%D0%BA%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D0%B0)

   -  [2\.1. Настройки, используемые для работы сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-2.1.%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B8,%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D0%B5%D0%BC%D1%8B%D0%B5%D0%B4%D0%BB%D1%8F%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D0%B0)

   -  [2\.2 Реестр ошибок сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-2.2%D0%A0%D0%B5%D0%B5%D1%81%D1%82%D1%80%D0%BE%D1%88%D0%B8%D0%B1%D0%BE%D0%BA%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D0%B0)

   -  [2\.3. Сваггер на сервис](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-2.3.%D0%A1%D0%B2%D0%B0%D0%B3%D0%B3%D0%B5%D1%80%D0%BD%D0%B0%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81)

   -  [2\.3. Описание payload access-токена](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-2.3.%D0%9E%D0%BF%D0%B8%D1%81%D0%B0%D0%BD%D0%B8%D0%B5payloadaccess-%D1%82%D0%BE%D0%BA%D0%B5%D0%BD%D0%B0)

-  [3\. Методы сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-3.%D0%9C%D0%B5%D1%82%D0%BE%D0%B4%D1%8B%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81%D0%B0)

   -  [3\.1.  Метод post /auth/login - получить права доступа](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-3.1.%D0%9C%D0%B5%D1%82%D0%BE%D0%B4post/auth/login-%D0%BF%D0%BE%D0%BB%D1%83%D1%87%D0%B8%D1%82%D1%8C%D0%BF%D1%80%D0%B0%D0%B2%D0%B0%D0%B4%D0%BE%D1%81%D1%82%D1%83%D0%BF%D0%B0)

   -  [3\.2.  Метод post /auth/refresh- обновить пару токенов](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-3.2.%D0%9C%D0%B5%D1%82%D0%BE%D0%B4post/auth/refresh-%D0%BE%D0%B1%D0%BD%D0%BE%D0%B2%D0%B8%D1%82%D1%8C%D0%BF%D0%B0%D1%80%D1%83%D1%82%D0%BE%D0%BA%D0%B5%D0%BD%D0%BE%D0%B2)

   -  [3\.3.  Метод post /auth/logout - обнулить время жизни пары токенов](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#id-%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81Auth%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9-3.3.%D0%9C%D0%B5%D1%82%D0%BE%D0%B4post/auth/logout-%D0%BE%D0%B1%D0%BD%D1%83%D0%BB%D0%B8%D1%82%D1%8C%D0%B2%D1%80%D0%B5%D0%BC%D1%8F%D0%B6%D0%B8%D0%B7%D0%BD%D0%B8%D0%BF%D0%B0%D1%80%D1%8B%D1%82%D0%BE%D0%BA%D0%B5%D0%BD%D0%BE%D0%B2)

<openapi src="./mikroservis-auth.yaml" flag="true"/>

## [1\. Общая информация о сервисе](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

<table header="row">
<tr>
<td>

Цель разработки сервиса (назначение)

</td>
<td>

Подтверждение наличия пользователей в бд и выдача им соответствующих прав

</td>
</tr>
<tr>
<td>

Зависимости

</td>
<td>

1. Микросервис Employees

</td>
</tr>
<tr>
<td>

Потребители

</td>
<td>





</td>
</tr>
<tr>
<td>

Архитектурный стиль

</td>
<td>

REST

Вызывается фронтом

</td>
</tr>
</table>

## [2\. Общие требования к реализации сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

## [2\.1. Настройки, используемые для работы сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

| Название настройки         | Значение настройки                                                                                                                                                                                                                 | Описание настройки                                                                                                                                                                   |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spring.datasource.url      | \$\{DB_URL:jdbc:[postgresql://](postgresql://localhost:5432/$%7BDB_NAME:best%7D?currentSchema=best_auth)[localhost:5432/\$\{DB_NAME:best}?currentSchema=best_auth](http://localhost:5432/${DB_NAME:best}?currentSchema=best_auth)} | URL базы данных, на конце которого название: по умолчанию "best", а если в переменных окружения есть DB_NAME, то примет его значение. Ещё текущая схема в бд указывается - best_auth |
| spring.datasource.username | \$\{DB_USER:best}                                                                                                                                                                                                                  | username для бд. По умолчанию best, а если в переменных окружения есть DB_USER, то примет его значение                                                                               |
| spring.datasource.password | \$\{DB_PASS:best}                                                                                                                                                                                                                  | Пароль к пользователю с username                                                                                                                                                     |
| logging.pattern.console    | %d\{ISO8601} %-5level \[%t\] %-30.30logger\{29}: %msg%n%throwable                                                                                                                                                                  | Шаблон для вывода логов в консоли                                                                                                                                                    |
| employees.client.url       | \$\{EMPLOYEES_URL:<http://localhost:8080>}                                                                                                                                                                                         | url сервиса Employees                                                                                                                                                                |
| server.port                | \$\{SERVER_PORT:8080}                                                                                                                                                                                                              | порт сервиса                                                                                                                                                                         |
| jwt.publicKey              | \$\{JWT_PUBLIC_KEY:#\{null}}                                                                                                                                                                                                       | токен                                                                                                                                                                                |
| jwt.privateKey             | \$\{JWT_PRIVATE_KEY:#\{null}}                                                                                                                                                                                                      | токен                                                                                                                                                                                |

## [2\.2 Реестр ошибок сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

[дать ссылку на реестр, иначе заполнить таблицу](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

Список ошибок и маппинг на HTTP Status Code

|      |                                                                                             |     |                                                                     |                                                              |
|------|---------------------------------------------------------------------------------------------|-----|---------------------------------------------------------------------|--------------------------------------------------------------|
| \-   | \-                                                                                          | 201 | Объект успешно создан                                               | В ответе указан объект с полями данных                       |
| 1000 | Истек таймаут ожидания ответа от конечной системы. Возможно повторный вызов решит проблему. | 500 | Истек таймаут ожидания ответа от конечной системы                   | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1001 | Не заполнен обязательный параметр login                                                     | 400 | Не все обязательные параметры заполнены для работы алгоритма метода | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1002 | Не заполнен обязательный параметр password                                                  | 400 | Не все обязательные параметры заполнены для работы алгоритма метода | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1003 | Не верен логин или пароль                                                                   | 401 | Логин и пароль пользователя не совпадают                            | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1004 | Не передан обязательный заголовок refreshToken                                              | 400 | Не все обязательные параметры заполнены для работы алгоритма метода | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1005 | Не передан обязательный заголовок accessToken                                               | 400 | Не все обязательные параметры заполнены для работы алгоритма метода | В ответе указан объект с сообщением об ошибке и кодом ошибки |
| 1006 | Неверный токен                                                                              | 401 | Сервер не может подтвердить подлинность токена                      | В ответе указан объект с сообщением об ошибке и кодом ошибки |

## [2\.3. Сваггер на сервис](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

```
Факт: https://test.best.atbplugin.tech/api/auth/swagger-ui/index.html
План: best-auth-api-v0.json
```

## [2\.3. Описание payload access-токена](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

|             |         |                                                                                                  |
|-------------|---------|--------------------------------------------------------------------------------------------------|
| jti         | string  | id токена                                                                                        |
| user_id     | string  | id пользователя                                                                                  |
| roles       | array   | Роли пользователя. enum из: \["ROLE_EMPLOYEE", "ROLE_OFFICE_MANAGER", "ROLE_TERRITORY_MANAGER"\] |
| division_id | integer | id направления, если в роли указан ROLE_EMPLOYEE, иначе null                                     |
| office_id   | integer | id офиса, если в роли указан ROLE_OFFICE_MANAGER, иначе null                                     |
| region_id   | integer | id региона, если в роли указан ROLE_TERRITORY_MANAGER, иначе null                                |
| iat         | integer | Дата создания токена в формате NumericDate                                                       |
| exp         | integer | Дата окончания токена в формате NumericDate                                                      |

## [3\. Методы сервиса](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)

## [3\.1.  Метод post /auth/login - ](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)получить права доступа

Описание метода

**Формат запроса**

|          |        |    |                     |
|----------|--------|----|---------------------|
| login    | string | \+ | Логин пользователя  |
| password | string | \+ | Пароль пользователя |

**Формат ответа**

<table header="row">
<tr>
<td>



</td>
<td>



</td>
<td>



</td>
<td>



</td>
</tr>
<tr>
<td>

```
accessToken
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен использующийся для подтверждения прав юзера

</td>
</tr>
<tr>
<td>

```
refreshToken
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен для обновления accessToken, если у того истечет срок жизни

</td>
</tr>
</table>

**Пример ответа без ошибки**

`Пример запроса: post /auth/login Body: { "login": "petrovalog1337", "password": "qwerty123" } Ответ: { "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6Ik9sYSIsImlhdCI6MTY1ODMyMTM3M30.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw", "refreshToken": "eyJhbJbQOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6Ik9sYSIsImlhdCI6MTY1ODMyMTM3M30.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw" }`

**Пример ответа с ошибкой**

`{ "message": "Неверен логин или пароль", "code": 1003 }`

**Схема работы метода:**

<plant-uml path="./mikroservis-auth.puml" width="756px" height="573px"/>

**Описание работы метода**

1. Пользователь вводит логин и пароль в форму авторизации

   1. Если пользователь не ввел логин или пароль - фронт должен подсветить незаполненные поля и не отправлять запрос

2. Фронт отправляет запрос post /auth/login на бек

3. Бек проверяет наличие логина пользователя в бд

   1. Если логина нет в бд - возвращает 1003 ошибку

4. Бек проверяет, совпадает лишь хеш введенного пароля с хешем в бд

   1. Если не совпадают - возвращает 1003 ошибку

5. Бек проверяет, какой ролью обладает пользователь

6. Бек формирует пару токенов

7. Бек отправляет токены фронту

8. Фронт записывает токены в куки

9. Фронт редиректится на страницу с графиками

## [3\.2.  Метод post /auth/refresh- ](https://wiki.atbplugin.tech/spaces/BEST/pages/2361973/%D0%9C%D0%B8%D0%BA%D1%80%D0%BE%D1%81%D0%B5%D1%80%D0%B2%D0%B8%D1%81+Auth+-+%D0%B0%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F+%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B5%D0%B9#)обновить пару токенов

Описание метода

**Формат запроса**

<table header="row">
<tr>
<td>



</td>
<td>



</td>
<td>



</td>
<td>



</td>
</tr>
<tr>
<td>

```
token
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен для обновления accessToken, если у того истечет срок жизни

</td>
</tr>
</table>

```
```

**Формат ответа**

<table header="row">
<tr>
<td>



</td>
<td>



</td>
<td>



</td>
<td>



</td>
</tr>
<tr>
<td>

```
accessToken
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен использующийся для подтверждения прав юзера

</td>
</tr>
<tr>
<td>

```
refreshToken
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен для обновления accessToken, если у того истечет срок жизни

</td>
</tr>
</table>

**Пример ответа без ошибки**

`Пример запроса: post` [`http://atb.su/api/auth/refresh`](http://atb.su/api/auth/refresh) `Ответ: { "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6Ik9sYSIsImlhdCI6MTY1ODMyMTM3M30.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw", "refreshToken": "eyJhbJbQOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6Ik9sYSIsImlhdCI6MTY1ODMyMTM3M30.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw" }`

**Пример ответа с ошибкой**

`{ "message": "Неверный токен", "code": 1006 }`

**Схема работы метода:**

<plant-uml path="./mikroservis-auth-2.puml" width="1007px" height="676px"/>

**Описание работы метода**

1. Фронт отправляет запрос post <http://atb.su/api/auth/refresh> с refhresh токеном в body

2. Бек проверяет валидность токена

   1. Если токен поддельный - вернуть 1006 ошибку

3. Бек валидирует токен

   1. Если токена нет в бд - возвращает 1006 ошибку

4. Бек удаляет refresh токен из бд

5. Бек формирует новую пару токенов

6. Бек записывает в бд refresh токен

7. Бек отсылает фронту новую пару токенов

8. Фронт перезатирает в куки токены

## [3\.3.  Метод post /auth/logout - ](#)обнулить время жизни пары токенов

**Формат запроса**

<table header="row">
<tr>
<td>

**Body**

</td>
<td>

**Тип**

</td>
<td>

**Обяз.**



</td>
<td>

**Описание**

</td>
</tr>
<tr>
<td>

```
token
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Токен использующийся для подтверждения прав юзера

</td>
</tr>
</table>

```
```

**Формат ответа**

<table header="row">
<tr>
<td>

**message-body**

</td>
<td>

**Тип**

</td>
<td>

**Обяз.**



</td>
<td>

**Описание**

</td>
</tr>
<tr>
<td>

```
message
```

</td>
<td>

string

</td>
<td>

\+

</td>
<td>

Сообщение "Токены обнулены"

</td>
</tr>
</table>





**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
Пример запроса: post http://atb.su/api/auth/logout

Ответ:
{
  "message": "токены обнулены"
}
```

</td>
</tr>
</table>

**Пример ответа с ошибкой**

<table header="row">
<tr>
<td>

```
{
  "message": "Неверный токен",
  "code": 1006
}
```

</td>
</tr>
</table>

**Схема работы метода**

<plant-uml path="./mikroservis-auth-3.puml" width="756px" height="573px"/>



**Описание работы метода**

1. Пользователь выходит из пользователя

2. Фронт отправляет запрос post <http://atb.su/api/auth/logout> с access токеном в body

3. Бек валидирует токен

4. Если токена нет в бд - возвращает 1006 ошибку

5. Если токен есть - удаляет его и соответствующий ему refresh токен

6. Возвращает фронту сообщение об успехе