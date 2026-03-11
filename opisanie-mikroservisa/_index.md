---
order: 2
title: Описание микросервиса
---

## 1\. Общая информация о сервисе





<table header="row">
<tr>
<td>

Цель разработки сервиса

</td>
<td>

Управление процессом создания, обработки и принятия решений по заявкам на подмену сотрудников.

</td>
</tr>
<tr>
<td>

Зависимости

</td>
<td>

1. Auth service- проверка авторизации и ролей пользователей

2. Schedule service - получение идентификаторов сотрудников.

3. Office service - получение информации об офисе, из которого создана заявка.

4. Notification service - отправка уведомлений при создании и обработке заявок.

</td>
</tr>
<tr>
<td>

Потребители

</td>
<td>

1. Руководители офисов - для создания заявок на подмену.

2. Территориальные менеджеры - для просмотра и обработки заявок.

3. Frontend - для отображения данных.

4. Notification Service - для оповещения участников.

</td>
</tr>
<tr>
<td>

Архитектурный стиль

</td>
<td>

REST API

</td>
</tr>
</table>





## 2\. Общие требования к реализации сервиса

## 2\.1. Настройки, используемые для работы сервиса

<table header="row">
<tr>
<td>

Название настройки

</td>
<td>

Значение настройки

</td>
<td>

Описание настройки

</td>
</tr>
<tr>
<td>





</td>
<td>





</td>
<td>





</td>
</tr>
</table>





## 2\.2. Реестр ошибок сервиса

| Код (внутренней) ошибки метода | Текстовое описание ошибки                        | HTTP Status Code | Описание                                                   | Объект                    |
|--------------------------------|--------------------------------------------------|------------------|------------------------------------------------------------|---------------------------|
| \-                             | \-                                               | 200              | Выполнение успешно                                         | Объект без ошибки         |
| 1017                           | Некорректные входные параметры                   | 400              | При валидации тела запроса                                 | В ответе объект с ошибкой |
| 1018                           | Невалидный или некорректный JWT-токен            | 401              | При проверке токена выявлена ошибка                        | В ответе объект с ошибкой |
| 1019                           | Отсутствует обязательный параметр \<parameter>   | 400              | Обязательные поля не переданы                              | В ответе объект с ошибкой |
| 1020                           | Офис / сотрудник / регион пользователя не найден | 404              | Не удалось получить officeId / regionId                    | В ответе объект с ошибкой |
| 1021                           | Статус с указанным id не существует              | 404              | id статуса в теле запроса не найден в справочнике статусов | В ответе объект с ошибкой |
| 1022                           | Заявка не найдена                                | 404              | applicationId не существует                                | В ответе объект с ошибкой |
| 1023                           | Истек таймаут ожидания ответа от внешней системы | 500              | Таймаут при вызове Employee Service / Office Service       | В ответе объект с ошибкой |

```
  
```

## 2\.3. Сваггер сервис

Ссылка на сваггер





## 2\.4. Список статусов заявок

<table header="row">
<tr>
<td>

**Id статуса**

</td>
<td>

**Статус**

</td>
<td>

**Описание**

</td>
<td>

**Когда выставляется**

</td>
</tr>
<tr>
<td>

1

</td>
<td>

IN_PROGRESS

</td>
<td>

Актуальная

</td>
<td>

При создании заявки.

</td>
</tr>
<tr>
<td>

2

</td>
<td>

CLOSED

</td>
<td>

Завершённая

</td>
<td>

При подтверждении (закрытии) заявки.

</td>
</tr>
<tr>
<td>

3

</td>
<td>

REJECTED

</td>
<td>

Отклонённая

</td>
<td>

При отмене заявки.

Отменить заявку может и руководитель офиса, и территориальный менеджер.

</td>
</tr>
<tr>
<td>

4

</td>
<td>

OVERDUE

</td>
<td>

Просроченная

</td>
<td>

Когда текущее системное время больше либо равно максимальному крайнему времени среди всех позиций заявки.

</td>
</tr>
</table>





## 3\. Методы сервиса

## 3\.1. Метод POST / applications (Создание заявки на подмену)

<table header="row">
<tr>
<td>

**Формат запроса**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Authorization

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Bearer JWT

</td>
</tr>
<tr>
<td>

Content-Type

</td>
<td>

string

</td>
<td>

да

</td>
<td>

application/json

</td>
</tr>
<tr>
<td>

officeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор офиса, от имени которого создаётся заявка

</td>
</tr>
<tr>
<td>

records

</td>
<td>

array\[object\]

</td>
<td>

да

</td>
<td>

Список записей с потребностями в подмене

