---
order: 1
title: Селиверстов Антон описание микросервиса Schedule
---

## 1\. Общая информация о сервисе

<table header="row">
<tr>
<td>

Цель разработки сервиса

</td>
<td>

Микросервис Schedule предназначен для управления расписанием смен и отсутствий сотрудников.

</td>
</tr>
<tr>
<td>

Зависимости

</td>
<td>

-  Auth - аутентификация пользователей и проверка прав доступа

-  Employee - получение информации о сотрудниках и их ролях

-  Office - получение информации об офисах и регионах

</td>
</tr>
<tr>
<td>

Потребители

</td>
<td>

-  Фронтенд-приложение для сотрудников и руководителей

</td>
</tr>
<tr>
<td>

Архитектурный стиль

</td>
<td>

Сервис разработан в соответствии с микросервисной архитектурой и предоставляет REST API

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

| Код (внутренней) ошибки метода | Текстовое описание ошибки                        | HTTP Status Code         | Описание                                                                        | Объект                                 |
|--------------------------------|--------------------------------------------------|--------------------------|---------------------------------------------------------------------------------|----------------------------------------|
| –                              | –                                                | 200                      | Выполнено успешно                                                               | Объект без ошибки                      |
| 1001                           | Ошибка бизнес-логики                             | 409                      | Нарушение бизнес-ограничений (пересечение смен, запрет дня, конфликт состояния) | В ответе объект с ошибкой              |
| 1002                           | Истек таймаут ожидания ответа от внешней системы | 500                      | Таймаут при вызове Employee Service / Calendar API                              | В ответе объект с ошибкой              |
| 1003                           | Не заполнен обязательный параметр `<parameter>`  | 400                      | Отсутствует обязательный query/path/body параметр                               | В ответе объект с ошибкой              |
| 1004                           | Указано недопустимое значение `<parameter>`      | 400                      | Некорректный формат, диапазон или значение параметра                            | В ответе объект с ошибкой              |
| 1005                           | Сущность не найдена                              | 404                      | Shift / Employee / Office не существует                                         | В ответе объект с ошибкой              |
| 1006                           | Доступ запрещён                                  | 403                      | Нет прав на операцию с офисом / сменой                                          | В ответе объект с ошибкой              |
| 1007                           | Пользователь не авторизован                      | 401                      | Отсутствует или невалидный JWT                                                  | В ответе объект с ошибкой              |
| 1008                           | Ошибка на стороне внешней системы                | HTTP как вернул источник | Получена ошибка от Employee Service / другого сервиса                           | В ответе объект с ошибкой              |
| –                              | –                                                | 500                      | Внутренняя ошибка метода                                                        | В ответе объект отсутствует или пустой |

## 2\.3. Сваггер сервиса

<table header="row">
<tr>
<td>

```
openapi: 3.0.0
info:
  title: Schedule Service API
  version: 0.8.0
  description: API сервиса графика смен

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    ErrorResponse:
      type: object
      properties:
        code:
          type: string
          description: Код ошибки
        message:
          type: string
          description: Описание ошибки
      
    LocalTime:
      type: object
      description: Время окончания смены
      example: '18:00'
      properties:
        hour:
          type: integer
          format: int32
        minute:
          type: integer
          format: int32
        second:
          type: integer
          format: int32
        nano:
          type: integer
          format: int32

    ShiftUpdateRequestDto:
      type: object
      description: Запрос на обновление времени смены
      required:
        - startAt
        - endAt
      properties:
        startAt:
          $ref: '#/components/schemas/LocalTime'
        endAt:
          $ref: '#/components/schemas/LocalTime'

    ShiftResponseDto:
      type: object
      properties:
        id:
          type: integer
          description: ID смены
        employeeId:
          type: integer
          description: ID сотрудника
        officeId:
          type: integer
          description: ID офиса
        sheduledOn:
          type: string
          format: date
          description: Дата смены
          example: "2023-04-04"
        startAt:
          type: string
          format: time
          description: Время начала смены
          example: "09:00"
        endAt:
          type: string
          format: time
          description: Время окончания смены
          example: "18:00"

    AbsencesCreateRequestDto:
      type: object
      description: Создание отсутствия сотрудника
      required:
        - employeeId
        - absenceTypeId
        - startOn
        - endOn
      properties:
        employeeId:
          type: integer
          format: int64
          description: ID сотрудника
          example: 101
        absenceTypeId:
          type: integer
          format: int64
          description: ID типа отсутствия
          example: 1
        startOn:
          type: string
          format: date
          description: Дата начала отсутствия
          example: '2026-01-20'
        endOn:
          type: string
          format: date
          description: Дата конца отсутствия
          example: '2026-01-25'

    AbsenceResponseDto:
      type: object
      description: Отсутствие сотрудника на конкретную дату
      properties:
        id:
          type: integer
          format: int64
          description: ID отсутствия
          example: 5001
        employeeId:
          type: integer
          format: int64
          description: ID сотрудника
          example: 1
        absentOn:
          type: string
          format: date
          description: Дата отсутствия
          example: '2026-01-22'
        absenceType:
          $ref: '#/components/schemas/AbsenceTypeResponseDto'

    AbsenceTypeResponseDto:
      type: object
      description: Тип отсутствия сотрудника
      required:
        - code
        - name
      properties:
        code:
          type: string
          description: Код типа отсутствия
          example: VACATION
        name:
          type: string
          description: Отображаемое название
          example: Отпуск
        description:
          type: string
          description: Описание типа отсутствия
          example: Плановый ежегодный отпуск
        allowsWeekends:
          type: boolean
          description: Разрешено ли назначение на выходные
          example: true

    EmployeeScheduleResponseDto:
      type: object
      description: Данные о сменах сотрудника
      properties:
        employeeId:
          type: integer
          description: ID сотрудника
        fullName:
          type: string
          description: ФИО сотрудника
          example: "Иванов Иван Иванович"
        substitutionGroup:
          type: boolean
          description: Принадлежность к группе подмены
          example: "Иванов Иван Иванович"
        attachedToOffice:
          type: boolean
          description: Принадлежность к офису
          example: "Иванов Иван Иванович"
        shifts:
          type: array
          items:
            $ref: '#/components/schemas/ShiftResponseDto'
        absences:
          type: array
          items:
            $ref: '#/components/schemas/AbsenceResponseDto'

    EmployeeShiftsResponseDto:
      type: object
      description: Список смен сотрудника (группировка по employeeId)
      properties:
        employeeId:
          type: integer
          format: int64
          description: ID сотрудника
          example: 42
        shifts:
          type: array
          description: Смены сотрудника
          items:
            $ref: '#/components/schemas/ShiftResponseDto'


    SchedulerResponse:
      type: object
      description: Ответ с расписанием смен, сгруппированным по должностям и сотрудникам
      additionalProperties:
        type: object
        additionalProperties:
          $ref: '#/components/schemas/EmployeeScheduleResponseDto'

    CreateShiftRequest:
      type: object
      required: [employeeId, officeId, startOn, endOn, startTime, endTime]
      properties:
        employeeId:
          type: integer
          description: ID сотрудника
        officeId:
          type: integer
          description: ID офиса
        startOn:
          type: string
          format: date
          description: Дата начала смены 
          example: "2023-04-04"
        endOn:
          type: string
          format: date
          description: Дата конца смены 
          example: "2023-04-04"
        startAt:
          type: string
          format: time
          description: Время начала смены
          example: "09:00"
        endAt:
          type: string
          format: time
          description: Время окончания смены
          example: "18:00"
          
    TimesheetResponse:
      type: object
      description: >
        Табель учета рабочего времени, сгруппированный по должностям.
        data — map: ключ = position.name, значение = список сотрудников.
      properties:
        data:
          type: object
          additionalProperties:
            type: array
            items:
              $ref: '#/components/schemas/EmployeeTimesheetDto'

    EmployeeTimesheetDto:
      type: object
      properties:
        employeeId:
          type: integer
          format: int64
        fullName:
          type: string
        position:
          type: object
          properties:
            id: { type: integer }
            code: { type: string }
            name: { type: string }
        days:
          type: array
          description: Дни месяца, отсортированы по dateOn ASC
          items:
            $ref: '#/components/schemas/TimesheetDayDto'
        summary:
          $ref: '#/components/schemas/TimesheetSummaryDto'

    TimesheetDayDto:
      oneOf:
        - $ref: '#/components/schemas/WorkDayDto'
        - $ref: '#/components/schemas/AbsenceDayDto'
        - $ref: '#/components/schemas/EmptyDayDto' 
      discriminator:
        propertyName: type
        mapping:
          work: '#/components/schemas/WorkDayDto'
          absence: '#/components/schemas/AbsenceDayDto'
          empty: '#/components/schemas/EmptyDayDto'
      description: >
        День либо рабочий (план/факт), либо отсутствие.
        Одновременно быть не может.

    WorkDayDto:
      type: object
      required: [type, dateOn, officeId, workTime, workedMinutes, isDraft]
      properties:
        type:
          type: string
          enum: [work]
        dateOn:
          type: string
          format: date
        officeId:
          type: integer
          format: int64
        workTime:
          type: string
          example: "08:00"
        workedMinutes:
          type: integer
          example: 480
        isDraft:
          type: boolean
          description: "true — данные из плана (смены), false — подтвержденный факт"
          example: true
          
    AbsenceDayDto:
      type: object
      required: [type, dateOn, absenceType]
      properties:
        type:
          type: string
          enum: [absence]
        dateOn:
          type: string
          format: date
        absenceType:
          $ref: '#/components/schemas/AbsenceTypeDto'

    AbsenceTypeDto:
      type: object
      required: [id, code, name]
      properties:
        id:
          type: integer
          format: int64
        code:
          type: string
          example: sick 
        name:
          type: string
          example: больничный 
    
    EmptyDayDto:
      type: object
      required: [type, dateOn]
      properties:
        type:
          type: string
          enum: [empty]
        dateOn:
          type: string
          format: date

    TimesheetSummaryDto:
      type: object
      properties:
        totalWorkTime: { type: string, example: "120:00" }
        totalMinutes: { type: integer }
        monthlyNorm: { type: string, example: "160:00" }
        normMinutes: { type: integer }
        balanceTime: { type: string, example: "-40:00" }
        balanceMinutes: { type: integer }
          
    WorktimeRequest:
      type: object
      required:
        - items
      properties:
        items:
          type: array
          items:
            type: object
            required:
              - employeeId
              - officeId
              - dateOn
              - workedMinutes
            properties: 
              employeeId: { type: integer, format: int64 }
              officeId: { type: integer, format: int64 }
              dateOn: { type: string, format: date }
              workedMinutes: { type: integer }

    WorktimeResponse:
      type: object
      properties:
        id:
          type: integer
          format: int64
          example: 585
        officeId:
          type: integer
          format: int64
          example: 1
        employeeId:
          type: integer
          format: int64
          example: 19
        dateOn:
          type: string
          format: date
          example: "2026-03-02"
        workedMinutes:
          type: integer
          example: 480

paths:
  /schedules/by-office/{officeId}:
    get:
      summary: Получение графика смен офиса
      tags: [Schedule]
      security:
        - bearerAuth: []
      description: |
        Получение списка смен, сгруппированных по должности сотрудника и самим сотрудникам.
      parameters:
        - name: officeId
          in: path
          required: true
          schema:
            type: integer
            example: 4
          description: Идентификатор офиса
        - name: year
          in: query
          required: true
          schema:
            type: integer
            example: 2026
          description: Календарный год
        - name: month
          in: query
          required: true
          schema:
            type: integer
            minimum: 1
            maximum: 12
            example: 4
          description: Порядковый номер месяца (1-12)
      responses:
        '200':
          description: Успешное получение расписания
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SchedulerResponse'
        '400':
          description: Некорректные параметры запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Доступ запрещен
        '502':
          description: Ошибка связи с Employee Service
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /schedules/by-employee/{employeeId}:
    get:
      summary: Получение личного графика смен
      tags: [Schedule]
      security:
        - bearerAuth: []
      description: Получение списка смен текущего пользователя
      parameters:
        - name: employeeId
          in: path
          required: true
          schema:
            type: integer
            example: 4
          description: Идентификатор сотрудника
        - name: year
          in: query
          required: true
          schema:
            type: integer
            example: 2026
          description: Календарный год
        - name: month
          in: query
          required: true
          schema:
            type: integer
            minimum: 1
            maximum: 12
            example: 4
          description: Порядковый номер месяца (1-12)
      responses:
        '200':
          description: Успешное получение расписания
          content:
            application/json:
              schema:
                type: object
                properties:
                  shifts:
                    type: array
                    items:
                      $ref: '#/components/schemas/ShiftResponseDto'
                  absences:
                    type: array
                    items:
                      $ref: '#/components/schemas/AbsenceResponseDto'
        '400':
          description: Некорректные параметры запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Доступ запрещен

  /shifts:
    post:
      summary: Создание смены
      tags: [Shifts]
      security:
        - bearerAuth: []
      description: Создание новой рабочей смены для сотрудника
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateShiftRequest'
      responses:
        '201':
          description: Смена успешно изменена
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ShiftResponseDto'
        '400':
          description: Ошибка валидации
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Недостаточно 
        '409':
          description: Конфликт дат (пересечение с другими отсутствиями или сменами)
        '500':
          description: Внутренняя ошибка сервера
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
    get:
      tags:
        - Shifts
      summary: Получить список смен (сгруппировано по employeeId)
      operationId: getShifts
      parameters:
        - name: officeId
          in: query
          required: false
          schema:
            type: integer
            format: int64

        - name: employeeIds
          in: query
          required: false
          schema:
            type: array
            items:
              type: integer
              format: int64

        - name: from
          in: query
          required: false
          schema:
            type: string
            format: date

        - name: to
          in: query
          required: false
          schema:
            type: string
            format: date

      responses:
        '200':
          description: OK
          content:
            '*/*':
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/EmployeeShiftsResponseDto'

        '400':
          description: Ошибка валидации запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

        '401':
          description: Неавторизован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

        '403':
          description: Нет прав доступа
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
    put:
      summary: обновление времени смен
      tags: [Shifts]
      security:
        - bearerAuth: []
      description: |
        Массовое редактирование времени начала и окончания смен.
        Обновление применяется ко всем сменам, переданным в shiftIds.
      parameters:
        - name: shiftIds
          in: query
          required: true
          schema:
            type: array
            minItems: 1
            items:
              type: integer
              format: int64
          description: Список идентификаторов смен
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ShiftUpdateRequestDto'
      responses:
        '200':
          description: Смены успешно обновлены
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/ShiftResponseDto'
        '400':
          description: Ошибка валидации или нарушение бизнес-правил
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Недостаточно прав
        '404':
          description: Одна или несколько смен не найдены
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '409':
          description: Конфликт с уже существуей сменой
        '500':
          description: Внутренняя ошибка сервера
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

    delete:
      summary: удаление смен
      tags: [Shifts]
      security:
        - bearerAuth: []
      description: |
        Массовое удаление смен по списку идентификаторов.
      parameters:
        - name: id
          in: query
          required: true
          schema:
            type: array
            minItems: 1
            items:
              type: integer
              format: int64
          description: Список идентификаторов смен
      responses:
        '204':
          description: Смены успешно удалены
        '400':
          description: Ошибка валидации запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Пользователь не авторизован
        '403':
          description: Недостаточно прав
        '404':
          description: Одна или несколько смен не найдены
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '500':
          description: Внутренняя ошибка сервера
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'


  /absences:
    post:
      tags:
        - Absences
      summary: Создать отсутствие сотрудника
      operationId: createAbsence
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AbsencesCreateRequestDto'
      responses:
        '201':
          description: Created
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/AbsenceResponseDto'
        '400':
          description: Ошибка валидации запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '401':
          description: Неавторизован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '403':
          description: Нет прав доступа
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '404':
          description: Сотрудник или тип отсутствия не найден
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '409':
          description: Конфликт дат (пересечение с другими отсутствиями или сменами)
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
    delete:
      tags:
        - Absences
      summary: Удалить отсутствие
      operationId: deleteAbsence
      parameters:
        - name: id
          in: query
          required: true
          schema:
            type: array
            minItems: 1
            items:
              type: integer
              format: int64
      responses:
        '204':
          description: No Content
        '401':
          description: Неавторизован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '403':
          description: Нет прав доступа
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '404':
          description: Отсутствие не найдено
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /absence-types:
    get:
      summary: Получить типы отсутствий
      operationId: getAbsenceTypes
      tags:
        - Absences
      responses:
        '200':
          description: Список типов отсутствий
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/AbsenceTypeResponseDto'
        '401':
          description: Неавторизован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '403':
          description: Нет прав доступа
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '500':
          description: Внутренняя ошибка сервера
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
    
  /timesheets/by-office/{officeId}:
    get:
      summary: Получение табеля выработки
      tags:
        - Timesheet
      parameters:
        - name: officeId
          in: path
          required: true
          schema: { type: integer, format: int64 }
        - name: positionId
          in: query
          required: false
          schema: { type: integer, format: int64 }
        - name: year
          in: query
          required: true
          schema: { type: integer }
        - name: month
          in: query
          required: true
          schema: { type: integer, minimum: 1, maximum: 12 }
      responses:
        '200':
          description: Табель успешно сформирован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TimesheetResponse'
        '400':
          description: Ошибка валидации данных
        '401':
          description: Пользователь неавторизован
        '500':
          description: Внутренняя ошибка сервера 
  
  /timesheets/by-region/{regionId}:
    get:
      summary: Получение табеля выработки
      tags:
        - Timesheet
      parameters:
        - name: regionId
          in: path
          required: true
          schema: { type: integer, format: int64 }
        - name: positionId
          in: query
          required: false
          schema: { type: integer, format: int64 }
        - name: year
          in: query
          required: true
          schema: { type: integer }
        - name: month
          in: query
          required: true
          schema: { type: integer, minimum: 1, maximum: 12 }
      responses:
        '200':
          description: Табель успешно сформирован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TimesheetResponse'
        '400':
          description: Ошибка валидации данных
        '401':
          description: Пользователь неавторизован 
        '500':
          description: Внутренняя ошибка сервера

  /worktimes:
    post:
      summary: Массовое подтверждение или изменение отработанного времени
      description: >
        Идемпотентный метод.
        Повторный запрос с одинаковыми (employeeId, officeId, dateOn)
        приводит к обновлению существующей записи.
      tags:
        - Timesheet
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/WorktimeRequest'
      responses:
        '200':
          description: Данные успешно сохранены.
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/WorktimeResponse'
        '400':
          description: Ошибка валидации данных
        '401':
          description: Пользователь неавторизован
        '403':
          description: Недостаточно прав
        '500':
          description: Внутренняя ошибка сервера
```

