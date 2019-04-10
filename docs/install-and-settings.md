# Установка и настройка Inex OPC сервера

 При необходимости реализации системы управления верхнего уровня, где часто используются такие современные программные средства, как SCADA системы, возникает 

## Системные требования

 - Windows XP SP3 и выше, Windows Server 2003 SP2, Windows Server 2008 SP2, 2008 SP2 2008 R2 SP1 и выше
 - .NET Framework 4.0

## Установка Inex OPC сервера

Для установки продукта необходимо получить установочный пакет (_InexOpcServer_X.X.X_x86.exe_ или _InexOpcServer_X.X.X_x64.exe_, где X.X.X версия продукта) и запустить его на выполнение с правами **Администратора**. При необходимости можно указать папку установки программы, перейдя в раздел **'Параметры'**. После принятия условий лицензии нажмите на кнопку **'Установка'**. После этого OPC сервер будет установлен на Ваш компьютер.

## Настройка Inex OPC сервера

Файл конфигурации **Inex OPC сервера** находится по пути: 
_&lt;path_to_install&gt;\storage\cfg\OpcSrv.config_

Ниже приводится содержание этого файла с первичными настройками и с коментариями, объясняющими назначение всех элементов и атрибутов:

``` xml
<?xml version="1.0"?>
<Settings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">

  <!--
  Раздел регистрации библиотек и экземпляров источников данных
  -->
  
  <referencesDataSources>
    <!--Регистрация библиотек источников данных-->
    <libraries>
      <!--
      libraryName:           Уникальное имя библиотеки
      assemblyQualifiedName: Полное имя класса источника данных
      -->
      <library libraryName="stub" assemblyQualifiedName="Stub.DataSource.StubDs, Stub.DataSource, Version=1.0.0.0" />
    </libraries>
    <!--Регистрация экземпляров источников данных-->
    <dataSources>
      <!--
      alias:       Уникальный псевдоним экземпляра источника данных
      libraryName: Имя библиотеки источника данных
      active:      Признак активности экземпляра источника данных. Не обязательный 
                   параметр. Значение по умолчанию: true
      
      Элемент dataSource описывает один именованный экземпляр источника данных. 
      Количество элементов dataSource может быть произвольным.
      -->
      <dataSource alias="STUB" libraryName="stub" active="true">
        <!--
        Список параметров инициализации экземпляра источника данных. Количество 
        параметров может быть произвольным.
        -->
        <initializeArgs>
          <!--
          key:   Уникальное имя параметра
          value: Значение параметра
          -->
          <initializeArg key="file" value="storage\ds-cfg\Stub.DataSource\Stub.DataSource.xml" />
        </initializeArgs>
      </dataSource>
    </dataSources>

  </referencesDataSources>

  <!--
  Раздел общих настроек
  -->
  
  <!--
  Время ожидания завершения операции запуска экземпляра источника 
  данных в миллисекундах
  -->
  <startDataSourceTimeout>5000</startDataSourceTimeout>
  <!--
  Время ожидания завершения операции останова экземпляра источника 
  данных в миллисекундах
  -->
  <stopDataSourceTimeout>5000</stopDataSourceTimeout>
  <!--
  Время ожидания завершения операции чтения/записи экземпляра 
  источника данных в миллисекундах
  -->
  <dataSourceOperationTimeout>5000</dataSourceOperationTimeout>
  <!--
  Символ разделителя частей идентификаторов OPC
  -->
  <opcItemIdSeparator>.</opcItemIdSeparator>
  <!--
  Минимальный допустимый интервал обновления данных в OPC-клиентах 
  в миллисекундах
  -->
  <opcMinUpdateRate>100</opcMinUpdateRate>
</Settings>
```
