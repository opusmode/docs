---
sourcePath: overlay/docapi/api-ref/actions/listTables.md
---
# Метод ListTables

Возвращает список таблиц.
Записи возвращаются постранично, за один раз можно вернуть до 100 записей.

## Запрос

Запрос содержит данные в формате JSON.

```json
{
   "ExclusiveStartTableName": "string",
   "Limit": number
}
```

### Параметры

Параметр | Описание
----- | -----
`ExclusiveStartTableName` | Имя таблицы, с которой начнется поиск.<br/><br/>**Тип**: Строка<br/>**Длина**: 3 - 255 символов<br/>**Шаблон**: [a-zA-Z0-9_.-]+<br/>**Обязательно**: Нет
`Limit` | Максимальное количество возвращаемых таблиц. Если параметр не указан, вернется до 100 таблиц.<br/><br/>**Тип**: Целое число<br/>**Диапазон**: 1 - 100.<br/>**Обязательно**: Нет

## Ответ
В случае успеха вернется HTTP с кодом 200.

Запрос возвращает данные в формате JSON.

```json
{
   "LastEvaluatedTableName": "string",
   "TableNames": [ "string" ]
}
```
### Параметры

Параметр | Описание
----- | -----
`LastEvaluatedTableName` | Имя последней таблицы в текущей странице результатов. Используйте это значение как `ExclusiveStartTableName` в новом запросе для получения следующей страницы результатов.<br/>Если значение пустое, значит больше нет таблиц для вывода.<br/><br/>**Тип**: Строка<br/>**Длина**: 3 - 255 символов<br/>**Шаблон**: [a-zA-Z0-9_.-]+
`TableNames` | Список таблиц, максимальный размер массива - 100 записей.<br/><br/>**Тип**: массив строк<br/>**Длина**: 3 - 255 символов<br/>**Шаблон**: [a-zA-Z0-9_.-]+

## Ошибки

Параметр | Описание
----- | -----
`InternalServerError` | Произошла внутренняя ошибка на стороне сервера.<br/><br/>**Код состояния HTTP**: 500<br/>

Также могут возникать [Общие ошибки](../common-errors), одинаковые для всех методов.