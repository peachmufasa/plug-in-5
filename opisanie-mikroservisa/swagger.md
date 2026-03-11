---
order: 1
title: Swagger
---

```apidom
openapi: 3.0.3
info:
  title: Applications API
  description: API для управления заявками на подмену сотрудников
  version: 1.0.0

components:

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    ApplicationStatus:
      type: object
      properties:
        id:
          type: integer
          format: int64
        code:
          type: string
        name:
          type: string

    Position:
      type: object
      properties:
        id:
          type: integer
        code:
          type: string
        name:
          type: string

    Office:
      type: object
      properties:
        id:
          type: integer
        code:
          type: string
        name:
          type: string
        address:
          type: string
        cityId:
          type: integer
        regionId:
          type: integer
        headId:
          type: integer

    RecordRequest:
      type: object
      required:
        - positionId
        - quantity
        - startOn
        - endOn
        - startAt
        - endAt
      properties:
        positionId:
          type: integer
          format: int64
        quantity:
          type: integer
          format: int64
        startOn:
          type: string
          format: date
        endOn:
          type: string
          format: date
        startAt:
          type: string
          example: "09:00"
        endAt:
          type: string
          example: "18:00"

    ApplicationCreateRequest:
      type: object
      required:
        - officeId
        - records
      properties:
        officeId:
          type: integer
          format: int64
        regionId:
          type: integer
          format: int64
        records:
          type: array
          items:
            $ref: '#/components/schemas/RecordRequest'

    RecordResponse:
      type: object
      properties:
        id:
          type: integer
        applicationId:
          type: integer
        position:
          $ref: '#/components/schemas/Position'
        quantity:
          type: integer
        startOn:
          type: string
          format: date
        endOn:
          type: string
          format: date
        startAt:
          type: string
        endAt:
          type: string

    ApplicationResponse:
      type: object
      properties:
        id:
          type: integer
        office:
          $ref: '#/components/schemas/Office'
        regionId:
          type: integer
        records:
          type: array
          items:
            $ref: '#/components/schemas/RecordResponse'

    ErrorResponse:
      type: object
      properties:
        code:
          type: string
        message:
          type: string

paths:

  /applications:

    post:
      summary: Создание заявки на подмену
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ApplicationCreateRequest'
      responses:
        '200':
          description: Заявка успешно создана
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ApplicationResponse'
        '400':
          description: Ошибка запроса
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

    get:
      summary: Получение списка заявок
      security:
        - bearerAuth: []
      parameters:
        - name: regionId
          in: query
          schema:
            type: integer
        - name: officeId
          in: query
          schema:
            type: integer
        - name: year
          in: query
          required: true
          schema:
            type: integer
        - name: month
          in: query
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Список заявок
          content:
            application/json:
              schema:
                type: object
                additionalProperties:
                  type: array
                  items:
                    $ref: '#/components/schemas/ApplicationResponse'
        '404':
          description: Регион не найден
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /applications/{applicationId}:

    patch:
      summary: Изменить статус заявки
      security:
        - bearerAuth: []
      parameters:
        - name: applicationId
          in: path
          required: true
          schema:
            type: integer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - statusId
              properties:
                statusId:
                  type: integer
      responses:
        '200':
          description: Статус изменён
          content:
            application/json:
              schema:
                type: object
                properties:
                  applicationId:
                    type: integer
                  status:
                    $ref: '#/components/schemas/ApplicationStatus'
        '400':
          description: Неверный статус
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

  /application-statuses:

    get:
      summary: Получить список статусов заявок
      security:
        - bearerAuth: []
      responses:
        '200':
          description: Список статусов
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/ApplicationStatus'
        '401':
          description: Неавторизован
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
```

<snippet id="wKA0K"/>