</td>
</tr>
</table>

## 3\. Методы сервиса

## 3\.1. Метод получения расписания офиса

<table header="row">
<tr>
<td>

**==-Формат запроса**

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

officeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор офиса

</td>
</tr>
<tr>
<td>

positionId

</td>
<td>

integer (int64)

</td>
<td>

нет

</td>
<td>

Идентификатор должности

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

Календарный год

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

Порядковый номер месяца (1–12)

</td>
</tr>
</table>

**Пример запроса**

GET /schedules/by-office/5?year=2026&month=4

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

Объект с расписанием, сгруппированным по должностям и сотрудникам

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
    "Менеджер по работе с клиентами": [
      {
        "id": 2,
        "fullName": "Лавров Максим Сергеевич",
        "substitutionGroup": false,
        "attachedToOffice": true,
        "city": {
          "id": 1,
          "name": "Уссурийск",
          "regionId": 1
        },
        "shifts": [
          {
            "id": 1,
            "office": {
              "id": 1,
              "code": "OFFICE-001",
              "name": "Офис №1 (Центральный)",
              "address": "г. Владивосток, ул. Светланская, 1",
              "cityId": 1,
              "regionId": 1,
              "headId": 1
            },
            "employeeId": 2,
            "scheduledOn": "2025-03-03",
            "startAt": "09:00:00",
            "endAt": "18:00:00"
          }
        ],
        "absences": [
          {
            "id": 1,
            "employeeId": 2,
            "absentOn": "2025-03-05",
            "absenceType": {
              "id": 1,
              "code": "vacation",
              "name": "Отпуск"
            }
          }
        ]
      }
    ],
    "Кассир": [
      {
        "id": 3,
        "fullName": "Гончарова Виктория Андреевна",
        "substitutionGroup": false,
        "attachedToOffice": true,
        "city": {
          "id": 1,
          "name": "Уссурийск",
          "regionId": 1
        },
        "shifts": [
          {
            "id": 5,
            "office": {
              "id": 1,
              "code": "OFFICE-001",
              "name": "Офис №1 (Центральный)",
              "address": "г. Владивосток, ул. Светланская, 1",
              "cityId": 1,
              "regionId": 1,
              "headId": 1
            },
            "employeeId": 3,
            "scheduledOn": "2025-03-03",
            "startAt": "10:00:00",
            "endAt": "19:00:00"
          }
        ],
        "absences": [
          {
            "id": 2,
            "employeeId": 3,
            "absentOn": "2025-03-06",
            "absenceType": {
              "id": 2,
              "code": "sick",
              "name": "Больничный"
            }
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
  "code": "MISSING_REQUIRED_PARAMETER",
  "message": "Не заполнен обязательный параметр month"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. **Контроллер принимает**

   1. `officeId` -- из path.

   2. `year`, `month` -- из query-параметров.

2. **Выполняется валидация параметров**

   1. `officeId > 0`;

   2. `year` заполнен;

   3. `month ∈ [1..12]`.

   4. При нарушении -- **400 Bad Request** (`MISSING_REQUIRED_PARAMETER`, `INVALID_PARAMETER_VALUE`).

3. **Проверяется авторизация**

   1. Отсутствует JWT -- **401 Unauthorized**.

4. **Проверка прав доступа**

   1. Пользователь должен иметь право просмотра расписания офиса.

   2. При отсутствии прав -- **403 Forbidden**.

5. **Определение периода выборки**

   1. `fromDate` = первый день месяца;

   2. `toDate` = последний день месяца.

6. **Получение смен офиса**

   1. Запрос к Schedule Service:

      1. выборка смен по `officeId` и диапазону дат.

   2. Формируется список смен офиса за период.

   3. Из смен извлекается множество уникальных `employeeId`.

7. **Получение привязанных к офису сотрудников**

   1. Интеграция с **Office Service**:

      1. `GET /offices/employees?officeId={officeId}`.

   2. Получается список `employeeId`, привязанных к офису.

8. **Формирование итогового списка сотрудников**

   1. Определяется тип месяца выборки относительно текущей даты:

      1. если запрошенный `year/month` меньше текущего года/месяца -- месяц считается **прошедшим**;

      2. если равен текущему или больше -- месяц считается **текущим** или **будущим**.

   2. Если месяц текущий или будущий:

      1. Выполняется слияние списков:

         1. сотрудники, привязанные к офису;

         2. сотрудники, имеющие смены в офисе за период (даже если не привязаны).

      2. Формируется итоговый набор `employeeIds`.

   3. Если месяц прошедший:

      1. Итоговый список формируется только из **сотрудников, имеющих смены** в офисе за указанный период;

      2. Слияние со списком привязанных сотрудников **не выполняется**;

      3. Формируется итоговый набор `employeeIds` на основании смен.

9. **В списке сотрудников для каждой сущности вычисляется логический флаг:**

   -  **Формирование контрольного множества**: из ответа *Office Service* извлекается `Set` идентификаторов сотрудников, имеющих действующую привязку к запрошенному `officeId`.

   -  **Маркировка сотрудников**:

      -  Если `employeeId` присутствует в контрольном множестве, полю `attachedToOffice` присваивается значение **true**.

      -  Если сотрудник попал в список только на основании наличия смен (отсутствует в ответе *Office Service*), полю `attachedToOffice` присваивается значение **false**.

10. **Обогащение данными сотрудников**

    1. Интеграция с **Employee Service**:

       1. `GET /employees?employeeIds={employeeIds[]}`.

    2. Интеграция с **Office Service**:

       1. `GET /offices?Id={officeId}`.

    3. Интеграция с **Catalog Service:**

       

       

       1. `GET /cities?Id={cityId}`.

    4. Получаются:

       1. ФИО;

       2. должность сотрудника;

       3. прочие атрибуты, необходимые для отображения.

    5. При ошибке интеграции -- **502 Bad Gateway**.

11. **Получение отсутствий**

    1. Из Schedule Service выбираются отсутствия сотрудников за тот же период.

    2. Отсутствия маппятся к соответствующим `employeeId`.

12. **Агрегация и структурирование данных**

    1. Группировка:

       1. 1-й уровень -- по должности;

       2. 2-й уровень -- по `employeeId`.

    2. Для каждого сотрудника формируется `EmployeeScheduleResponseDto`:

       1. `employeeId`;

       2. `fullName`; 

       3. substitutionGroup;

       4. attachedToOffice;

       5. `shifts[]`;

       6. `absences[]`.

13. **Формирование ответа**

    1. Ответ собирается в формате `SchedulerResponse`

       (map: `positionName → employeeId → EmployeeScheduleResponseDto`).

    2. Если данных нет -- возвращается пустой объект `{}`.

14. **Логирование**

    1. `officeId`, `year`, `month`;

    2. количество смен и сотрудников;

    3. ошибки интеграций -- уровень **WARN / ERROR**.

15. **Возврат результата**

    1. Успешно -- **200 OK** + агрегированный объект расписания.

</td>
</tr>
</table>

## 3\.2. Метод назначения смены (Руководитель офиса)

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

```
startOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата начала смены

</td>
</tr>
<tr>
<td>

```
endOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата конца смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (time)

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

endAt

</td>
<td>

string (time)

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
  "employeeId": 10,
  "officeId": 4,
  "startOn": "2026-04-01",
  "endOn": "2026-04-01", 
  "startAt": "09:00",
  "endAt": "18:00"
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

Id

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

scheduledOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (time)

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

endAt

</td>
<td>

string (time)

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
  "id": 123,
  "employeeId": 10,
  "officeId": 4,
  "scheduledOn": "2026-04-01",
  "startAt": "09:00",
  "endAt": "18:00"
}
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
  "code": "SHIFT_OVERLAP",
  "message": "Пересечение с существующей сменой или отсутствием"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает тело запроса CreateShiftRequest.

2. Проверка JWT и прав доступа:

   1. пользователь авторизован;

   2. пользователь имеет право назначать смены в данном офисе.

3. Валидация входных данных:

   1. employeeId, officeId, date, startAt, endAt обязательны;

   2. формат даты и времени корректный;

   3. startAt \< endAt.

4. Валидация сотрудника (ГП / привязка к офису)

   1. Выполняется **только если** role=OFFICE_HEAD в jwt

   2. По `employeeId` выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Если `substitutionGroup = true`, операция назначения смены запрещается.

      1. Возвращается `400 Bad Request`.

      2. Проверка привязки сотрудника к офису в этом случае не выполняется.

   4. Если `substitutionGroup = false`, делается запрос GET /offices/\{officeId}/employees выполняется проверка привязки сотрудника к офису `officeId`, для которого создаётся смена.

      1. Если сотрудник не привязан к указанному офису, операция назначения смены запрещается.

      2. Возвращается `400 Bad Request`.

   5. Данная проверка выполняется до проверки смен на регламент офиса и коллизии.

5. Интеграция с сервисом офисов:

   1. выполняется запрос

      GET /working-hours/by-office/\{officeId};

   2. получаются рабочие часы офиса по дням недели;

   3. определяется день недели для date;

   4. проверяется:

      1. офис работает в эту дату;

      2. время смены попадает в допустимый интервал;

      3. соблюдены ограничения бизнес-правил (максимальная длительность, выходные, производственный календарь).

6. Проверка пересечений:

   1. отсутствие пересечения с другими сменами сотрудника (в том числе в других офисах);

   2. отсутствие пересечения с отсутствиями (VACATION, SICK и т.д.).

7. При обнаружении ошибки:

   1. операция прерывается;

   2. смена не создаётся;

   3. возвращается 400 / 403 / 404 / 409 с описанием причины.

8. При успешной валидации:

   1. генерируются записи смен на каждый день диапазона (разбивка периода на дни);

   2. сохранение записей в БД в рамках одной транзакции;

   3. формируется список ShiftResponseDto;

   4. возвращается статус `201 Created`.

9. Логирование:

   1. пользователь;

   2. employeeId, officeId, date;

   3. результат операции;

   4. персональные данные сотрудников в логах не фиксируются (маскирование).

</td>
</tr>
</table>

## 3\.3 Метод удаления смены (Руководитель офиса)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

DELETE /shifts?Id=123&d=124&Id=125



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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "SHIFTS_NOT_FOUND",
  "message": "Смены не найдены"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает список идентификаторов смен `shiftIds` из query-параметров запроса.

2. Проверка JWT и прав доступа пользователя на удаление смен.

3. Валидация query-параметра `Id`:

   -  корректный формат идентификатора.

4. По каждому `shiftId`:

   -  проверка существования смены;

5. Валидация сотрудника на группу подмены

   1. Выполняется **только если** role=OFFICE_HEAD в jwt

   2. По employeeId из смен выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Проверяется состоит ли сотрудник в группе подмены

   4. Если `substitutionGroup = true`, операция удаления смены запрещается.

      1. Возвращается `400 Bad Request`.

6. При обнаружении хотя бы одной ошибки:

   -  операция прерывается;

   -  ни одна смена не удаляется;

   -  возвращается соответствующая ошибка (`400 / 403 / 404`).

7. При успешной валидации всех смен:

   -  выполняется удаление всех смен в рамках одной транзакции.

8. Формирование ответа:

   -  тело отсутствует;

   -  статус `204 No Content`.

9. Логирование:

   -  пользователь, список `shiftIds`, результат операции.

</td>
</tr>
</table>

## 3\.4. Метод редактирования смены (Руководитель офиса)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "startAt": "10:00",
  "endAt": "19:00"
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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

id

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

ID смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

ID сотрудника

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

да

</td>
<td>

ID офиса

</td>
</tr>
<tr>
<td>

scheduledOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время начала

</td>
</tr>
<tr>
<td>

endAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время окончания

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "id": 123,
  "employeeId": 45,
  "officeId": 3,
  "scheduledOn": "2026-04-10",
  "startAt": "10:00",
  "endAt": "19:00"
}
{
  "id": 124,
  "employeeId": 45,
  "officeId": 3,
  "date": "2026-04-10",
  "startAt": "10:00",
  "endAt": "19:00"
}
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
  "code": "SHIFTS_NOT_FOUND",
  "message": "Смены не найдены"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает список идентификаторов смен `shiftIds` из query-параметров и тело запроса с новыми значениями времени.

2. Проверка JWT и прав доступа пользователя на редактирование смен.

3. Валидация входных данных:

   1. `shiftIds` не пустой;

   2. корректный формат идентификаторов;

   3. `startAt < endAt`.

4. Валидация сотрудника на группу подмены

   1. Выполняется **только если** role=OFFICE_HEAD в jwt

   2. По employeeId из смен выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Проверяется состоит ли сотрудник в группе подмены

   4. Если `substitutionGroup = true`, операция удаления смены запрещается.

      1. Возвращается `400 Bad Request`.

5. По каждому `shiftId`:

   1. проверка существования смены;

   2. проверка доступности смены пользователю (права, офис, роль);

   3. Интеграция с сервисом офисов:

      -  выполняется запрос

         GET /working-hours/by-office/\{officeId};

      -  получаются рабочие часы офиса по дням недели;

      -  определяется день недели для date;

      -  проверяется:

         -  офис работает в эту дату;

         -  время смены попадает в допустимый интервал;

         -  соблюдены ограничения бизнес-правил (максимальная длительность, выходные, производственный календарь).

   4. проверка бизнес-правил (пересечения, ограничения периода).

6. При обнаружении хотя бы одной ошибки:

   1. операция прерывается;

   2. ни одна смена не изменяется;

   3. возвращается соответствующая ошибка (`400 / 403 / 404`).

7. При успешной валидации всех смен:

   1. выполняется обновление времени всех смен в рамках одной транзакции.

8. Формирование ответа:

   1. возвращается список обновлённых смен;

   2. статус `200 OK`.

9. Логирование:

   1. пользователь, список `shiftIds`, новые значения времени, результат операции.

</td>
</tr>
</table>

## 3\.5 Метод назначения отсутствий (Руководитель офиса)

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

</td>
</tr>
<tr>
<td>

absenceTypeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор типа отсутствия

</td>
</tr>
<tr>
<td>

startOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата начала отсутствия

</td>
</tr>
<tr>
<td>

endOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата конца отсутствия

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "employeeId": 101,
  "absenceTypeId": 1,
  "startOn": "2026-05-10",
  "endOn": "2026-05-11"
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

Id

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

```
absentOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата остутствия

</td>
</tr>
<tr>
<td>

absenceType

</td>
<td>

object

</td>
<td>

да

</td>
<td>

Тип отсутствия(объект)

</td>
</tr>
<tr>
<td>

```
absenceType.id
```

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор типа отсутствия

</td>
</tr>
<tr>
<td>

absenceType.code

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Значение `code` внутри объекта `absenceType`.

</td>
</tr>
<tr>
<td>

[absenceType.name](http://absenceType.name)

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Значение `name` внутри объекта `absenceType`.

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
    "id": 123,
    "employeeId": 101,
    "absentOn": "2026-05-10",
    "absenceType": {
      "id": 1,
      "code": "vacation",
      "name": "отпуск"
    }
  },
  {
    "id": 124,
    "employeeId": 101,
    "absentOn": "2026-05-11",
    "absenceType": {
      "id": 1,
      "code": "sick",
      "name": "Больничный"
    }
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
  "code": "ABSENCE_OVERLAP",
  "message": "Пересечение с существующей сменой или отсутствием"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает тело запроса `AbsencesCreateRequestDto`.

2. **Проверка JWT и прав доступа:**

   1. пользователь авторизован;

   2. пользователь имеет право управлять отсутствиями (например, HR или Руководитель).

3. **Валидация входных данных:**

   1. `employeeId`, `absenceTypeId`, `startOn`, `endOn` обязательны;

   2. формат даты корректный;

   3. `startOn` \<= `endOn`;

   4. `absenceTypeId` существует в справочнике типов отсутствий.

4. **Валидация сотрудника на группу подмены**

   1. Выполняется **только если** role=OFFICE_HEAD в jwt

   2. По employeeId из смен выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Проверяется состоит ли сотрудник в группе подмены

   4. Если `substitutionGroup = true`, операция удаления смены запрещается.

      1. Возвращается `400 Bad Request`

5. **Проверка пересечений (Коллизии):**

   1. Проверяется отсутствие пересечения указанного диапазона дат (`startOn` - `endOn`) с уже существующими **сменами** сотрудника.

   2. Проверяется отсутствие пересечения с другими **отсутствиями** сотрудника (VACATION, SICK и т.д.).

   3. *Примечание: Проверка на рабочий график офиса не выполняется.*

6. **При обнаружении ошибки:**

   1. операция прерывается;

   2. отсутствие не создаётся;

   3. возвращается 400 / 403 / 404 / 409 с описанием причины.

7. **При успешной валидации:**

   1. генерируются записи отсутствий на каждый день диапазона (разбивка периода на дни);

   2. сохранение записей в БД в рамках одной транзакции;

   3. формируется список `AbsenceResponseDto`;

   4. возвращается статус `201 Created`.

8. **Логирование:**

   1. пользователь;

   2. `employeeId`, тип отсутствия, даты;

   3. результат операции.

</td>
</tr>
</table>

## 3\.6 Метод удаления отсутствий (Руководитель офиса)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

DELETE /absences?id=123&id=124&id=125

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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "ABSENCE_NOT_FOUND",
  "message": "Отсутствие не найдено"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает список идентификаторов отсутствий `absenceIds` из query-параметров запроса (`id`).

2. Проверка JWT и прав доступа пользователя на удаление отсутствий.

3. Валидация query-параметра `id`:

   1. корректный формат идентификатора.

4. По каждому `absenceId`:

   1. проверка существования отсутствия

5. Валидация сотрудника на группу подмены

   1. Выполняется **только если** role=OFFICE_HEAD в jwt

   2. По employeeId из отсутствий выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Проверяется состоит ли сотрудник в группе подмены

   4. Если `substitutionGroup = true`, операция удаления смены запрещается.

      1. Возвращается `400 Bad Request`.

6. При обнаружении хотя бы одной ошибки:

   1. операция прерывается;

   2. ни одно отсутствие не удаляется;

   3. возвращается соответствующая ошибка (400 / 403 / 404).

7. При успешной валидации всех отсутствий:

   1. выполняется удаление всех записей в рамках одной транзакции.

8. Формирование ответа:

   1. тело отсутствует;

   2. статус `204 No Content`.

9. Логирование:

   1. пользователь, список `absenceIds`, результат операции.

</td>
</tr>
</table>

## 3\.7. Метод назначения смены (Территориальный менеджер)

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

```
startOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата начала смены

</td>
</tr>
<tr>
<td>

```
endOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата конца смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (time)

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

endAt

</td>
<td>

string (time)

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
  "employeeId": 10,
  "officeId": 4,
  "startOn": "2026-04-01",
  "endOn": "2026-04-01", 
  "startAt": "09:00",
  "endAt": "18:00"
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

shiftId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

scheduledOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (time)

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

endAt

</td>
<td>

string (time)

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
  "id": 123,
  "employeeId": 10,
  "officeId": 4,
  "scheduledOn": "2026-04-01",
  "startAt": "09:00",
  "endAt": "18:00"
}
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
  "code": "SHIFT_OVERLAP",
  "message": "Пересечение с другой сменой"
}
```

</td>
</tr>
</table>

**Схема работы метода**

\<схему нарисовать. Если логика сложная, то схема обязательна.>

**Описание работы метода**

1. Контроллер принимает тело запроса CreateShiftRequest.

2. Проверка JWT и прав доступа:

   1. пользователь авторизован;

   2. пользователь имеет право назначать смены в данном офисе.

3. Валидация входных данных:

   1. employeeId, officeId, date, startAt, endAt обязательны;

   2. формат даты и времени корректный;

   3. startAt \< endAt.

4. Интеграция с сервисом офисов:

   1. выполняется запрос

      GET /working-hours/by-office/\{officeId};

   2. получаются рабочие часы офиса по дням недели;

   3. определяется день недели для date;

   4. проверяется:

      1. офис работает в эту дату;

      2. время смены попадает в допустимый интервал;

      3. соблюдены ограничения бизнес-правил (максимальная длительность, выходные, производственный календарь).

5. Проверка пересечений:

   1. отсутствие пересечения с другими сменами сотрудника (в том числе в других офисах);

   2. отсутствие пересечения с отсутствиями (VACATION, SICK и т.д.).

6. При обнаружении ошибки:

   1. операция прерывается;

   2. смена не создаётся;

   3. возвращается 400 / 403 / 404 с описанием причины.

7. При успешной валидации:

   1. создаётся запись смены в рамках транзакции;

   2. формируется ShiftResponseDto;

   3. возвращается статус 201 Created.

8. Логирование:

   1. пользователь;

   2. employeeId, officeId, date;

   3. результат операции;

   4. персональные данные сотрудников в логах не фиксируются (маскирование).





**Если роль = ROLE_REGION_MANAGER:**

1. Контроллер принимает тело запроса CreateShiftRequest.

2. Проверка JWT и прав доступа

   1. пользователь авторизован;

   2. роль = ROLE_REGION_MANAGER (территориальный менеджер);

   3. пользователь имеет право назначать смены в офисах **только своего региона**.

3. Валидация входных данных:

   1. employeeId, officeId, startOn, endOn, startAt, endAt обязательны;

   2. формат даты и времени корректный;

   3. startAt \< endAt;

   4. startOn \< endOn.

4. Валидация сотрудника группы подмены

   1. Выполняется **только если** role = ROLE_REGION_MANAGER в jwt 

   2. Выполняется запрос в Employee Service GET / employees/by-user/\{userId}

   3. Проверяется substitutionGroup = true

      1. Если сотрудник не в группе подмены, возвращается *400 Bad Request*

   4. Проверяется регион сотрудника, которому добавляют смену

      1. регион сотрудника == регион ТМа

5. Интеграция с сервисом офисов

   1. Выполняется запрос GET /working-hours/by-office/\{officeId};

   2. Получение регламента работы офиса по дням недели;

   3. Для каждого дня периода проверяется:

      1. офис работает в эту дату;

      2. время смены попадает в допустимый диапазон;

      3. соблюдены ограничения бизнес-правил (регламент офиса).

6. Проверка пересечений

   1. отсутствие пересечения с другими сменами сотрудника (в разных офисах);

   2. отсутствие пересечения с отсутствиями (VACATION, SICK и т.д.).

   3. При обнаружении пересечения по времени операция прерывается, возвращается 409 Conflict с описанием конфликта.

   4. если составная смена - на фронте показывается предупреждение

   5. В одном офисе может быть **только одна смена** в день 

7. При успешной валидации:

   1. Период разбивается на отдельные дни;

   2. Генерируются записи смен для каждого дня;

   3. Все записи сохраняются в БД в рамках одной транзакции;

   4. Формируется список ShiftResponseDto;

   5. Возвращается статус 201 Created с массивом созданных смен.

</td>
</tr>
</table>

## 3\.8. Метод удаления смены (Территориальный менеджер)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

DELETE /shifts?Id=123&d=124&Id=125



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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "SHIFTS_NOT_FOUND",
  "message": "Смены не найдены"
}
```

</td>
</tr>
</table>

**Схема работы метода**

\<схему нарисовать. Если логика сложная, то схема обязательна.>

**Описание работы метода**

1. Контроллер принимает список идентификаторов смен `shiftIds` из query-параметров запроса.

2. Проверка JWT и прав доступа пользователя на удаление смен.

3. Валидация query-параметра `shiftIds`:

   -  список не пустой;

   -  корректный формат идентификаторов.

4. По каждому `shiftId`:

   -  проверка существования смены;

   -  проверка доступности смены пользователю (права, офис, роль).

5. При обнаружении хотя бы одной ошибки:

   -  операция прерывается;

   -  ни одна смена не удаляется;

   -  возвращается соответствующая ошибка (`400 / 403 / 404`).

6. При успешной валидации всех смен:

   -  выполняется удаление всех смен в рамках одной транзакции.

7. Формирование ответа:

   -  тело отсутствует;

   -  статус `204 No Content`.

8. Логирование:

   -  пользователь, список `shiftIds`, результат операции.

**Если роль = ROLE_REGION_MANAGER:**

1. Контроллер принимает список идентификаторов смен `shiftIds` из query-параметров запроса.

2. Проверка JWT и прав доступа

   1. Пользователь авторизован.

   2. Роль = `ROLE_REGION_MANAGER`.

   3. Пользователь имеет право управлять сменами только в рамках своего региона.

   4. Из JWT берётся `userId`.

   5. Выполняется: GET /employees/by-user/\{userId}

   6. Возвращается `EmployeeDto` с `regionId` ТМ'а.

3. Валидация входных данных

   1. передан минимум один shiftId

   2. корректный формат идентификатора

4. Получение сотрудника ГП

   1. по каждому shiftId определяется employeeId

   2. Выполняется запрос GET/employees?id=\{employeeId}

   3. Проверка:

      1. Сотрудник существует

      2. Сотрудник принадлежит ГП: substitutionGroup = true.

         -  Если substitutionGroup = false: 400 Bad Request

5. Проверка региона

   1. Регион ТМа == регион сотрудника

   2. Регион ТМа ==  регион офиса смены

6. По каждому shiftId

   1. Проверка существования смены

   2. Проверка доступности смены пользователю

   3. Офис смены принадлежит региону ТМ

7. Если обнаружена хотя бы одна ошибка:

   1. операция прерывается

   2. изменения не сохраняются

8. Формирование ответа

   1. тело отсутствует

   2. 204 No Content

9. Логирование:

   1. пользователь, список `shiftIds`, результат операции.

</td>
</tr>
</table>

## 3\.9. Метод редактирования смены (Территориальный менеджер)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "startAt": "10:00",
  "endAt": "19:00"
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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

id

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

ID смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

ID сотрудника

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

да

</td>
<td>

ID офиса

</td>
</tr>
<tr>
<td>

date

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
<tr>
<td>

startAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время начала

</td>
</tr>
<tr>
<td>

endAt

</td>
<td>

string (HH:mm)

</td>
<td>

да

</td>
<td>

Время окончания

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "id": 123,
  "employeeId": 45,
  "officeId": 3,
  "date": "2026-04-10",
  "startAt": "10:00",
  "endAt": "19:00"
}
{
  "id": 124,
  "employeeId": 45,
  "officeId": 3,
  "date": "2026-04-10",
  "startAt": "10:00",
  "endAt": "19:00"
}
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
  "code": "SHIFTS_NOT_FOUND",
  "message": "Смены не найдены"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает список идентификаторов смен `shiftIds` из query-параметров и тело запроса с новыми значениями времени.

2. Проверка JWT и прав доступа пользователя на редактирование смен.

3. Валидация входных данных:

   1. `shiftIds` не пустой;

   2. корректный формат идентификаторов;

   3. `startAt < endAt`.

4. По каждому `shiftId`:

   1. проверка существования смены;

   2. проверка доступности смены пользователю (права, офис, роль);

   3. Интеграция с сервисом офисов:

      -  выполняется запрос

         GET /working-hours/by-office/\{officeId};

      -  получаются рабочие часы офиса по дням недели;

      -  определяется день недели для date;

      -  проверяется:

         -  офис работает в эту дату;

         -  время смены попадает в допустимый интервал;

         -  соблюдены ограничения бизнес-правил (максимальная длительность, выходные, производственный календарь).

   4. проверка бизнес-правил (пересечения, ограничения периода).

5. При обнаружении хотя бы одной ошибки:

   1. операция прерывается;

   2. ни одна смена не изменяется;

   3. возвращается соответствующая ошибка (`400 / 403 / 404`).

6. При успешной валидации всех смен:

   1. выполняется обновление времени всех смен в рамках одной транзакции.

7. Формирование ответа:

   1. возвращается список обновлённых смен;

   2. статус `200 OK`.

8. Логирование:

   1. пользователь, список `shiftIds`, новые значения времени, результат операции.

**Если роль = ROLE_REGION_MANAGER:**

1. Проверка JWT и прав пользователя

   1. Пользователь авторизован

   2. Роль = ROLE_REGION_MANAGER

   3. Пользователь имеет право редактировать смены **только в рамках своего региона**

      1. из JWT берётся userId

      2. Выполняется GET/employees/by-user/\{userId}

      3. Возвращается EmployeeDto с regionId ТМ

2. Валидация входных данных

   1. `shiftIds` не пустой;

   2. корректный формат идентификаторов;

   3. `startAt < endAt`. 

3. Получение сотрудника

   1. Выполняется запрос в Employee Service GET /employees?id=\{employeeId}

      1. Сотрудник существует

      2. Сотрудник активен

      3. сотрудник принадлежит ГП: substitutionGroup = true. Иначе: 400 Bad Request

4. Проверка региона сотрудника

   1. Регион ТМа == регион сотрудника ГП

   2. Регион ТМа == регион офиса смены

5. По каждому `shiftId`:

   1. проверка существования смены;

   2. проверка доступности смены пользователю;

   3. Офис принадлежит региону ТМа

   4. Интеграция с сервисом офисов:

      -  выполняется запрос

         GET /working-hours/by-office/\{officeId};

      -  получаются рабочие часы офиса по дням недели;

      -  определяется день недели для date;

      -  проверяется:

         -  офис работает в эту дату;

         -  время смены попадает в допустимый интервал;

         -  соблюдены ограничения бизнес-правил (максимальная длительность, выходные, производственный календарь).

   5. проверка бизнес-правил (пересечения, ограничения периода).

6. При обнаружении хотя бы одной ошибки:

   1. Операция прерывается

   2. изменения не сохраняются

   3. возвращается соответствующая ошибка (400, 403, 404)

7. Формирование ответа

   1. Возвращается список обновлённых смен

   2. Статус 200 OK

8. Логирование

   1. пользователь

   2. список shiftIds

   3. новые назначения времени

   4. новый офис назначения

   5. результат операции

</td>
</tr>
</table>

## 3\.10. Метод отправки уведомления сотруднику об изменении смены

<table header="row">
<tr>
<td>

**Формат запроса (POST/shifts;  DELETE/shifts/(shiftid);   PUTCH/shifts/(shiftid);  - при выполнении любого из данных запросов должен ассинхронно затем выполняться функционал отправки записи о создании уведомления** 

```
```

```
```

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

date

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "employeeId": 10,
  "officeId": 4,
  "date": "2026-04-01",
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

shiftId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

date

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата смены

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "id": 123,
  "employeeId": 10,
  "officeId": 4,
  "date": "2026-04-01",
}
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
  "code": "400",
  "message": "Пересечение с другой сменой"
}
```

</td>
</tr>
</table>

**Схема работы метода**

\<схему нарисовать. Если логика сложная, то схема обязательна.>

**Описание работы метода**

Описать алгоритм по шагам, с указанием всех веток ошибок, с указанием маппингов (преобразований) в том числе по справочникам, формирование выходного объекта, логирование,  учет правил маскирования.

</td>
</tr>
</table>

## 3\.11. Метод получения табеля для офиса

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

officeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор офиса

</td>
</tr>
<tr>
<td>

positionId

</td>
<td>

integer (int64)

</td>
<td>

нет

</td>
<td>

Идентификатор должности

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

Календарный год

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

Порядковый номер месяца (1–12)

</td>
</tr>
</table>

**Пример запроса**

GET /timesheets/by-office/5?year=2026&month=3

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

Объект с записями о рабочем времени на конкретный день, сгруппированный по сотрудникам

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
    "Менеджер по работе с клиентами": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ],
    "Кассир": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ],
    "Менеджер по работе с юридическими лицами": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ]
  }
}
```

</td>
</tr>
</table>

**Пример ответа пустого списка ....**

<table header="row">
<tr>
<td>

```
data: {}
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
  "code": "MISSING_REQUIRED_PARAMETER",
  "message": "Не заполнен обязательный параметр month"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. **Контроллер принимает параметры запроса:**

   1. `officeId` (path-переменная);

   2. `positionId`, `year`, `month` (query-параметры).

