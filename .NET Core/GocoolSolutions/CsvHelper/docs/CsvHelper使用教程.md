# 一

共3个CSV文件

CsvReader

CsvFile

CsvWriter

# 二

先熟悉三个概念

class CsvRecord:

​	List<string> Fields

​	int FieldCount

CsvReader类代表Csv文件的一行，也就是一条记录。Fields是个字符串数组，每个元素是记录的字段，数组的顺序也是Csv文件字段的顺序。

 

class CsvRecords(List<Record>):

​	pass

CsvRecords是记录的集合，类型是List<List<string>>.

这里有个技巧，组合可以用继承代替，是个骚操作。相当于为一个类起别名。如CsvRecord与List<List<CsvRecord>完全等价。

# 三 读取一个Csv文件

## CsvReader

```c#
using (CsvReader reader = new CsvReader(@"C:\test.csv",Encoding.UTF8))

{
// 如果Csv文件含有标题，则第一行读到的是标题
  while (reader.ReadNextRecord())

  {

​    Console.WriteLine(reader.Fields[0]);//记录的第一个字段

​    Console.WriteLine(reader.Fields[1]);//记录的第二个字段

  }

}
```

## 注意点

必须使用using，它要释放文件资源。

CsvReader类是专门用来读一个Csv文件或Csv文件的流或Csv格式的字符串。

不要管HasHeader属性，不访问也不设置。

TrimColumns属性设置成true，则解析后的某个字段会Trim();

## CsvFile

```c#
CsvFile file = new CsvFile();
file.Populate(@"C:\test.csv",true,false);
//遍历所有字段
foreach (CsvRecord record in file.Records)
{
    foreach (string field in record.Fields)
    {
        Console.WriteLine(field);
    }
}
//遍历所有标题
foreach (string header in file.Headers)
{
    Console.WriteLine(header);
}
//访问指定行数的记录
CsvRecord record2 = file[1];
//访问指定行数和列数的记录
string field2 = file[1, 2];
//访问指定行数和列名的记录
field2 = file[1, "Name"];

```

## 注意点

new CsvFile对象时不需要using。

```c#
public void Populate(string filePath, bool hasHeaderRow, bool trimColumns)
```

加载指定路径的Csv文件，第二个参数必须指定，它指示当前加载的文档是否有标题，true则把第一行加载到Headers属性，false，则Headers为空，HeaderCount = 0，Records记录中含有第一行。

CsvFile的Populate加载文档，流，字符串时，都会先转换成CsvReader，再把CsvReader转换成CsvFile模型，转换成CsvReader时，内部已经使用using，所以使用CsvFile时，不需要使用using，磁盘文档在方法执行完毕后时立刻自动被释放的。

# 四 生成一个新的Csv文件或为Csv文件增加记录标题

先创建一个CsvFile内存模型，再调用Write方法。记得用using。

```c#
using(CsvWriter writer = new CsvWriter())

{
	CsvWriter.Write(CsvFile csvFile, Encoding encoding)
}
```

```c#
CsvFile file = new CsvFile();
//添加标题
file.Headers.Add("Age");
//添加记录
CsvRecord record = new CsvRecord();
record.Fields.Add("hello");
//添加空字符串
record.Fields.Add("");
//添加两个逗号
record.Fields.Add(",,");
using(CsvWriter writer = new CsvWriter())
{
    writer.WriteCsv(file,"C:\\test.csv");
}
//追加记录的话可以先从文件中把字段加载到Records属性，再添加
file.Populate(@"C:\test.csv",true,false);

```

## CsvReader的实现思路

重要属性：List<string> Fields		重要方法：bool ReadNextRecord()

首先，CsvReader会有一个StreamReader获得Csv文件，Csv字符串。Fields初始值是null。

ReadNextRecord( )每运行一次，就会调用StreamReader读取一行记录并转化到属性Fields中。

当读取完最后一行后，ReadLine()返回null，ReadNextRecord()返回false。

# 五 生成一个Csv文件的内存结构模型

CsvFile属性：

List<string> Headers 标题

CsvRecords Records 记录集合

 

新建一个Csv文件内存模型的方法：

\1. CsvFile csvFile = new CsvFile(); //此时Headers和Records都是空列表

\2. csvFile. Populate(string filePath)//从一个Csv文件生成内存模型，此时Headers和Records已被填充。

我们现在可以修改或访问CsvFile, 获取某一行记录，获取某一行的某个字段：利用索引器

如何创建一个CsvFile？

CsvFile有两个属性Headers和Records，我们可以先CsvFile file = new CsvFile( ); 然后填充这两个暴露的属性。

 # 六 修改CSV文件字段的值

1. CSVReader修改，或利用CsvFile的索引器修改。



 

 