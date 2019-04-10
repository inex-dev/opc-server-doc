# Пример создания подключаемого источника данных

В этой статье публикуются исходные файлы на языке C# простого источника данных, генерирующего случайные значения для тегов. Данный код не претендует на изысканность. Его назначение показать простоту процесса создания вполне рабочего подключаемого модуля.

Проект состоит из трех файлов исходного кода:

- **StubDs.cs** - содержит непосредственно сам класс создаваемого источника данных, наследуемый от [_OpcSrv.Shared.IDataSource_](api-references-interfaces.md#idatasource);
- **Config.cs** - содержит класс конфигурации источника данных;
- **Generator.cs** - содержит класс генератора случайных значений тегов.