2. **Проверка JWT и прав доступа:**

   1. пользователь авторизован (токен валиден и не просрочен) при нарушении **401 Unauthorized**; 

   2. пользователь имеет право на редактирование табеля в данном офисе при нарушении - **403 Forbidden**.

   3. эндпоинт доступен для роли руководителя офиса.

3. **Валидация входных данных и определение границ:**

   1. `officeId`, `year`, `month` обязательны к заполнению;

   2. формат `year` (ГГГГ) и `month` (1–12) корректен;

   3. `officeId` и `positionId` (если передан) имеют корректный формат;

   4. при нарушении какого либо из пунктов - **400 Bad Request.**

4. **Определение дат (на стороне сервера):**

   1. Бэкенд вычисляет текущую дату сервера (**today**), первое число запрашиваемого месяца (**startDate**) и последнее число (**endDate**).

   2. Для валидации дат используется **метод «Срединного часового пояса»** (например, Красноярск/Новосибирск, UTC+7):

      -  **Для Владивостока (UTC+10/12)**: Когда там наступает утро (например, 08:00), в «середине» уже перевалило за полночь. Это значит, что `today` на сервере обновился, и восточные регионы могут вносить данные без задержек.

      -  **Для Москвы (UTC+3)**: Когда в Москве вечер (например, 20:00), в «середине» уже наступили следующие сутки. Однако, за счет того, что выбрана «середина», а не самый крайний пояс (Камчатка), окно для случайного или намеренного ввода данных «на завтра» для москвичей остается минимальным.

