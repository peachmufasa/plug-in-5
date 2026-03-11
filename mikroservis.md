---
order: 2
title: "Микросервис\_"
---

## 1\. Общая информация о сервисе

<table header="row">
<tr>
<td>

Цель разработки сервиса

</td>
<td>





</td>
</tr>
<tr>
<td>

Зависимости

</td>
<td>





</td>
</tr>
<tr>
<td>

Потребители

</td>
<td>

1.  

</td>
</tr>
<tr>
<td>

Архитектурный стиль

</td>
<td>





</td>
</tr>
</table>

<openapi src="./mikroservis.yaml" flag="true"/>



## 2\. Общие требования к реализации сервиса

## 2\.1. Настройки, используемые для работы сервиса

<table header="row">
<tr>
<td>



</td>
<td>



</td>
<td>



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
<td>



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
<td>



</td>
</tr>
<tr>
<td>





</td>
<td>

OK

</td>
<td>

200

</td>
<td>

Успешный вход или обновление токенов

</td>
<td>

--

</td>
</tr>
<tr>
<td>





</td>
<td>

Created

</td>
<td>

201

</td>
<td>

Успешная регистрация пользователя

</td>
<td>

--

</td>
</tr>
<tr>
<td>





</td>
<td>

No Content

</td>
<td>

204

</td>
<td>

Успешный выход из системы (logout / logout all)

</td>
<td>

--

</td>
</tr>
<tr>
<td>





</td>
<td>

Validation error

</td>
<td>

400

</td>
<td>

Некорректный формат полей, ошибка валидации входных данных

</td>
<td>

ProblemDetail

</td>
</tr>
<tr>
<td>





</td>
<td>

Invalid token

</td>
<td>

401

</td>
<td>

Токен просрочен или имеет неверный формат

</td>
<td>

ProblemDetail

</td>
</tr>
<tr>
<td>





</td>
<td>

Forbidden

</td>
<td>

403

</td>
<td>

Доступ запрещён, учетная запись деактивирована

</td>
<td>

ProblemDetail

</td>
</tr>
<tr>
<td>





</td>
<td>

Invalid credentials

</td>
<td>

404

</td>
<td>

Неверный логин или пароль

</td>
<td>

ProblemDetail

</td>
</tr>
<tr>
<td>





</td>
<td>

Registration duplicate

</td>
<td>

409

</td>
<td>

Пользователь с таким именем уже зарегистрирован

</td>
<td>

ProblemDetail

</td>
</tr>
<tr>
<td>





</td>
<td>

Internal server error

</td>
<td>

500

</td>
<td>

Внутренняя ошибка сервера

</td>
<td>

ProblemDetail

</td>
</tr>
</table>

## 2\.3. Сваггер сервис

Sorry, but it appears that your Open API Documentation for Confluence add-on license has either expired, or is not valid for your version of Confluence. Please have your Confluence administrator visit the Manage add-ons page and renew the license.

<snippet id="wKA0K"/>