</td>
</tr>
<tr>
<td>

records\[\].positionId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор должности

</td>
</tr>
<tr>
<td>

records\[\].quantity

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Количество сотрудников, необходимых на указанную позицию

</td>
</tr>
<tr>
<td>

records\[\].startOn

</td>
<td>

string (YYYY-MM-DD)

</td>
<td>

да

</td>
<td>

Дата начала периода позиции потребности

</td>
</tr>
<tr>
<td>

records\[\].endOn

</td>
<td>

string (YYYY-MM-DD)

</td>
<td>

да

</td>
<td>

Дата окончания периода позиции потребности

</td>
</tr>
<tr>
<td>

records\[\].startAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время начала смены

</td>
</tr>
<tr>
<td>

records\[\].endAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время окончания смены

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "officeId": 5,
  "regionId": 25,
  "records": [
    {
      "positionId": 3,
      "quantity": 1,
      "startOn": "2026-03-01",
      "endOn": "2026-03-10",
      "startAt": "09:00:00",
      "endAt": "18:00:00"
    },
    {
      "positionId": 4,
      "quantity": 2,
      "startOn": "2026-03-05",
      "endOn": "2026-03-15",
      "startAt": "08:00:00",
      "endAt": "17:00:00"
    }
  ]
}
```

</td>
</tr>
</table>

**Формат ответа**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Content-Type

</td>
<td>

string

</td>
<td>

да

</td>
<td>

application/json

</td>
</tr>
<tr>
<td>

id

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Уникальный идентификатор заявки

</td>
</tr>
<tr>
<td>

officeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор офиса, к которому относится заявка

</td>
</tr>
<tr>
<td>

regionId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор региона

</td>
</tr>
<tr>
<td>

status

</td>
<td>

object

</td>
<td>

да

</td>
<td>

Текущий статус заявки

</td>
</tr>
<tr>
<td>

[status.id](http://status.id)

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Внутренний ID статуса

</td>
</tr>
<tr>
<td>

status.code

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Код статуса

</td>
</tr>
<tr>
<td>

[status.name](http://status.name)

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Человеко-читаемое название статуса

</td>
</tr>
<tr>
<td>

createdAt

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Дата и время создания заявки

</td>
</tr>
<tr>
<td>

updatedAt

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Дата и время последнего изменения заявки

</td>
</tr>
<tr>
<td>

records

</td>
<td>

array\[object\]

</td>
<td>

да

</td>
<td>

Список записей

</td>
</tr>
<tr>
<td>

records\[\].id

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Уникальный Id записи внутри заявки

</td>
</tr>
<tr>
<td>

records\[\].positionId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор должности

</td>
</tr>
<tr>
<td>

records\[\].quantity

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Количество сотрудников, необходимых на данную позицию

</td>
</tr>
<tr>
<td>

records\[\].startOn

</td>
<td>

string (YYYY-MM-DD)

</td>
<td>

да

</td>
<td>

Дата начала периода позиции потребности

</td>
</tr>
<tr>
<td>

records\[\].endOn

</td>
<td>

string (YYYY-MM-DD)

</td>
<td>

да

</td>
<td>

Дата окончания периода позиции потребности

</td>
</tr>
<tr>
<td>

records\[\].startAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время начала смены

</td>
</tr>
<tr>
<td>

records\[\].endAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время окончания смены

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "data": {
    "additionalProp1": [
      {
        "id": 0,
        "office": {
          "id": 1,
          "code": "ГО 1",
          "name": "Главный офис 1",
          "address": "ул. Светланская, 56, Владивосток, Приморский край, 692000",
          "cityId": 1,
          "regionId": 1,
          "headId": 1
        },
        "regionId": 0,
        "records": [
          {
            "id": 0,
            "applicationId": 0,
            "position": {
              "id": 1,
              "code": "CASHIER",
              "name": "Кассир"
            },
            "quantity": 0,
            "startOn": "2026-03-03",
            "endOn": "2026-03-03",
            "startAt": "18:00",
            "endAt": "18:00"
          }
        ]
      }
    ]
  }
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
    "code": "INVALID_INTERVAL",
	"message": "Некорректный временной интервал"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает тело запроса с параметрами

2. Проверка JWT и прав пользователя

   -  JWT валиден

   -  Пользователь авторизован

   -  роль = ROLE_OFFICE_HEAD

   -  Если роль не соответствует: **403 Forbidden**

   -  Из JWT извлекаются:

      -  userId

      -  role

      -  officeId

3. Определение офиса пользователя

   1. Выполняется запрос GET /employees/by-user/\{userId}

   2. Определяется id пользователя

   3. Выполняется GET/offices/by-head/\{employeeId}

   4. Определяется id офиса

   5. Проверка:

      1. корректный идентификатор

      2. офис существует

4. Валидация входных данных

   1. officeId указан

   2. records\[\] не пустой

   3. для каждого records

      -  positionId указан

      -  формат даты корректный

      -  startAt \< endAt

   4. При ошибке: 400 Bad Request

5. Создание заявки

   1. Создаётся запись Application:

      1. officeId

      2. regionId

      3. status = ACTIVE

      4. createdAt = [current.date](http://current.date)

   2. Для record:

      1. создаётся ApplicationRecord

      2. связывается с ApplicationId

6. Формирование ответа

   1. id

   2. status

      -  id

      -  code

      -  name

   3. createdAt

   4. updatedAt

   5. records\[\]

7. Ошибки

   1. 400 Bad Request

      1. Некорректные данные

      2. Пустой records

      3. Неверный формат дат

   2. 403 Forbidden

      1. Некорректная роль

      2. офис пользователя ! = офису создания заявки

   3. 404 Not Found

      1. офис не найден

8. Логирование

   1. role

   2. officeId

   3. Результат операций

   4. id созданной заявки

</td>
</tr>
</table>

## 3\.2. Метод GET / applications (Просмотр списка заявок на подмену)

<table header="row">
<tr>
<td>

Возвращает список заявок на подмену по региону

**Формат запроса**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Authorization

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Bearer JWT

</td>
</tr>
<tr>
<td>

regionId

</td>
<td>

integer

</td>
<td>

нет

</td>
<td>

Идентификатор региона

</td>
</tr>
<tr>
<td>

officeId

</td>
<td>

integer

</td>
<td>

нет

</td>
<td>

Идентификатор офиса

</td>
</tr>
<tr>
<td>

year

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Период (год)

</td>
</tr>
<tr>
<td>

month

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Период (месяц)

</td>
</tr>
</table>

**Пример запроса**

GET /applications?regionId=\{regionId}&year=\{year}&month=\{month}

**Формат ответа**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Content-Type

</td>
<td>

string

</td>
<td>

да

</td>
<td>

application/json

</td>
</tr>
<tr>
<td>

response

</td>
<td>

object

</td>
<td>

да

</td>
<td>

Список всех заявок с фильтрацией по региону с полной информацией по текущему месяцу. Сгруппированы по статусу

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "data": {
    "В работе": [
      {
        "id": 1,
        "office": {
          "id": 1,
          "code": "OFFICE-001",
          "name": "Офис №1 (Центральный)",
          "address": "г. Владивосток, ул. Светланская, 16",
          "cityId": 1,
          "regionId": 1,
          "headId": 1
        },
        "regionId": 1,
        "records": [
          {
            "id": 1,
            "applicationId": 1,
            "position": {
              "id": 1,
              "code": "CLIENT_MANAGER",
              "name": "Менеджер по работе с клиентами"
            },
            "quantity": 2,
            "startOn": "2026-03-01",
            "endOn": "2026-03-03",
            "startAt": "09:00:00",
            "endAt": "18:00:00"
          },
          {
            "id": 3,
            "applicationId": 1,
            "position": {
              "id": 2,
              "code": "VIP_CLIENT_MANAGER",
              "name": "Менеджер по работе с ВИП клиентами"
            },
            "quantity": 1,
            "startOn": "2026-03-05",
            "endOn": "2026-03-06",
            "startAt": "10:00:00",
            "endAt": "17:00:00"
          },
          {
            "id": 9,
            "applicationId": 1,
            "position": {
              "id": 5,
              "code": "CASHIER",
              "name": "Кассир"
            },
            "quantity": 2,
            "startOn": "2026-02-01",
            "endOn": "2026-02-03",
            "startAt": "09:00:00",
            "endAt": "18:00:00"
          }
        ]
      }
    ],
    "Просроченная": [],
    "Завершённая": [],
    "Отклонённая": []
  }
}
```