5. **Сбор ID сотрудников и интеграция с Office Service:**

   1. На основе **комбинации параметров year и month** определяются тип месяца(текущий/прошедший/будущий) 

   2. Далее выполняется следующая логика:

      1. Если запрашиваемый месяц является **прошедшим**:

         1. `shiftLimitDate = endDate` (план берется за весь месяц целиком).

         2. Выполняется запрос к локальной БД (таблице `shifts`) для извлечения уникальных `employeeId` из плановых смен данного офиса (`officeId`) в интервале `[startDate, shiftLimitDate]`.

         3. Shifts сохраняется во временную коллекцию для дальнейшей логики.

         4. Выполняется запрос к локальной БД (таблице work_times) для извлечения уникальных `employeeId` из записей рабочего времени данного офиса (`officeId`) в интервале `[startDate, shiftLimitDate]`.

         5. Worktimes сохраняется во временную коллекцию для дальнейшей логики.

         6. Выполняется объединение списков employeeId.

         7. Получается массив employeeIds\[\].

      2. Если запрашиваемый месяц является **текущим**:

         1. `shiftLimitDate = today` (план берется только до сегодняшнего дня включительно)

         2. Выполняется запрос к локальной БД (таблице `shifts`) для извлечения уникальных `employeeId` из плановых смен данного офиса (`officeId`) в интервале `[startDate, shiftLimitDate]`.

         3. Shifts сохраняется во временную коллекцию для дальнейшей логики.

         4. Выполняется вызов `GET /offices/{officeId}/employees`. 

         5. employeeIds формируются как объединение двух множеств: 

            1. сотрудников из смен в диапазоне от startDate до shiftLimitDate

            2. сотрудников, привязанных к офису, полученных через GET /offices/\{officeId}/employees.

         6. Получается массив employeeIds\[\].

      3. Если запрашиваемый месяц является **будущим**:

         1. shiftLimitDate не определяется.

         2. Запрос к shifts не выполняется.

         3. Employee Service не вызывается.

         4. Возвращается пустой объект `data`

      4. Если массив employeeIds\[\] пуст (но статус 200) -- возвращается пустой объект `data`.

