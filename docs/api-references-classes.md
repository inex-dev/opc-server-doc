# Классы

## Класс Branch

Пространство имен: OpcSrv.Shared.Browse

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class Branch: TreeItem**

Класс описания папки тегов в иерархическом адресном пространстве

### Свойства

Имя | Тип | Доступ | Значение по умолчанию | Описание 
----|-----|--------|-----------------------|---------
Id | string | RW | null | Идентификатор папки 
Name | string | RW | null | Имя папки. Это значение будет опображаться в дереве адресного пространства OPC клиента и являться частью OPC идентификатора тега, поэтому не может содержать символ, указанный в элементе **opcItemIdSeparator** в файле OpcSrv.config
Description | string | RW | null | Описание папки
Kind | TreeItemKind | R | TreeItemKind.Branch | Тип элемента
Branches | TreeItemCollection &lt;Branch&gt; | R | коллекция | Коллекция дочерних папок
Leaves | TreeItemCollection &lt;Leaf&gt; | R | коллекция | Коллекция дочерних тегов

!!!warning "Внимание"
    Адресное пространство OPC сервера аналогично файловой структуре, поэтому свойство **Name** всех экземпляров классов Leaf и Branch в рамках одного родителя должно быть уникально.

---

## Класс CacheStorage

Пространство имен: OpcSrv.Shared.Cache

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class CacheStorage**

Класс работы с кэш OPC сервера. При помощи именно этого класса источник данных сообщает об изменении значений своих тегов OPC серверу.


### Методы

!!!summary "C#"
    **public void SetData(string alias, string id, ItemValue value)**

Параметры:

