# Пример создания подключаемого источника данных

В этой статье публикуются исходные файлы на языке C# простого источника данных, генерирующего случайные значения для тегов. Данный код не претендует на изысканность. Его назначение показать простоту процесса создания вполне рабочего подключаемого модуля.

Проект состоит из трех файлов исходного кода:

- **StubDs.cs** - содержит непосредственно сам класс создаваемого источника данных, наследуемый от [_OpcSrv.Shared.IDataSource_](api-references-interfaces.md#idatasource);
- **Config.cs** - содержит класс конфигурации источника данных;
- **Generator.cs** - содержит класс генератора случайных значений тегов.

Также публикуется файл **Stub.DataSource.xml**, содержащий настроечную информацию для создаваемого источника данных. 

Для изучения процесса создания подключаемого модуля источника данных Вам следует выполнить:

1. Создать проект библиотеки .NET Framework 4.0;
2. В проект добавитьссылку на сборку **OpcSrv.Shared.dll**;
3. Добавить к проекту указанные выше три файла исходного кода;
4. Скомпилировать проект для платформы x86 или x64 в зависимости от используемой Вами версии **Inex OPC сервера**;
5. Поместить получившуюся .DLL в папку установки **Inex OPC сервера**;
6. Прописать новый источник данных в файле OpcSrv.config аналогично **Stub.DataSource.dll** как указано в разделе [Настройка Inex OPC сервера](install-and-settings.md#inex-opc_2).


## StubDs.cs
``` csharp
using OpcSrv.Shared;
using OpcSrv.Shared.Browse;
using OpcSrv.Shared.Cache;
using System;
using System.Collections.Generic;

namespace Stub.DataSource
{
  class StubDs : OpcSrv.Shared.IDataSource
  {
    // Конфигурация
    Config _config;
    // Таймер обновления данных
    System.Threading.Timer _timer = null;
    // Объект ведения журнала
    OpcSrv.Shared.Loging.ILogger _logger;
    // Псевдоним 
    string _alias;
    // Словарь тегов
    Dictionary<string, Leaf> _tags = new Dictionary<string, Leaf>();

    public StubDs()
    {
    }

    public DataSourceAddressSpace GetAddressSpace()
    {
      _logger.Info("Чтение адресного пространства");
      DataSourceAddressSpace retVal = new DataSourceAddressSpace();
      for (int i = 0; i < _config.Folders.Count; i++)
      {
        Folder f = _config.Folders[i];
        Branch branch = new Branch();
        branch.Id = f.Id;
        branch.Name = f.Name;
        retVal.Branches.Add(branch);

        for (int j = 0; j < f.Tags.Count; j++)
        {
          Tag t = f.Tags[j];
          Leaf leaf = new Leaf();
          leaf.Id = t.Id;
          leaf.Name = t.Name;
          leaf.Type = t.Type;
          leaf.Readable = t.Readable;
          leaf.Writeable = t.Writeable;
          leaf.LoLimit = t.LoLimit;
          leaf.LoLoLimit = t.LoLoLimit;
          leaf.HiLimit = t.HiLimit;
          leaf.HiHiLimit = t.HiHiLimit;
          branch.Leaves.Add(leaf);
          _tags.Add(leaf.Id, leaf);
        }
      }
      return retVal;
    }

    public void Initialize(string alias, Dictionary<string, string> args)
    {
      _alias = alias;
      _logger = OpcSrv.Shared.Loging.LogManager.CreateLogger(_alias);
      _logger.Info("Initialize");
      const string key = "file";
      string file = args[key];
      string fullPath = System.IO.Path.Combine(OpcSrv.Shared.IO.Folder.CurrentPath, file);
      _logger.Info($"Configuration file: {fullPath}");
      OpcSrv.Shared.Loging.LogManager.Flush();
      _config = Config.Load(fullPath);
    }

    public void Start()
    {
      _logger.Info("Start");
      _timer = new System.Threading.Timer(OnTick, null, 0, _config.UpdateInterval);
    }


    public void Stop()
    {
      _logger.Info("Stop");
      if (_timer != null)
        _timer.Dispose();
      _timer = null;
    }

    public ItemValue[] ReadFromDevice(string[] arrId, CancelToken cancelToken)
    {
      _logger.Info("ReadFromDevice");
      ItemValue[] retVal = new ItemValue[arrId.Length];
      DateTime now = DateTime.UtcNow;
      for(int i = 0; i < arrId.Length; i++)
      {
        System.Threading.Thread.Sleep(100);//имитируем чтение с устройства
        if (cancelToken.IsCancelRequested)
          return null;
        Leaf leaf;
        if(!_tags.TryGetValue(arrId[i], out leaf))
        {
          retVal[i] = GetValue(now, leaf.Type);
        }
      }
      return retVal;
    }

    public OperationError[] WriteToDevice(string[] arrId, object[] values, CancelToken cancelToken)
    {
      _logger.Info("WriteToDevice");
      OperationError[] retVal = new OperationError[arrId.Length];
      for(int i = 0; i < arrId.Length; i++)
      {
        if (cancelToken.IsCancelRequested)
          return null;
        System.Threading.Thread.Sleep(100);//имитируем запись в устройство
        retVal[i] = OperationError.Ok;
      }
      return retVal;
    }

    void OnTick(object state)
    {
      DateTime now = DateTime.UtcNow;
      for (int i = 0; i < _config.Folders.Count; i++)
      {
        Folder f = _config.Folders[i];
        for (int j = 0; j < f.Tags.Count; j++)
        {
          Tag t = f.Tags[j];
          string id = t.Id;
          DataType type = t.Type;
          CacheStorage.Instance.SetData(_alias, id, GetValue(now, type));
        }
      }
    }

    Generator _gen = new Generator();
    ItemValue GetValue(DateTime time, DataType type)
    {
      switch (type)
      {
        case DataType.Bool:
          return new ItemValue(_gen.GetBool(), Quality.GoodNonspecific, time);
        case DataType.DateTime:
          return new ItemValue(_gen.GetDateTime(), Quality.GoodNonspecific, time);
        case DataType.Double:
          return new ItemValue(_gen.GetDouble(), Quality.GoodNonspecific, time);
        case DataType.Float:
          return new ItemValue(_gen.GetFloat(), Quality.GoodNonspecific, time);
        case DataType.Int16:
          return new ItemValue(_gen.GetInt16(), Quality.GoodNonspecific, time);
        case DataType.Int32:
          return new ItemValue(_gen.GetInt32(), Quality.GoodNonspecific, time);
        case DataType.Int64:
          return new ItemValue(_gen.GetInt64(), Quality.GoodNonspecific, time);
        case DataType.Int8:
          return new ItemValue(_gen.GetInt8(), Quality.GoodNonspecific, time);
        case DataType.String:
          return new ItemValue(_gen.GetString(), Quality.GoodNonspecific, time);
        case DataType.UInt16:
          return new ItemValue(_gen.GetUInt16(), Quality.GoodNonspecific, time);
        case DataType.UInt32:
          return new ItemValue(_gen.GetUInt32(), Quality.GoodNonspecific, time);
        case DataType.UInt64:
          return new ItemValue(_gen.GetUInt64(), Quality.GoodNonspecific, time);
        case DataType.UInt8:
          return new ItemValue(_gen.GetUInt8(), Quality.GoodNonspecific, time);
        default:
          throw new ArgumentException();
      }
    }
  }
}
```

## Config.cs
``` csharp
using OpcSrv.Shared;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Xml.Serialization;

namespace Stub.DataSource
{
  [Serializable]
  public class Config
  {
    public int UpdateInterval { get; set; }
    public int ReadTimeout { get; set; }
    public int WriteTimeout { get; set; }
    public List<Folder> Folders = new List<Folder>();

    public static void Save(Config config, string path)
    {
      string dir = Path.GetDirectoryName(path);
      if (!Directory.Exists(dir))
        Directory.CreateDirectory(dir);
      XmlSerializer ser = new XmlSerializer(typeof(Config));
      using (FileStream fs = new FileStream(path, FileMode.Create, FileAccess.Write))
      {
        ser.Serialize(fs, config);
      }
    }

    public static Config Load(string path)
    {
      XmlSerializer ser = new XmlSerializer(typeof(Config));
      using (FileStream fs = new FileStream(path, FileMode.Open, FileAccess.Read))
      {
        return (Config)ser.Deserialize(fs);
      }
    }
  }

  [Serializable]
  public class Folder
  {
    [XmlAttribute]
    public string Id { get; set; }
    [XmlAttribute]
    public string Name { get; set; }
    public List<Tag> Tags = new List<Tag>();
  }

  [Serializable]
  public class Tag
  {
    [XmlAttribute]
    public string Id { get; set; }

    [XmlAttribute]
    public string Name { get; set; }

    [XmlAttribute]
    public DataType Type { get; set; }

    [XmlAttribute]
    public bool Readable { get; set; } = true;

    [XmlAttribute]
    public bool Writeable { get; set; } = false;

    [XmlIgnore]
    public double? LoLimit
    {
      get { return string.IsNullOrEmpty(LoLimitValue) ? null : new double?(double.Parse(LoLimitValue, CultureInfo.InvariantCulture)); }
    }
    [XmlAttribute(AttributeName = "LoLimit")]
    public string LoLimitValue { get; set; }


    [XmlIgnore]
    public double? LoLoLimit
    {
      get { return string.IsNullOrEmpty(LoLoLimitValue) ? null : new double?(double.Parse(LoLoLimitValue, CultureInfo.InvariantCulture)); }
    }
    [XmlAttribute(AttributeName = "LoLoLimit")]
    public string LoLoLimitValue { get; set; }


    [XmlIgnore]
    public double? HiLimit
    {
      get { return string.IsNullOrEmpty(HiLimitValue) ? null : new double?(double.Parse(HiLimitValue, CultureInfo.InvariantCulture)); }
    }
    [XmlAttribute(AttributeName = "HiLimit")]
    public string HiLimitValue { get; set; }

    [XmlIgnore]
    public double? HiHiLimit
    {
      get { return string.IsNullOrEmpty(HiHiLimitValue) ? null : new double?(double.Parse(HiHiLimitValue, CultureInfo.InvariantCulture)); }
    }
    [XmlAttribute(AttributeName = "HiHiLimit")]
    public string HiHiLimitValue { get; set; }
  }
}
```

## Generator.cs
``` csharp
using System;

namespace Stub.DataSource
{
  class Generator
  {
    Random _rnd = new Random();

    public sbyte GetInt8()
    {
      return (sbyte)_rnd.Next(sbyte.MinValue, sbyte.MaxValue);
    }

    public short GetInt16()
    {
      return (short)_rnd.Next(short.MinValue, short.MaxValue);
    }

    public int GetInt32()
    {
      return _rnd.Next(-100000, 100000);
    }

    public long GetInt64()
    {
      return Convert.ToInt64(_rnd.Next(-100000000, 100000000));
    }

    public byte GetUInt8()
    {
      return (byte)_rnd.Next(byte.MinValue, byte.MaxValue);
    }

    public ushort GetUInt16()
    {
      return (ushort)_rnd.Next(ushort.MinValue, ushort.MaxValue);
    }

    public uint GetUInt32()
    {
      return (uint)_rnd.Next(0, 100000);
    }

    public ulong GetUInt64()
    {
      return Convert.ToUInt64(_rnd.Next(0, 100000000));
    }

    public string GetString()
    {
      return Guid.NewGuid().ToString();
    }

    public float GetFloat()
    {
      float retVal = GetInt16() + (float)_rnd.NextDouble();
      return retVal;
    }

    public double GetDouble()
    {
      double retVal = GetInt32() + _rnd.NextDouble();
      return retVal;
    }

    public bool GetBool()
    {
      int dat = _rnd.Next(1, 3);
      if (dat % 2 == 0) return false;
      return true;
    }

    public DateTime GetDateTime()
    {
      return DateTime.Now;
    }

    public decimal GetDecimal()
    {
      byte scale = (byte)_rnd.Next(29);
      bool sign = _rnd.Next(2) == 1;
      return new decimal((int)GetUInt32(),
                         (int)GetUInt32(),
                         (int)GetUInt32(),
                         sign,
                         scale);
    }
  }
}
```

## Stub.DataSource.xml
``` xml
<?xml version="1.0"?>
<Config xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Folders>
    <Folder Id="random" Name="random">
      <Tags>
        <Tag Id="1" Name="int8" Type="Int8" Writeable="false" Readable="true" />
        <Tag Id="2" Name="int16" Type="Int16" />
        <Tag Id="3" Name="int32" Type="Int32" />
        <Tag Id="4" Name="int64" Type="Int64" />
        <Tag Id="5" Name="uint8" Type="UInt8" />
        <Tag Id="6" Name="uint16" Type="UInt16" />
        <Tag Id="7" Name="uint32" Type="UInt32" />
        <Tag Id="8" Name="uint64" Type="UInt64" />
        <Tag Id="9" Name="float" Type="Float" />
        <Tag Id="10" Name="double" Type="Double" />
        <Tag Id="11" Name="bool" Type="Bool" />
        <Tag Id="12" Name="datetime" Type="DateTime" />
        <Tag Id="13" Name="string" Type="String" />
      </Tags>
    </Folder>
  </Folders>
  <UpdateInterval>1000</UpdateInterval>
  <ReadTimeout>100</ReadTimeout>
  <WriteTimeout>100</WriteTimeout>
</Config>
```