6. **Интеграция с Employee Service:**

   1. Выполняется запрос `GET /employees?id={employeeIds[]}&substitutionGroup=false`. (Если передан параметр `positionId`, то запрос принимает вид `GET /employees?id={employeeIds[]}&substitutionGroup=false&positionId={positionId}`).

   2. Из ответа извлекаются данные сотрудников: `employeeId`, `fullName`, `position`.

   3. Для каждого сотрудника получается значение `weekly_minutes` (из связанного справочника `working_norm`).

   4. **Логика подготовки filteredIds:** В дальнейшую обработку идут только те ID, которые вернул Employee Service.

7. **Получение данных из локальной бд по filteredIds в заданном периоде:**

   1. **Shifts:** Из ранее полученного списка смен извлекаются записи, соответствующие только тем сотрудникам, которые вошли в `filteredIds[]`.

   2. Бэкенд выполняет запрос к своим локальным таблицам:

      1. **Absences:** Получение всех пропусков сотрудников за весь месяц (`[startDate, endDate]`).

   3. Данные сохраняются во временные коллекции для сопоставления.

8. **Расчет нормативных показателей для каждого сотрудника:**

   1. Из конфигурационного файла считывается количество рабочих дней в неделе

      `daysPerWeek` (по умолчанию -- **5**).

   2. Рассчитывается дневная норма: dailyNormMinutes = weekly_minutes / daysPerWeek

   3. Определяется количество рабочих дней в месяце как количество календарных дней,

      

      приходящихся на **понедельник–пятницу** - calendarWorkingDaysInMonth

   4. Для каждого сотрудника определяется количество дней отсутствия

      

      absenceDays -- количество записей Absence,

      приходящихся на **рабочие дни** месяца.

   5. absence **нужно учитывать только для рабочих дней**: absenceDays = count(absence.dateOn ∈ workingDays)

   6. Рассчитывается персональная норма рабочих дней

      

      effectiveWorkingDays = calendarWorkingDaysInMonth - absenceDays

   7. Рассчитывается месячная нормативная нагрузка

      

      normMinutes = effectiveWorkingDays \* dailyNormMinutes