Имя | Тип | Описание
----|-----|---------
alias|string|Псевдоним источника данных, полученный при вызове метода [OpcSrv.Shared.IDataSource.Initialize()](api-references-interfaces.md#idatasource)
id|IDataSource|Идентификатор изменившегося тега
value|[ItemValue](api-references-structs.md#itemvalue)|Новое значение тега

Метод передает в кэш сервера новое значение тега

---

## Класс CancelToken

Пространство имен: OpcSrv.Shared

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class CancelToken**

Класс объекта отмены операции

### Свойства

Имя | Тип | Доступ | Значение по умолчанию | Описание 
----|-----|--------|-----------------------|---------
IsCancelRequested | bool | R | false | Значение свойства равное **true** сигнализирует об отмене текущей операции

---

## Класс DataSourceAddressSpace

Пространство имен: OpcSrv.Shared.Browse

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class DataSourceAddressSpace**

Класс описания адресного пространства источника данных

### Свойства

Имя | Тип | Доступ | Значение по умолчанию | Описание 
----|-----|--------|-----------------------|---------
Branches | TreeItemCollection&lt;Branch&gt; | R | коллекция | Коллекция дочерних папок
Leaves | TreeItemCollection&lt;Leaf&gt; | R | коллекция | Коллекция дочерних тегов

---

## Класс Leaf

Пространство имен: OpcSrv.Shared.Browse

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class Leaf: TreeItem**

Класс описания тега в иерархическом адресном пространстве. 

### Свойства

Имя | Тип | Доступ | Значение по умолчанию | Описание 
----|-----|--------|-----------------------|---------
Id | string | RW | null | Идентификатор тега 
Name | string | RW | null | Имя тега. Это значение будет отображаться в дереве адресного пространства OPC клиента и являться частью OPC идентификатора тега, поэтому не может содержать символ, указанный в элементе **opcItemIdSeparator** в файле OpcSrv.config. 
Description | string | RW | null | Описание тега
Kind | TreeItemKind | R | TreeItemKind.Leaf | Тип элемента
Type | DataType | RW | N/A | Тип данных тега
Readable | bool | RW | true | Возможность чтения значения тега
Writeable | bool | RW | false | Возможность записи значения тега
HiLimit | double? | RW | null | Технический максимум значения тега
HiHiLimit | double? | RW | null | Аварийный максимум значения тега
LoLimit | double? | RW | null | Технический минимум значения тега
LoLoLimit | double? | RW | null | Аварийный минимум значения тега

!!!warning "Внимание"
    Адресное пространство OPC сервера аналогично файловой структуре, поэтому свойство **Name** всех экземпляров классов Leaf и Branch в рамках одного родителя должно быть уникально.

---

## Класс LogManager

Пространство имен: OpcSrv.Shared.Loging

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public class LogManager**

Класс создания объектов ведения журналов работы

### Методы

!!!summary "C#"
    **public static ILogger CreateLogger(string name)**

Параметры:

Имя | Тип | Описание
----|-----|---------
name|string|Наименование канала ведения журналов работы

Метод создает именованный объект [_OpcSrv.Shared.Loging.ILogger_](api-references-interfaces.md#ilogger).

---

## Класс Quality

Пространство имен: OpcSrv.Shared

Сборка: OpcSrv.Shared.dll

!!!summary "C#"
    **public static class Quality**

Класс констант определения качества данных значений тегов. Спецификация OPC Data Access имеет достаточно гибкую методику определения качества данных. Класс **Quality** содержит набор констант наиболее употребительных значений качеств. Однако, если Вам не достаточно предложенных значений, то Вы можете дополнительно использовать свои. Главное что-бы эти значения были согласованы с методикой определения качества данных OPC Data Access 2.05a.

### Свойства

Имя | Тип | Доступ | Значение по умолчанию | Описание 
----|-----|--------|-----------------------|---------
GoodNonspecific|short|R| 192 |Качество хорошее. Особых условий нет
GoodLocalOverride|short|R| 216 |Качество хорошее. Локальное переопределение. Подстатус для качества GOOD: значение было переопределено. Обычно это означает, что вход обновление значения было отключено, а введенное вручную значение было «принудительным».
BadNonspecific|short|R| 0 |Значение плохое, но конкретная причина неизвестна
BadLastKnown|short|R| 20 |Значение плохое. Последнее известное значение. Подстатус для качества BAD: связь не удалась. Тем не менее, последнее известное значение доступно
BadCommFailure|short|R| 24 |Значение плохое. Подстатус для качества BAD: связь не удалась. Последнее известное значение не доступно
BadConfigError|short|R| 4 |Значение плохое. Ошибка конфигурации. Подстатус для качества BAD: существует некоторая специфическая проблема сервера с конфигурацией. Например, рассматриваемый элемент был удален из конфигурации
BadDeviceFailure|short|R| 12 |Значение плохое. Отказ устройства. Подстатус для качества BAD: обнаружен сбой устройства
BadNotConnected|short|R| 8 |Значение плохое. Нет соединения. Подстатус для качества BAD. Это качество может отражать то, что в данный момент значение недоступно, по таким причинам, как значение, возможно, не было предоставлено источником данных.
BadOutOfService|short|R| 28 |Значение плохое. Не работает. Подстатус для качества BAD. блок снят со сканирования или иным образом заблокирован. Это качество также используется, когда элемент не является активным.
BadSensorFailure|short|R| 16 |Значение плохое. Обнаружен сбой датчика. Подстатус для качества BAD.
UncertainNonspecific|short|R| 64 |Значение неопределенное. Нет конкретной причины, по которой значение является неопределенным
UncertainEguExceeded|short|R| 84 |Значение неопределенное. Превышен технический лимит. Подстатус для качества UNCERTAIN. Возвращаемое значение выходит за пределы, определенные для этого параметра
UncertainLastUsable|short|R| 68 |Значение неопределенное. Последнее используемое значение. Подстатус для качества UNCERTAIN: Источник данных для этого значения перестал его обновлять. Возвращаемое значение должно рассматриваться как устаревшее. 
UncertainSubnormal|short|R| 88 |Значение неопределенное. Подстатус для качества UNCERTAIN. Значение получено из нескольких источников и имеет меньше необходимого количества хороших источников

---