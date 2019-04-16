# Пример конфигурации и подключения источника данных МЭК104 протокола

ОРС-сервер располагает наличием подключаемого модуля, работающего с устройствами по протоколу МЭК104. Данная статья служит инструкцией по настройке и подключению упоминаемого модуля. Настройка и подключение состоит из следующих этапов:

## Создание XML-файла конфигурации
В текстовом редакторе создается XML-файл с параметрами для устройства, в котором описаны параметры цифровых и аналоговых входов и переменные соединения.

Файл имеет два блока с контролируемыми параметрами для цифровых и аналоговых входов (din и ain соответственно).

Каждый отдельный тег обозначает параметр и использует следующие аттрибуты:

1. Аттрибут **"Id"** указывает индекс параметра, должен соответствовать IOA (информационному адресу объекта) на устройстве.
2. Аттрибут **"Name"** задает имя параметру, которое будет отображаться в клиенте орс-сервера.
3. Аттрибут **"Type"** задает тип значений параметра (для цифровых входов используется Type="Bool", для аналоговых - Type="Float").
4. Аттрибут **"Readable"** при установлении его в значение "true", позволяет считывать этот параметр с помощью метода "ReadFromDevice", реализованным в ОРС-сервере. При пропуске аттрибута, ему присваивается значение по умолчанию - "true".
5. Аттрибут **"Writable"** при установлении его в значение "true", позволяет записывать новое значение в этот параметр с помощью метода "WriteToDevice", реализованным в ОРС-сервере. При пропуске аттрибута, ему присваивается значение по умолчанию - "false".

Помимо блоков контролируемых значений, в файле указываются значения настройки установки и управления соединением:

1. Параметр **"SyncTimePeriod"** задает значение периода синхронизации времени в устройстве в секундах. При нулевом значении параметра, синхронизация времени с устройством пройдет единократно при установлении соединения с устройством. В примере указано значение в 30 минут (30 ед * 60 секунд).
2. Параметр **"InterrogationInterval"** задает значение периода общего опроса устройства в секундах. При нулевом значении параметра, общий опрос устройства пройдет единократно при установлении соединения с устройством. В примере задано значение в 30 секунд.
3. Параметр **"t0"**, стандартный параметр протокола мэк104. Задает значение на временной интервал между подключениями к устройству (Если нет связи с устройством, каждые "t0" секунд будет осуществляться попытка соединения с устройством).По умолчанию задано стандартное значение из документации к протоколу мэк104 в величине 10 секунд.
4. Параметр **"t1"**, стандартный параметр протокола мэк104. Используется для контроля качества соединения. Вызывает перезапуск соединения если за время t1 после отправки I-пакета устройству не пришли в ответ I-пакет либо S-пакет. Стандартное значение, заданное в документации на протокол - 15 секунд.
5. Параметр **"t2"**, стандартный параметр протокола мэк104. Используется для подтверждения принятия пакетов и синхронизации сообщений. По истечению времени t2 с момента принятия I-пакета, отправляет S-пакет со значениями о количестве принятых и отправленных пакетов. Стандартное значение, заданное в документации на протокол - 10 секунд.
6. Параметр **"t3"**, стандартный параметр протокола мэк104. Используется для тестирования канала соединения при простое. По истечению времени, отсчет которого начинается с принятием I-пакета либо S-пакета, отправляет контрольный тест-пакет. Если два запроса тест-пакета остаются без ответа, устройство перезапускается. Стандартное значение, заданное в документации на протокол - 20 секунд.
7. Параметр **"k"**, стандартный параметр протокола мэк104. Задает максимальное количество пакетов с информацией о параметрах, которое может отправить передатчик (контроллер) без получения подтверждения.
8. Параметр **"w"**, стандартный параметр протокола мэк104. Задает максимальное количество пакетов с информацией о параметрах, которое может получить приемник без отправки подтверждения.
9. Параметр **"ip_addr"** представляет собой IP-адресс устройства в формате строки "xxx.xxx.xxx.xxx".

  Ниже приводится содержание XML-файла с настройками и с комментариями, объясняющими назначение элементов и атрибутов:

``` xml
<?xml version="1.0"?>
<Config xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Контрольные параметры указываются в двух соответствующих блоках: 
  цифровые (din) и аналоговые (ain) входы -->
  <Folders>
    <!-- Блок для цифровых входов. Аттрибуты блока принимают значение "din" -->
    <Folder Id="din" Name="din">
      <Tags>
        <Tag Id="1"  Name="din1"  Type="Bool" Readable="true" Writeable="true"  />
        <Tag Id="2"  Name="din2"  Type="Bool" Readable="true"/>
        <Tag Id="3"  Name="din3"  Type="Bool" Readable="true"/>
        <Tag Id="4"  Name="din4"  Type="Bool" Readable="true"/>
        <Tag Id="5"  Name="din5"  Type="Bool" Readable="true"/>
        <Tag Id="6"  Name="din6"  Type="Bool" Readable="true"/>
        <Tag Id="7"  Name="din7"  Type="Bool" Readable="true"/>
        <Tag Id="8"  Name="din8"  Type="Bool" Readable="true"/>
        <Tag Id="9"  Name="din9"  Type="Bool" Readable="true"/>
        <Tag Id="10" Name="din10" Type="Bool" Readable="true"/>
        <Tag Id="11" Name="din11" Type="Bool" Readable="true"/>
        <Tag Id="12" Name="din12" Type="Bool" Readable="true"/>
        <Tag Id="13" Name="din13" Type="Bool" Readable="true"/>
      </Tags>
    </Folder>
    <!-- Блок аналоговых входов. Аттрибуты блока принимают значение "ain" -->
    <Folder Id="ain" Name="ain">
      <Tags>
        <Tag Id="14" Name="ain1" Type="Float" Readable="true" />
        <Tag Id="15" Name="ain2" Type="Float" Readable="true"/>
        <Tag Id="16" Name="ain3" Type="Float" Readable="true"/>
        <Tag Id="17" Name="ain4" Type="Float" Readable="true"/>
      </Tags>
    </Folder>
  </Folders>
  <!-- Период синхронизации времени на устройстве -->  
  <SyncTimePeriod>1800</SyncTimePeriod>
  <!-- Интервал периодического опроса устройства -->
  <InterrogationInterval>30</InterrogationInterval>
  <!-- Тайм-аут при установлении соединения, в секундах -->
  <t0>10</t0>
  <!-- Тайм-аут при посылке или тестировании APDU, в секундах -->
  <t1>15</t1>
  <!-- Тайм-аут для подтверждения в случае отсутствия сообщения с данными t2<t1 -->
  <t2>10</t2>
  <!-- Тайм-аут для посылки блоков тестирования в случае долгого простоя -->  
  <t3>20</t3>  
  <!-- Максимальная разность между переменной состояния передачи и 
  номером последнего подтвержденного APDU -->
  <k>12</k>
  <!-- Последнее подтверждение после приема w APDU формата I -->
  <w>8</w>
  <!-- IP-адресс устройства (порт для мэк104 по умолчанию 2404) -->
  <ip_addr>127.0.0.1</ip_addr>
</Config>
```

## Добавление нового источника данных

Сохранить созданный файл конфигурации следует по пути, задав ему уникальное имя с расширением ".xml" (В примере файлу присвоено имя "Iec104DataSource.xml"): 

_&lt;path_to_install&gt;\storage\ds-cfg\Iec104\&lt;file_name&gt;.xml_


Следующий этап добавления происходит путем редактирования xml-файла конфигурации OPC-сервера, который находится по пути: _&lt;path_to_install&gt;\storage\cfg\OpcSrv.config_.
Для редактирования подходит любой текстовый редактор. Изменения состоят в следующем:

**1.** В блоке регистрации библиотек источников данных указывается наша библиотека протокола мэк104 со своими уникальным именем библиотеки и полным именем класса источника данных