9. **Получение подтвержденных данных из локальной БД work_times:**

   1. **Если месяц предыдущий используется уже существующий список worktimes** и пункт b пропускается.

   2. Выполняется запрос к таблице `work_times`:

      

      `SELECT * FROM work_times`

      `WHERE office_id = {:officeId}`

      `AND employee_id IN (:filteredIds)`

      `AND date_on BETWEEN {:startDate}` 

      `AND {:shiftLimitDate}`

10. **Агрегация данных и формирование массива days:**

    1. Для каждого дня месяца в цикле по каждому сотруднику применяется последовательная проверка (логика `oneOf` в схеме):

       1. **Проверка 1 (Отсутствия):**

          -  Если есть запись из `Absence`, формируется `AbsenceDayDto`.

          -  в поле `type записывается` `"absence"` 

          -  Дальнейшие проверки для этого дня пропускаются.

       2. **Проверка 2 (Подтвержденная смена)**:

          -  Если найдена запись в локальной БД по employeeId, date и officeId, формируется WorkDayDto. isDraft = false; 

          -  workedMinutes берется напрямую из поля `worked_minutes таблицы work_time`;

          -  officeId из поля office_id; 

          -  `workTime` = вычисляется путем конвертации `worked_minutes` в формат `HH:mm` (например, 480 мин -> "08:00");

          -  в поле `type записывается` `"work".`

       3. **Проверка 3 (Плановая смена)**:

          -  Если есть запись из Shifts, формируется WorkDayDto. isDraft = true;

          -  workedMinutes = вычисляется как разница между метками времени конца и начала смены (`endAt - startAt`) в минутах; 

          -  `workTime` = результат вычисления разницы, приведенный к строковому формату `HH:mm;`

          -  `в поле type записывается "work".`

       4. **Проверка 4 (Пустой день)**:

          -  Если данных нет ни в одном источнике cоздаем EmptyDayDto (`type: "empty"`, `dateOn: {текущий день итерации цикла}`).

11. **Расчет Summary:**

    1. totalMinutes: Сумма всех минут из итогового массива days (только WorkDay).

    2. totalWorkTime: Форматирование totalMinutes в строку HH:mm.

    3. balanceMinutes: totalMinutes - normMinutes.

    4. balanceTime: Форматирование разницы в строку со знаком +/-HH:mm.

    5. monthlyNorm: Нормативное рабочее время за месяц в строковом формате (например, `"160:00"`). Вычисляется на основе `normMinutes`.

    6. normMinutes: Месячная нормативная нагрузка в минутах. Рассчитывается как `calendarWorkingDaysInMonth * dailyNormMinutes`.

12. **Агрегация и структурирование ответа (TimesheetResponse):**

    После выполнения всех расчетов и сопоставления данных по дням, бэкенд формирует итоговую структуру.

    **Группировка данных:**

    -  **1-й уровень -- по названию должности:** В качестве ключа `map` используется [position.name](http://position.name). Если у сотрудников разные должности, они разделяются по соответствующим ключам.

    -  **2-й уровень -- список сотрудников:** Внутри каждой должности формируется массив объектов `EmployeeTimesheetDto`.

    **Для каждого сотрудника формируется EmployeeTimesheetDto:**

    -  **Личные данные:** `employeeId`, `fullName` и объект `position` (id, code, name).

    -  **Массив** `days[]`**:** Содержит **ровно один объект на каждую календарную дату** запрашиваемого месяца (от 1-го до 28–31 числа).

       -  **Сортировка:** Дни внутри массива должны быть строго отсортированы по `dateOn` (ASC).

       -  **Типизация (oneOf):** Каждый элемент -- это либо `WorkDayDto` (факт/план), либо `AbsenceDayDto` (отсутствие), либо `EmptyDayDto` (если данных нет).

    -  **Объект** `summary`**:** Итоговые показатели сотрудника за месяц (нормативы, фактически отработанное время и баланс).

    **Формирование финального объекта:**

    Ответ собирается в формате **TimesheetResponse**:

    -  Поле `data` представляет собой `Map<String, List<EmployeeTimesheetDto>>`, где ключ -- это [position.name](http://position.name).

    -  **Сортировка сотрудников:** Внутри каждой должности список сотрудников должен быть отсортирован по `fullName` в алфавитном порядке.

13. **Форматирование ответа:**

    1. Все строковые представления времени содержат ведущие нули (формат `HH:mm`).

</td>
</tr>
</table>

## 3\.12 Метод получения табеля для группы подмены

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

positionId

</td>
<td>

integer (int64)

</td>
<td>

нет

</td>
<td>

Идентификатор должности

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

Календарный год

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

Порядковый номер месяца (1–12)

</td>
</tr>
</table>

**Пример запроса**

GET /timesheets/by-region/5?positionId=1&year=2026&month=3

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

Объект с записями о рабочем времени на конкретный день, сгруппированный по сотрудникам

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
    "Менеджер по работе с клиентами": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ],
    "Кассир": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ],
    "Менеджер по работе с юридическими лицами": [
      {
        "employeeId": 0,
        "fullName": "string",
        "position": {
          "id": 0,
          "code": "string",
          "name": "string"
        },
        "days": [
          {
            "type": "work",
            "dateOn": "2026-02-26",
            "officeId": 0,
            "workTime": "08:00",
            "workedMinutes": 480,
            "isDraft": true
          },
          {
            "type": "absence",
            "dateOn": "2026-02-26",
            "absenceType": {
              "id": 0,
              "code": "sick",
              "name": "больничный"
            }
          },
          {
            "type": "empty",
            "dateOn": "2026-02-26"
          }
        ],
        "summary": {
          "totalWorkTime": "120:00",
          "totalMinutes": 0,
          "monthlyNorm": "160:00",
          "normMinutes": 0,
          "balanceTime": "-40:00",
          "balanceMinutes": 0
        }
      }
    ]
  }
}
```

</td>
</tr>
</table>

**Пример ответа пустого списка ....**

<table header="row">
<tr>
<td>

```
data: {}
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
  "code": "MISSING_REQUIRED_PARAMETER",
  "message": "Не заполнен обязательный параметр month"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. **Контроллер принимает параметры запроса:**

   1. `regionId` (path-переменная);

   2. `positionId`, `year`, `month` (query-параметры).

2. **Проверка JWT и прав доступа:**

   1. пользователь авторизован (токен валиден и не просрочен) при нарушении **401 Unauthorized**; 

   2. пользователь имеет право на редактирование табеля группы подмены в данном регионе при нарушении - **403 Forbidden**.

   3. эндпоинт доступен для роли территориального менеджера.

3. **Валидация входных данных и определение границ:**

   1. `regionId`, `year`, `month` обязательны к заполнению;

   2. формат `year` (ГГГГ) и `month` (1–12) корректен;

   3. `regionId` и `positionId` (если передан) имеют корректный формат;

   4. при нарушении какого либо из пунктов - **400 Bad Request.**

4. **Определение дат (на стороне сервера):**

   1. Бэкенд вычисляет текущую дату сервера (**today**), первое число запрашиваемого месяца (**startDate**) и последнее число (**endDate**).

   2. Для валидации дат используется **метод «Срединного часового пояса»** (например, Красноярск/Новосибирск, UTC+7):

      -  **Для Владивостока (UTC+10/12)**: Когда там наступает утро (например, 08:00), в «середине» уже перевалило за полночь. Это значит, что `today` на сервере обновился, и восточные регионы могут вносить данные без задержек.

      -  **Для Москвы (UTC+3)**: Когда в Москве вечер (например, 20:00), в «середине» уже наступили следующие сутки. Однако, за счет того, что выбрана «середина», а не самый крайний пояс (Камчатка), окно для случайного или намеренного ввода данных «на завтра» для москвичей остается минимальным.

   3. **Валидация будущего периода:** Если комбинация параметров `year` и `month` указывает на период позже текущего календарного месяца, возвращается пустой массив days.

   4. **Определение границы плановых смен (shiftLimitDate):**

      1. Если запрашиваемый месяц является **текущим**, то `shiftLimitDate = today` (план берется только до сегодняшнего дня включительно).

      2. Если запрашиваемый месяц является **прошедшим**, то `shiftLimitDate = endDate` (план берется за весь месяц целиком).

   5. Данная дата (`shiftLimitDate`) используется в качестве параметра `to` при запросе в **Schedule Service** для фильтрации плановых смен.

5. **Интеграция с Employee Service:**

   1. Выполняется запрос `GET /employees?regionId={regionId}&substitutionGroup=true.(Если передан параметр positionId то GET /employees?regionId={regionId}&substitutionGroup=true&positionId={positionId})`

   2. Из ответа извлекаются данные сотрудников: `employeeId`, `fullName`, `position`.

   3. Для каждого сотрудника получается значение `weekly_minutes` (из связанного справочника `working_norm`).

   4. Извлекается список employeeIds\[\] из EmployeeResponseDt**o**

6. **Получение данных из локальной бд по employeeIds\[\] в заданном периоде:**

   1. Бэкенд выполняет запросы к своим локальным таблицам:

      1. **Absences:** Получение всех пропусков за весь месяц.

      2. **Shifts:** Получение плановых смен в интервале `[startDate, shiftLimitDate]`.

   2. Данные сохраняются во временные коллекции для сопоставления.

7. **Расчет нормативных показателей для каждого сотрудника:**

   1. Из конфигурационного файла считывается количество рабочих дней в неделе

      `daysPerWeek` (по умолчанию -- **5**).

   2. Рассчитывается дневная норма: dailyNormMinutes = weekly_minutes / daysPerWeek

   3. Определяется количество рабочих дней в месяце как количество календарных дней,

      

      приходящихся на **понедельник–пятницу** - calendarWorkingDaysInMonth

   4. Для каждого сотрудника определяется количество дней отсутствия

      

      absenceDays -- количество записей Absence,

      приходящихся на **рабочие дни** месяца.

   5. absence **нужно учитывать только для рабочих дней**: absenceDays = count(absence.dateOn ∈ workingDays)

   6. Рассчитывается персональная норма рабочих дней

      

      effectiveWorkingDays = calendarWorkingDaysInMonth - absenceDays

   7. Рассчитывается месячная нормативная нагрузка

      

      normMinutes = effectiveWorkingDays \* dailyNormMinutes

8. **Получение подтвержденных данных из локальной БД (Worktime):**

   1. Выполняется запрос к таблице `worktime`: 

      `SELECT employee_id, date_on, MAX(office_id) as office_id, SUM(worked_minutes) as worked_minutes` 

      `FROM worktime`

      `WHERE employee_id IN (:employeeIds)`

      `AND date_on BETWEEN :startDate AND :shiftLimitDate` 

      `GROUP BY employee_id, date_on;`