</td>
</tr>
</table>

**Пример ответа пустого списка**

<table header="row">
<tr>
<td>

```
{
  "data": {
    "В работе": [],
    "Просроченная": [],
    "Завершённая": [],
    "Отклонённая": []
  }
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
  "code": "REGION_NOT_FOUND",
  "message": "Регион с данным идентификатором не найден"
}
```

</td>
</tr>
</table>

**Схема работы метода**







**Описание работы метода**

1. <note>

   1. **Контроллер принимает query-параметры:**

      1. regionId - необязательный

      2. officeId - необязательный

      3. year - обязательный

      4. month - обязательный

   2. **Проверка JWT и прав доступа**

      1. пользователь авторизован

      2. Допустимые роли

         1. ROLE_REGION_MANAGER

         2. ROLE_OFFICE_HEAD

      3. Пользователь имеет право просматривать заявки **только своего региона**

      4. из JWT берется userId

         1. Выполняется GET / user / by-user / \{userId}

      5. определяется regionId ТМ

      6. Проверяется regionId из запроса == regionId пользователя

         1. При  ошибке - 403 Forbidden

   3. **Валидация входных данных**

      1. regionId указан

      2. year указан и имеет корректный формат

      3. month `∈ [1..12]`.

      4. При ошибке: 400 Bad Request

   4. **Определение периода выборки**

      1. fromDate - первый день месяца

      2. toDate - последний день месяца

   5. **Получение данных**

      1. Выполняется выборка заявок:

         1. по региону

         2. по диапазону дат

   6. **Формирование ответа**

      1. Формируется объект data, сгруппированный по статусам заявок

      2. Каждый ключ объекта соответствует статусу заявки

      3. Для каждого статуса заявки возвращается массив заявок, каждая из которых содержит:

         1. id

         2. region

         3. office

            -  id

            -  code

            -  name

            -  address

            -  cityId

            -  regionId

            -  headId

         4. массив records

            -  id

            -  applicationId

            -  position

            -  quantity

            -  startOn

            -  endOn

            -  startAt

            -  endAt

   7. **Логирование**

      1. userId

      2. regionId

      3. year

      4. month

      5. результат операции

   </note>