``` xml
<!--
libraryName: Уникальное имя библиотеки
assemblyQualifiedName: Полное имя класса источника данных
-->
<library libraryName="Iec104" assemblyQualifiedName="Iec104.Iec104DataSource, Iec104, Version=1.0.0.0" />
```

**2.** В блоке регистрации экземпляров источников данных указываются параметры нового экземпляра источника данных: alias, libraryName, active и значения параметров key и value, где:

- "alias" - уникальный псевдоним экземпляра источника данных.
- "libraryName" - имя библиотеки источника данных (указывалось при регистрации новой библиотеки источника, см. пункт 1 этого параграфа).
- "active" - признак активности экземпляра источника данных. Не обязательный параметр. Значение по умолчанию: true
- "key" - ключ-параметр, указывающий на источник конфигурации экземпляра. Имеет значение "file".
- "value" - содержит путь до файла с конфигурацией экземпляра. В качестве примера в это значение вписан путь конфигурации из параграфа 1 данной статьи.

В нашем примере регистрация нового экземпляра источника данных будет выглядеть следующим образом:

``` xml
<dataSource alias="IEC104" libraryName="Iec104" active="true">
    <initializeArgs>
        <initializeArg key="file" value="storage\ds-cfg\Iec104\Iec104DataSource.xml" />
    </initializeArgs>
</dataSource>
```

!!!warning "Внимание"
    Для каждого нового устройства псевдоним и файл конфигурации должны быть уникальны.

После внесения изменений, файл конфигурации будет иметь вид:

``` xml
<?xml version="1.0"?>
<Settings xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">

<referencesDataSources>
    <!--Регистрация библиотек источников данных-->
    <libraries>
        <!--
        libraryName: Уникальное имя библиотеки
        assemblyQualifiedName: Полное имя класса источника данных
        -->
        <library libraryName="stub" assemblyQualifiedName="Stub.DataSource.StubDs, Stub.DataSource, Version=1.0.0.0" />
        <!-- Подключение библиотеки протокола мэк104 -->
        <library libraryName="Iec104" assemblyQualifiedName="Iec104.Iec104DataSource, Iec104, Version=1.0.0.0" />
    </libraries>
    <!--Регистрация экземпляров источников данных-->
    <dataSources>
        <!--
        alias: Уникальный псевдоним экземпляра источника данных
        libraryName: Имя библиотеки источника данных
        active: Признак активности экземпляра источника данных. Не обязательный параметр. Значение по умолчанию: true
        -->
        <dataSource alias="STUB" libraryName="stub" active="true">
            <!--Параметры инициализации экземпляра источника данных-->
            <initializeArgs>
                <!--
                key: Уникальное имя параметра
                value: Значение параметра
                -->
                <initializeArg key="file" value="storage\ds-cfg\Stub.DataSource\Stub.DataSource.xml" />
            </initializeArgs>
        </dataSource>

        <!-- новый экземпляр источника данных -->
        <dataSource alias="IEC104" libraryName="Iec104" active="true">
            <initializeArgs>
                <initializeArg key="file" value="storage\ds-cfg\Iec104\Iec104DataSource.xml" />
            </initializeArgs>
        </dataSource>  
    </dataSources>

  </referencesDataSources>

  <!--Время ожидания завершения операции запуска экземпляра источника данных в миллисекундах-->
  <startDataSourceTimeout>5000</startDataSourceTimeout>
  <!--Время ожидания завершения операции останова экземпляра источника данных в миллисекундах-->
  <stopDataSourceTimeout>5000</stopDataSourceTimeout>
  <!--Время ожидания завершения операции чтения/записи экземпляра источника данных в миллисекундах-->
  <dataSourceOperationTimeout>5000</dataSourceOperationTimeout>
  <!--Символ разделителя частей идентификаторов OPC-->
  <opcItemIdSeparator>.</opcItemIdSeparator>

</Settings>
```