9. **Агрегация данных и формирование массива days:**

   1. Для каждого сотрудника формируется массив `days[]` по принципу **«ровно один объект на одну дату»**. Для каждого дня месяца применяется последовательная проверка (логика `oneOf`):

      1. **Проверка 1 (Отсутствие):**

         -  Если на дату есть запись в `Absences`, создается `AbsenceDayDto`.

         -  в поле `type записывается` `"absence".` 

         -  Остальные данные за этот день игнорируются.

      2. **Проверка 2 (Подтвержденный факт):** Если в результате SQL-запроса есть запись на эту дату:

         -  Создается `WorkDayDto` с `isDraft: false`.

         -  `workedMinutes` берется готовой суммой из SQL.

         -  `officeId` берется из результата SQL (поле `MAX(office_id)`).

      3. **Проверка 3 (План):** Если факта нет, но есть плановые смены в `Shifts`:

         -  Создается `WorkDayDto` с `isDraft: true;`

         -  Если смен на одну дату несколько (сотрудник в один день работал в двух офисах), бэкенд **суммирует** их длительность в одно значение `workedMinutes;`

         -  `workTime` = вычисляется путем конвертации `worked_minutes` в формат `HH:mm` (например, 480 мин -> "08:00");

         -  в поле `type записывается` `"work"`

      4. **Проверка 4 (Пустой день):** 

         -  Если данных нет ни в одном источнике cоздаем EmptyDayDto (`type: "empty"`, `date: {date}`).

10. **Расчет Summary:**

    1. totalMinutes: Сумма всех минут из итогового массива days (только WorkDay).

    2. totalWorkTime: Форматирование totalMinutes в строку HH:mm.

    3. balanceMinutes: totalMinutes - normMinutes.

    4. balanceTime: Форматирование разницы в строку со знаком +/-HH:mm.

    5. monthlyNorm: Нормативное рабочее время за месяц в строковом формате (например, `"160:00"`). Вычисляется на основе `normMinutes`.

    6. normMinutes: Месячная нормативная нагрузка в минутах. Рассчитывается как `calendarWorkingDaysInMonth * dailyNormMinutes`.