</td>
</tr>
</table>

## 3\.3. Метод PATCH /applications/\{applicationId} (Изменить статус заявки)

<table header="row">
<tr>
<td>

Метод предназначен для частичного обновления ресурса "Заявка".

Используется для изменения статуса заявки

**Формат запроса**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Authorization

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Bearer JWT

</td>
</tr>
<tr>
<td>

applicationId

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Идентификатор заявки

</td>
</tr>
</table>

**Пример запроса**

*PATCH /applications/\{applicationId}*

<table header="row">
<tr>
<td>

```
{
  "statusId": "2"
}
```

</td>
</tr>
</table>

**Формат ответа**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Content-Type

</td>
<td>

string

</td>
<td>

да

</td>
<td>

application/json

</td>
</tr>
<tr>
<td>

response

</td>
<td>

object

</td>
<td>

да

</td>
<td>

Изменённый статус заявки

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "applicationId": 101,
  "status": 
	{
	  "id": 2,
	  "code": "CLOSED",
	  "name": "Завершённая"
}
```

</td>
</tr>
</table>

**Пример ответа пустого списка**

<table header="row">
<tr>
<td>

```
{}
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
  "code": "INVALID_STATUS",
  "message": "Статуса не существует"
}
```

</td>
</tr>
</table>





**Схема работы метода**







**Описание работы метода**

1. <note>

   1. **Контроллер принимает path-параметры:**

      1. applicationId - обязательный

      2. Тело запроса

         1. statusId - id нового статуса заявки

   2. **Проверка JWT и прав доступа**

      1. JWT валиден

      2. пользователь авторизован

      3. из JWT берется userId, role

      4. Допустимые роли:

         1. ROLE_REGION_MANAGER

         2. ROLE_OFFICE_HEAD

   3. Получение заявки по applicationId

      1. Определяется regionId заявки

      2. Определяется officeId заявки

      3. Текущий statusId

      4. Если заявка не найдена: 403 Forbidden

   4. Проверка доступа к заявке

      1. ROLE_REGION_MANAGER

         1. regionId заявки == regionId пользователя

         2. При ошибке: 403 Forbidden

      2. ROLE_OFFICE_HEAD

         1. officeId заявки == officeId пользователя

         2. При ошибке: 403 Forbidden

         3. роли допустим перевод заявки **только** в статус REJECTED

   5. Валидация входных данных

      1. statusId указан

      2. статус существует

      3. новый статус != текущий статус

      4. При ошибке: 400 Bad Request

   6. Проверка допустимости перехода статусов

      1. ROLE_REGION_MANAGER

         1. ACTIVE -> REJECTED

         2. ACTIVE -> COMPLETED

      2. ROLE_OFFICE_HEAD

         1. ACTIVE -> REJECTED

      3. Недопустимые переходы: 409 Conflict

   7. Обновляются поля:

      1. statusId

      2. updatedAt

   8. Формирование ответа

      1. applicationId

      2. status

         1. id

         2. code

         3. name

   9. Логирование

      1. userId

      2. role

      3. applicationId

      4. результат операции

   </note>

</td>
</tr>
</table>

## 3\.4. Метод GET /application-statuses (Получение статусов)

<table header="row">
<tr>
<td>

Возвращает список статусов заявок

**Пример запроса**

*GET /application-statuses*

**Формат ответа**

<table header="row">
<tr>
<td>

**Имя параметра**

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

Content-Type

</td>
<td>

string

</td>
<td>

да

</td>
<td>

application/json

</td>
</tr>
<tr>
<td>

response

</td>
<td>

array

</td>
<td>

да

</td>
<td>

Список статусов заявок

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
[
  {
    "id": 1,
    "code": "ACTIVE",
    "name": "Актуальная"
  },
  {
    "id": 2,
    "code": "CLOSED",
    "name": "Завершённая"
  },
  {
    "id": 3,
    "code": "REJECTED",
    "name": "Отклонённая"
  },
  {
    "id": 4,
    "code": "OVERDUE",
    "name": "Просроченная"
  }
]
```