11. **Агрегация и структурирование ответа (TimesheetResponse)**

    После выполнения всех расчетов и сопоставления данных по дням, бэкенд формирует итоговую структуру.

    **Группировка данных:**

    -  **1-й уровень -- по названию должности:** В качестве ключа `map` используется [position.name](http://position.name). Если у сотрудников разные должности, они разделяются по соответствующим ключам.

    -  **2-й уровень -- список сотрудников:** Внутри каждой должности формируется массив объектов `EmployeeTimesheetDto`.

    **Для каждого сотрудника формируется EmployeeTimesheetDto:**

    -  **Личные данные:** `employeeId`, `fullName` и объект `position` (id, code, name).

    -  **Массив** `days[]`**:** Содержит **ровно один объект на каждую календарную дату** запрашиваемого месяца (от 1-го до 28–31 числа).

       -  **Сортировка:** Дни внутри массива должны быть строго отсортированы по `dateOn` (ASC).

       -  **Типизация (oneOf):** Каждый элемент -- это либо `WorkDayDto` (факт/план), либо `AbsenceDayDto` (отсутствие), либо `EmptyDayDto` (если данных нет).

    -  **Объект** `summary`**:** Итоговые показатели сотрудника за месяц (нормативы, фактически отработанное время и баланс).

    **Формирование финального объекта:**

    Ответ собирается в формате **TimesheetResponse**:

    -  Поле `data` представляет собой `Map<String, List<EmployeeTimesheetDto>>`, где ключ -- это [position.name](http://position.name).

    -  **Сортировка сотрудников:** Внутри каждой должности список сотрудников должен быть отсортирован по `fullName` в алфавитном порядке.

12. **Форматирование ответа:**

    1. Все строковые представления времени содержат ведущие нули (формат `HH:mm`).

</td>
</tr>
</table>

## 3\.13 Метод назначения рабочего времени

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

dateOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата рабочего дня

</td>
</tr>
<tr>
<td>

workedMinutes

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Фактически отработанное время в минутах

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "items": [
    {
      "employeeId": 19,
      "officeId": 1,
      "dateOn": "2026-03-02",
      "workedMinutes": 480
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

items

</td>
<td>

array

</td>
<td>

да

</td>
<td>

Список записей отработанного времени

</td>
</tr>
</table>

**items\[\]**

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

Идентификатор записи рабочих часов

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

dateOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата рабочего дня

</td>
</tr>
<tr>
<td>

workedMinutes

</td>
<td>

integer

</td>
<td>

да

</td>
<td>

Фактически отработанное время в минутах

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
    "id": 585,
    "officeId": 1,
    "employeeId": 19,
    "dateOn": "2026-03-02",
    "workedMinutes": 480,
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
  "code": "MISSING_REQUIRED_PARAMETER",
  "message": "Не заполнен обязательный параметр month"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. **Контроллер принимает тело запроса:**

   1. `items[]` -- массив объектов подтверждения (сотрудник, дата, минуты, офис)

2. **Проверка JWT и прав доступа:**

   1. пользователь авторизован (токен валиден и не просрочен) при нарушении **401 Unauthorized**;

   2. пользователь имеет право редактировать табель учёта рабочего времени сотрудников, при нарушении - **403 Forbidden**.

   3. эндпоинт доступен для ролей руководителя офиса и территориального менджера.

3. **Валидация входных данных:**

   1. массив `items` обязателен к заполнению

   2. для каждого элемента в `items`:

      1. `employeeId, dateOn,officeId` обязательны

      2. `workedMinutes` должны быть в диапазоне от 0 до 1440 (количество минут в сутках)

   3. **валидация дат:** запрещено сохранять записи для будущих дат относительно текущего времени сервера

   4. при нарушении какого либо из пунктов - **400 Bad Request.**

4. **Транзакционная обработка:**

   1. бэкенд открывает транзакцию для обработки массива `items`

   2. для каждого элемента выполняется SQL-запрос с логикой `ON CONFLICT (employee_id, date_on, office_id)`:

      1. если записи для данного сотрудника на указанную дату в базе нет -- выполняется **INSERT** новой записи

      2. если запись уже существует -- выполняется **UPDATE** существующей записи (обновляется поле `worked_minutes`)

5. **Результат операции:**

   1. при успешном сохранении возвращается статус `200 OK`

   2. если хотя бы один элемент не прошел валидацию -- транзакция откатывается, возвращается `400 Bad Request`

</td>
</tr>
</table>

## 3\.14. Метод получения расписания по региону

<table header="row">
<tr>
<td>

**Формат запроса:**

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

да

</td>
<td>

Идентификатор региона сотрудников

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

Календарный год

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

Порядковый номер месяца (1–12)

</td>
</tr>
</table>

**Пример запроса**

GET / schedules/by-region/\{regionId}?&year=2026&month=2

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

Объект с расписанием, сгруппированным по должностям и сотрудникам

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
{
  "data": 
{
    "Менеджер по работе с клиентами": [
      {
        "employeeId": 101,
        "fullName": "Фадеева Надежда Богдановна",
        "phone": "+79990000001",
        "employeeCity": {
          "id": 5,
          "name": "Владивосток"
        },
        "shifts": [
          {
            "id": 1001,
            "scheduledOn": "2026-02-10",
            "startAt": "09:00",
            "endAt": "18:00",
            "office": {
              "id": 12,
              "address": "ул. Светланская, 16",
              "city": {
                "id": 5,
                "name": "Владивосток"
              }
            }
          },
          {
            "id": 1002,
            "scheduledOn": "2026-02-14",
            "startAt": "10:00",
            "endAt": "19:00",
            "office": {
              "id": 21,
              "address": "ул. Центральная, 8",
              "city": {
                "id": 6,
                "name": "Уссурийск"
              }
            }
          }
        ],
        "absences": [
          {
            "id": 501,
            "type": "VACATION",
            "startDate": "2026-02-20",
            "endDate": "2026-02-25"
          }
        ]
      },
      {
        "employeeId": 102,
        "fullName": "Смирнов Тимур Викторович",
        "phone": "+79990000002",
        "employeeCity": {
          "id": 5,
          "name": "Владивосток"
        },
        "shifts": [
          {
            "id": 1004,
            "scheduledOn": "2026-02-12",
            "startAt": "09:00",
            "endAt": "18:00",
            "office": {
              "id": 12,
              "address": "ул. Светланская, 16",
              "city": {
                "id": 5,
                "name": "Владивосток"
              }
            }
          }
        ],
        "absences": [
          {
            "id": 505,
            "type": "SICK",
            "startDate": "2026-02-17",
            "endDate": "2026-02-22"
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
  "code": "MISSING_REQUIRED_PARAMETER",
  "message": "Не заполнен обязательный параметр month"
}
```

</td>
</tr>
</table>

**Схема работы метода**







**Описание работы метода**

1. Контроллер принимает

   1. regionId -- из path.

   2. `year`, `month` -- из query-параметров.

2. Выполняется валидация параметров

   1. `regionId > 0`;

   2. `year` заполнен;

   3. `month ∈ [1..12]`.

   4. При нарушении -- **400 Bad Request** (`MISSING_REQUIRED_PARAMETER`, `INVALID_PARAMETER_VALUE`).

3. Проверяется авторизация

   1. Отсутствует JWT -- 401 Unauthorized.

4. Проверка прав доступа

   1. Пользователь должен иметь право просмотра расписания региона.

   2. При отсутствии прав -- **403 Forbidden**.

5. Определение периода выборки

   1. `fromDate` = первый день месяца;

   2. `toDate` = последний день месяца.

6. Получение сотрудников региона

   1. Интеграция с Employee Service:

      1. GET /employees?isSubstitutionGroup=true&regionId=\{regionId}.

   2. Возвращается список employeeId, относящихся к региону

7. Получение офисов региона

   1. Запрос к Office Service:

      1. GET /offices?regionId=\{regionId}.

   2. Возвращается список officeId, относящихся к региону.

8. Получение смен офисов

   1. Выборка смен по списку officeId и диапазону дат

   2. Формируется список смен офисов за период.

   3. Из смен извлекается множество уникальных employeeId.


   1.  

      

9. Формирование итогового списка сотрудников

   1. Выполняется слияние списков:

      1. Сотрудники, имеющие смены в офисах региона за период

      2. сотрудники подмены, относящиеся к региону (даже у которых нет смен)

   2. Формируется итоговый набор employeeIds.

10. В процессе слияния списков сотрудников для каждой сущности вычисляется логический флаг:

    -  Формирование контрольного множества: из ответа Employee Service извлекается Set идентификаторов сотрудников, имеющих отношение к запрошенному regionId и isSubstitutionGroup=true.

11. Обогащение данными сотрудников

    1. Интеграция с Employee Service:

       1. GET /employees?employeeIds=\[employeeIds\[\]\]

    2. Возвращаются для каждого сотрудника:

       1. ФИО;

       2. должность сотрудника;

       3. прочие атрибуты, необходимые для отображения.

    3. При ошибке интеграции -- 502 Bad Gateway.

12. Получение отсутствий

    1. Из Schedule Service выбираются отсутствия сотрудников за тот же период.

    2. Отсутствия маппятся к соответствующим employeeId.

13. Агрегация и структурирование данных

    1. Группировка:

       1. 1-й уровень -- по должности;

       2. 2-й уровень -- по employeeId.

    2. Для каждого сотрудника формируется EmployeeScheduleResponseDto:

       1. employeeId;

       2. fullName;

       3. substitutionGroup;

       4. shifts\[\];

       5. absences\[\].

14. Формирование ответа

    1. Ответ собирается в формате SchedulerResponse (map: positionName -> employeeId -> EmployeeScheduleResponseDto).

    2. Если данных нет -- возвращается пустой объект \{}.

15. Логирование

    1. `regionId`, `year`, `month`;

    2. количество смен и сотрудников.

16. Возврат результата

    1. 200 OK + агрегированный ответ расписания 

</td>
</tr>
</table>

## 3\.15. Метод создания отсутствий (Территориальный менеджер)

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

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

</td>
</tr>
<tr>
<td>

absenceTypeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор типа отсутствия

</td>
</tr>
<tr>
<td>

startOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата начала отсутствия

</td>
</tr>
<tr>
<td>

endOn

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата конца отсутствия

</td>
</tr>
</table>

**Пример запроса**

<table header="row">
<tr>
<td>

```
{
  "employeeId": 101,
  "absenceTypeId": 1,
  "startOn": "2026-05-10",
  "endOn": "2026-05-11"
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

Id

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор смены

</td>
</tr>
<tr>
<td>

employeeId

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор сотрудника

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

Идентификатор офиса

</td>
</tr>
<tr>
<td>

```
absentOn
```

</td>
<td>

string (date)

</td>
<td>

да

</td>
<td>

Дата остутствия

</td>
</tr>
<tr>
<td>

absenceType

</td>
<td>

object

</td>
<td>

да

</td>
<td>

Тип отсутствия(объект)

</td>
</tr>
<tr>
<td>

```
id
```

</td>
<td>

integer (int64)

</td>
<td>

да

</td>
<td>

Идентификатор типа отсутствия

</td>
</tr>
<tr>
<td>

code

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Значение `code` внутри объекта `absenceType`.

</td>
</tr>
<tr>
<td>

name

</td>
<td>

string

</td>
<td>

да

</td>
<td>

Значение `name` внутри объекта `absenceType`.

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
    "id": 123,
    "employeeId": 101,
    "absentOn": "2026-05-10",
    "absenceType": {
      "id": 1,
      "code": "vacation",
      "name": "отпуск"
    }
  },
  {
    "id": 124,
    "employeeId": 101,
    "absentOn": "2026-05-11",
    "absenceType": {
      "id": 1,
      "code": "sick",
      "name": "Больничный"
    }
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
  "code": "ABSENCE_OVERLAP",
  "message": "Пересечение с существующей сменой или отсутствием"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

\*Данный метод ТАКЖЕ используется при проставлении **отсутствий сотрудникам фронта**, тем самым ручка **переиспользуется** для **2 РОЛЕЙ** и необходимо проводить **валидцию** в зависимости **от РОЛИ В JWT** (для ТМа - роль REGION_MANAGER)

1. Контроллер принимает тело запроса `AbsencesCreateRequestDto`.

2. **Проверка JWT и прав доступа:**

   1. пользователь авторизован;

   2. пользователь имеет право управлять отсутствиями (существующие + роль REGION_MANAGER).

3. **Валидация входных данных:**

   1. `employeeId`, `absenceTypeId`, `startOn`, `endOn` обязательны;

   2. формат даты корректный;

   3. `startOn` \<= `endOn`;

   4. `absenceTypeId` существует в справочнике типов отсутствий.

4. **Валидация сотрудника на группу подмены**

   1. Выполняется **только если** role=REGION_MANAGER в jwt

   2. По employeeId из смен выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

   3. Проверяется состоит ли сотрудник в группе подмены

   4. Если `substitutionGroup = false`, операция удаления смены запрещается.

      1. Возвращается `400 Bad Request`

5. **Проверка пересечений (Коллизии):**

   1. Проверяется отсутствие пересечения указанного диапазона дат (`startOn` - `endOn`) с уже существующими **сменами** сотрудника.

   2. Проверяется отсутствие пересечения с другими **отсутствиями** сотрудника (VACATION, SICK и т.д.).

6. **При обнаружении ошибки:**

   1. операция прерывается;

   2. отсутствие не создаётся;

   3. возвращается 400 / 403 / 404 / 409 с описанием причины.

7. **При успешной валидации:**

   1. генерируются записи отсутствий на каждый день диапазона (разбивка периода на дни);

   2. сохранение записей в БД в рамках одной транзакции;

   3. формируется список `AbsenceResponseDto`;

   4. возвращается статус `201 Created`.

8. **Логирование:**

   1. пользователь;

   2. `employeeId`, тип отсутствия, даты;

   3. результат операции.   

</td>
</tr>
</table>

## 3\.16. Метод удаления отсутствий (Территориальный менеджер)

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

DELETE /absences?id=123&id=124&id=125

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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "ABSENCE_NOT_FOUND",
  "message": "Отсутствие не найдено"
}
```

</td>
</tr>
</table>

**Схема работы метода**





**Описание работы метода**

1. Контроллер принимает **список** идентификаторов отсутствий absenceIds из query-параметров запроса (id).

2. Проверка JWT и прав доступа пользователя на удаление отсутствий.

3. Валидация query-параметра id:

   1. корректный формат идентификатора.

4. По каждому absenceId:

   1. проверка **существования** отсутствия

   2. **Валидация** сотрудника на сотрудника фронта

   3. Выполняется только если role = **REGION_MANAGER в** jwt

      

      По employeeId из отсутствий выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

      1. Проверяется является ли сотрудник работкником фронта подмены

         

         Если **substitutionGroup = false**, операция удаления смены запрещается.

      2. Возвращается **400 Bad Request.**

   4. При обнаружении хотя бы одной ошибки:

      1. операция прерывается;

      2. ни одно отсутствие не удаляется;

      3. возвращается соответствующая ошибка **(400 / 403 / 404)**.

   5. При успешной валидации всех отсутствий:

      1. выполняется удаление всех записей в рамках одной транзакции.

5. Формирование ответа:

   1. тело отсутствует;

   2. статус **204 No Content**

</td>
</tr>
</table>

## 3\.17 Метода отображения расписания для сотрудника (общий грфик)

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





</td>
<td>





</td>
<td>





</td>
<td>





</td>
</tr>
</table>

**Пример запроса**

\-----

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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "ABSENCE_NOT_FOUND",
  "message": "Отсутствие не найдено"
}
```

</td>
</tr>
</table>

**Схема работы метода**



**Описание работы метода**

1. Контроллер принимает **список** идентификаторов отсутствий absenceIds из query-параметров запроса (id).

2. Проверка JWT и прав доступа пользователя на удаление отсутствий.

3. Валидация query-параметра id:

   1. корректный формат идентификатора.

4. По каждому absenceId:

   1. проверка **существования** отсутствия

   2. **Валидация** сотрудника на сотрудника фронта

   3. Выполняется только если role = **REGION_MANAGER в** jwt

      

      По employeeId из отсутствий выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

      1. Проверяется является ли сотрудник работкником фронта подмены

         

         Если **substitutionGroup = false**, операция удаления смены запрещается.

      2. Возвращается **400 Bad Request.**

   4. При обнаружении хотя бы одной ошибки:

      1. операция прерывается;

      2. ни одно отсутствие не удаляется;

      3. возвращается соответствующая ошибка **(400 / 403 / 404)**.

   5. При успешной валидации всех отсутствий:

      1. выполняется удаление всех записей в рамках одной транзакции.

5. Формирование ответа:

   1. тело отсутствует;

   2. статус **204 No Content**

</td>
</tr>
</table>

## 3\.18 Метода отображения расписания для сотрудника (график на день)

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





</td>
<td>





</td>
<td>





</td>
<td>





</td>
</tr>
</table>

**Пример запроса**

\-----

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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "ABSENCE_NOT_FOUND",
  "message": "Отсутствие не найдено"
}
```

</td>
</tr>
</table>

**Схема работы метода**



**Описание работы метода**

1. Контроллер принимает **список** идентификаторов отсутствий absenceIds из query-параметров запроса (id).

2. Проверка JWT и прав доступа пользователя на удаление отсутствий.

3. Валидация query-параметра id:

   1. корректный формат идентификатора.

4. По каждому absenceId:

   1. проверка **существования** отсутствия

   2. **Валидация** сотрудника на сотрудника фронта

   3. Выполняется только если role = **REGION_MANAGER в** jwt

      

      По employeeId из отсутствий выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

      1. Проверяется является ли сотрудник работкником фронта подмены

         

         Если **substitutionGroup = false**, операция удаления смены запрещается.

      2. Возвращается **400 Bad Request.**

   4. При обнаружении хотя бы одной ошибки:

      1. операция прерывается;

      2. ни одно отсутствие не удаляется;

      3. возвращается соответствующая ошибка **(400 / 403 / 404)**.

   5. При успешной валидации всех отсутствий:

      1. выполняется удаление всех записей в рамках одной транзакции.

5. Формирование ответа:

   1. тело отсутствует;

   2. статус **204 No Content**

</td>
</tr>
</table>

## 3\.19 Метода отображения своего расписания для сотрудника на месяц 

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

Id

</td>
<td>

array\<integer(int64)>

</td>
<td>

да

</td>
<td>

Список идентификаторов смен

</td>
</tr>
</table>

**Пример запроса**

\--------------

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

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
<tr>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
<td>

\-

</td>
</tr>
</table>

**Пример ответа без ошибки**

<table header="row">
<tr>
<td>

```
HTTP 204 No Content
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
  "code": "ABSENCE_NOT_FOUND",
  "message": "Отсутствие не найдено"
}
```

</td>
</tr>
</table>

**Схема работы метода**



**Описание работы метода**

1. Контроллер принимает **список** идентификаторов отсутствий absenceIds из query-параметров запроса (id).

2. Проверка JWT и прав доступа пользователя на удаление отсутствий.

3. Валидация query-параметра id:

   1. корректный формат идентификатора.

4. По каждому absenceId:

   1. проверка **существования** отсутствия

   2. **Валидация** сотрудника на сотрудника фронта

   3. Выполняется только если role = **REGION_MANAGER в** jwt

      

      По employeeId из отсутствий выполняется запрос GET /employees?id=\{employeeId} в Employee Service для получения данных сотрудника.

      1. Проверяется является ли сотрудник работкником фронта подмены

         

         Если **substitutionGroup = false**, операция удаления смены запрещается.

      2. Возвращается **400 Bad Request.**

   4. При обнаружении хотя бы одной ошибки:

      1. операция прерывается;

      2. ни одно отсутствие не удаляется;

      3. возвращается соответствующая ошибка **(400 / 403 / 404)**.

   5. При успешной валидации всех отсутствий:

      1. выполняется удаление всех записей в рамках одной транзакции.

5. Формирование ответа:

   1. тело отсутствует;

   2. статус **204 No Content**

</td>
</tr>
</table>