</td>
</tr>
</table>

**Пример ответа пустого списка ....**

<table header="row">
<tr>
<td>

```
{}
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
  "code": "UNAUTHORIZED",
  "message": "Токен отсутствует или недействителен"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. <note>

   1. Проверка JWT и прав доступа

      1. JWT валиден

      2. Пользователь авторизован

      3. Допустимые роли:

         1. `ROLE_REGION_MANAGER`

         2. `ROLE_OFFICE_MANAGER`

      4. Если пользователь не авторизован -> **401 Unauthorized**

   2. Получение данных

      1. Выполняется выборка всех записей из таблицы

      2. Сортировка по id

   3. Формирование ответа

      1. Ответ 200 OK

      2. Список объектов

         -  id

         -  code - код заявки

         -  name - название заявки

   4. Ошибки

      1. 401 Unauthorized - невалидный токен

   5. Логирование

      1. role

      2. результат операции

      3. количество возвращенных статусов

   </note>

</td>
</tr>
</table>

## Таблицы сервиса с описанием







##### Таблица applications

| Наименование поля | Тип данных и длина | Обяз. | Значение по умолчанию | Ключ                 | Описание поля                                             |
|-------------------|--------------------|-------|-----------------------|----------------------|-----------------------------------------------------------|
| id                | INTEGER            | Да    | Автоинкремент         | PK                   | Идентификатор заявки                                      |
| office_id         | BIGSERIAL          | Да    | \-                    | FK к таблице offices | Идентификатор офиса, к которому относится заявка          |
| region_id         | VARCHAR            | Да    | \-                    | FK к таблице region  | Идентификатор региона, в котором заявка будет выполняться |
| status_id         | INTEGER            | Да    | \-                    | FK к таблице status  | Идентификатор текущего статуса заявки                     |
| created_at        | TIMESTAMP          | Да    | \-                    | \-                   | Дата и время создания заявки                              |
| updated_at        | TIMESTAMP          | Нет   | \-                    | \-                   | Дата и время обновления заявки                            |





##### Таблица record_application

| Наименование поля | Тип данных и длина | Обяз. | Значение по умолчанию | Ключ                      | Описание поля                                        |
|-------------------|--------------------|-------|-----------------------|---------------------------|------------------------------------------------------|
| id                | INTEGER            | Да    | Автоинкремент         | PK                        | Идентификатор записи в заявке                        |
| application_id    | INTEGER            | Да    | \-                    | FK к таблице applications | Идентификатор заявки                                 |
| position_id       | BIGSERIAL          | Да    | \-                    | FK к таблице positions    | Идентификатор должности, на которую заводится запись |
| quantity          | INTEGER            | Да    | \-                    | \-                        | Количество требуемых сотрудников                     |
| start_on          | DATE               | Да    | \-                    | \-                        | Дата начала периода                                  |
| end_on            | DATE               | Да    | \-                    | \-                        | Дата окончания периода                               |
| start_at          | TIME               | Да    | \-                    | \-                        | Время начала смен периода                            |
| end_at            | TIME               | Да    | \-                    | \-                        | Время окончания смен периода                         |





##### Таблица status

| Наименование поля | Тип данных и длина | Обяз. | Значение по умолчанию | Ключ   | Описание поля                |
|-------------------|--------------------|-------|-----------------------|--------|------------------------------|
| id                | INTEGER            | Да    | Автоинкремент         | PK     | Идентификатор статуса заявки |
| code              | VARCHAR            | Да    | ACTIVE                | UNIQUE | Код статуса заявки           |
| name              | VARCHAR            | Да    | Актуальна             | \-     | Наименование статуса заявки  |