---
title: 12 关于Avro
toc: true
date: 2018-06-27 07:51:34
---
#### 第 12 章

![img](Hadoop43010757_2cdb48_2d8748-161.jpg)



Avro



Apache \NXQ（<http://avro>. apache, org/）T是一个独立于编程语言的数据序列化系统。 该项目由 Doug Cutting（Hadoop之父）创建，旨在解决 Hadoop 中 Writable 类型的不 足：缺乏语言的可移植性。拥有一个可被多种语言（当前是 C、c++、C#、Java、 PHP、python和 Ruby）处理的数据格式与绑定到单一语言的数据格式相比，前者更 易于与公众共享数据集。Avro同时也更具生命力，该语言将使得数据具有更长的 生命周期，即使原先用于读/写该数据的语言已经不再使用。

但为什么要有一个新的数据序列化系统？与 Apache Thrift和 Google 的 Protocol Buffers相比，Avro有其独有的特性。@与前述系统及其他系统相似，Avro数据是用语 言无关的模式定义的。但与其他系统不同的是，在 Avro 中，代码生成是可选的， 这意味着你可以对遵循指定模式的数据进行读/写操作，即使在此之前代码从来没 有见过这个特殊的数据模式。为此，Avro假设数据模式总是存在的（在读/写数据

时），它形成的是非常精简的编码，因为编码后的数值不需要用字段标识符来打标 签。

Avro模式通常用 JSON 来写，数据通常采用二进制洛式编码，但也有其他选择。 还有一种高级语言称为 Avro IDL，可以使用开发人员更为熟悉的类 C 语言来写模

①    得名于 20 世纪英国一家飞机制造商。

②    基准测试［[http://code.google.eom/p/thrift-protobuf-compare/｝](http://code.google.eom/p/thrift-protobuf-compare/%ef%bd%9d%e8%a1%a8%e6%98%8e%ef%bc%8c%e5%92%8c%e5%85%b6%e4%bb%96%e5%ba%8f%e5%88%97%e5%8c%96%e7%b1%bb%e5%ba%93%e7%9b%b8%e6%af%94%ef%bc%8c)[表明，和其他序列化类库相比，](http://code.google.eom/p/thrift-protobuf-compare/%ef%bd%9d%e8%a1%a8%e6%98%8e%ef%bc%8c%e5%92%8c%e5%85%b6%e4%bb%96%e5%ba%8f%e5%88%97%e5%8c%96%e7%b1%bb%e5%ba%93%e7%9b%b8%e6%af%94%ef%bc%8c) Avro的性能更好。

式。还有一个基于 JSON 的数据编码方式（对构建原型和调试 Avm 数据很有用，因 为它是我们人类可读的）。

Avro 觀苑 X<http://avro>. apache, org/docs/current/spec. /?Zw/）对所有实现都必须支持的二 进制格式进行了精确定义，同时还指定这些实现需要支持的其他 Avro 特性。但 是，该规范并没有为 API 制定规范：实现可以根据自己的需求操作 Avro 数据并给 出相应的 API，因为每个 API 都与语言相关。重要的二进制格式只有一种这一事 实意味着绑定新的编程语言的门槛比较低，可以避免语言和格式组合爆炸问题，否 则将对互操作性造成一定的问题。

Avro有丰富的模式解析（schema resolution）能力。在精心定义的约束条件下，读数 据所用的模式不必与写数据所用的模式相同。由此，Avro是支持模式演化的。例 如，如果有一个新的、可选择的字段要加入记录中，那么只需在用来读取老数据 的模式中声明它即可。新客户端和以前的客户端非常相似，均能读取按旧模式存 储的数据，同时新的客户端可以使用新字段写入新的内容。相反，如果老客户端 读取新客户端写入的数据，会忽略新加入的字段并按照先前的数据模式处理。

Avro为一系列的对象指定了一个对象容器格式，类似于 Hadoop 的顺序文件。Avro 数据文件包含元数据项（模式数据存储在其中），使此文件可以自我声明。Avro数 据文件支持压缩，并且是可切分的，这对 MapReduce 的输人格式至关重要。对 Avro的支持远不止于 MapReduce ，事实上本书中所有的数据处理框架（Pig、 Hive、Crunch、Spark）都能读/写 Avro 数据文件。

Avro还可用于 RPC，但在这里不做详细说明。详情参见规范文档。

##### 12.1 Avro数据类型和模式

Avro定义了少量的基本数据类型，通过编写模式的方式，它们可被用于构建应用 特定的数据结构。考虑到互操作性，实现必须支持所有的 Avro 类型。

表 12-1列举了 Avro的基本类型。每个基本类型也可以使用 type 属性来指定，其 结果是形式更加冗长，示例如下：

每个 Avro 语言的 API 都包含该语言特定的 Avro 类型表示。例如，Avro的 double类型可以用 C、c++和 Java 语言的 double 类型，python的 float 类型以 及 Ruby 的 Float 类型来表示。

表 12-1. Avro基本类型

| 类型     | 描述                         | 模式示例  |
| -------- | ---------------------------- | --------- |
| null     | 空值                         | "null"    |
| boolean  | 二进制值                     | "boolean" |
| int      | 32位带符号整数               | "int"     |
| long     | 64位带符号整数               | "long"    |
| float    | 单精度（32位）IEEE 754浮点数 | "float"   |
| double • | 双精度（64位）IEEE 754浮点数 | ••double" |
| bytes    | 8位无符号字节序列            | "bytes"   |
| string   | Unicode字符序列              | "string"  |

表 12-2列举了 Avro的复杂类型，并为每种类型给出相应的模式示例。

表 12-2. Avro复杂类型

类型

array



map



record



描述

一个排过序的对象集合。特定数 组中的所有对象必须模式相同

未排过序的键-值对。键必须是字 符串，值可以是任何一种类型，但一 个特定 m 叩中所有值必须模式相同 1个任意类型的命名字段集合-



模式示例 {

•■type": ’.array", "items": ,,longH

}



{

"type": "map",

"values": "string”

1_

{

■•type": "record",

••name": "WeatherRecord",

"doc": "A weather reading.",

"fields":[

{"name": "year", "type": "int"}, {"name": "temperature", "type": int"}, {"name": "stationld", "type": string"}

]

}



续表

类型 描述    -    模式示例

enum 一个命名的值集合    {

'    "type": "enum",

"name": "Cutlery",

"doc": "An eating utensil.", "symbols": ["KNIFE", "FORK", "SPOON"]

}

| fixed | 一组固定数量的 8 位无符号字节    | {••type": "name": "size"： 16} | "fixed","MdSHash",         |
| ----- | ------------------------------ | ------------------------------ | -------------------------- |
| union | 模式的并集。并集可用 JSON 数组表 | [                              |                            |
|       | 示，其中毎个元素为一个模式。并 | "null"，雪雪    fl    •    ■雪  |                            |
|       | 集表示的数据必须与其内的某个模 | string{"type":                 | "map", "values": "string"} |
|       | 式相匹配                       | ]                              |                            |

而且，一种语言可能有多种表示或映射。所有的语言都支持动态映射，即使运行 前并不知道具体模式，也可以使用动态映射。对此，Java称为通用映射(generic mapping) 0

另外，Java和 c++实现可以自动生成代码来表示符合某种 Avm 模式的数据。如果 在读/写数据之前就有模式备份的话，代码生成(code generation)能优化数据处理， 这在 Java 中被称为特殊映射(specific mapping)0同时，为用户代码生成的类与为 通用代码生成的类相比，前者提供的 API 更贴近问题域。

Java还有第三类映射，即自反映射(reflect mapping，将 Avro 类型映射到已有的 Java类型)。它的速度比通用映射和特殊映射都慢，但不失为定义一个类型的方便途 径，原因在于 Avm 能够自动推断模式。

表 12-3列举了 Java的类型映射。如表中所示，除非有特别说明，否则特殊映射和 通用映射相同。类似地，除非有特别说明，否则自反映射与特殊映射相同。特殊 映射与通用映射仅在 record、enum和 fixed 三个类型上有区别，它们的特殊映 射都有自动生成的类，类名由 name 属性和可选的 namesapce 属性决定。

表 12-3. Avro的 Java 类型映射

| null    | null类型               |                           |                          |
| ------- | ---------------------- | ------------------------- | ------------------------ |
| boolean | boolean                |                           |                          |
| int     | int                    |                           | short 或 int             |
| long    | long                   |                           |                          |
| float   | float                  | •                         |                          |
| double  | double                 |                           |                          |
| bytes   | java.nio.bytebuffer    |                           | 字节数组                 |
| string  | org.apache.avro.       |                           | java.lang.String         |
|         | util.utf8              |                           |                          |
| array   | org.apache.avro.       |                           | 数组或                   |
|         | generic.GenericArray   |                           | java.util.Collection     |
| map     | java.util.map          |                           |                          |
| record  | org.apache.avro.       | 生成实现 org.apache.       | 具有零参数构造函数的任   |
|         | generic .genericrecord | avro.specific.Specific    | 意用户类。继承了所有不   |
|         |                        | Record类的实现            | 传递的实例字段           |
| enum    | java.lang.string       | 生成〕ava enum类型        | 任意〕ava enum类型       |
| fixed   | org.apache.avro.       | 生成实现                  | org.apache.avro.generic. |
|         | generic.genericfixed   | org.apache.avro<specific. | genericFixed             |
|         |                        | SpecificFixed 的类        |                          |
| union   | java.lang.Object       |                           |                          |

磨一 Avro string类型既可以通过 Java String类型来表示，也可以通过 Avro Utf8 Java 类型来表示。选择使用 Utf8 的原因在于其高效率：因为 Avro Utf8类型是易变 的，单个 Utf8 实例可以重用，并可对一系列值进行衡写操作。另外，Java String 在新建对象时就要进行 UTF-8解码，而 Avro 执行 UtfB 解码较晚一些，在某些 情况下，这样做可以提高系统性能。

Utf8 实现了 Java 的 java.lang.CharSequence 接口，该接口可以与 Java 类库 实现互操作。在其他情况下可能需要通过调用 toStr>ing()方法将 Utf8 实例转 化成 String 对象。

在通用映射和特殊映射中，UtfB都是默认的，但是对于特定的映射也可以使用 String。有两个方法可以达到该目的。第一个方法是将模式中的 avro. java.string 属性设置成 String：

{ "type": "string") "avro.java.string": "String" }

另外一个方法是，对于一些特定的映射操作，你可以构建基于 String 的 get() 和 set()方法的类。如果使用 Avro Maven插件，该功能可以通过将 stringType属性设置成 String 来实现(在 12.2.1节中有一个相关的例子)。

最后，请注意，Java自反映射始终使用 Java 的 String 对象，其主要原因是 Java的兼容性，而非性能。

##### 12.2内存中的序列化和反序列化

Avro为序列化和反序列化提供了一些 API，如果想把 Avro 集成到现有系统(比如 已定义帧格式的消息系统)，这些 API 函数就很有用，而对于其他情况，请考虑使 用 Avro 的数据文件格式。

让我们写一个 Java 程序来从数据流读/写 Avro 数据。首先以一个简单的 Avro 模式 为例，它用于表示以记录形式出现的一对字符串:

{

"type": "record",

"name’. ： nStingPair.、

•’doc": nA pair of strings.

"fields":[

{"name": "left", "type": "string"}, {"name": "right", "type": "string"}

]

}

如果此模式存储在类路径下一个名为 StringPair.avsc的文件中(.avsc是 Avro 模式 文件的常用扩展名)，我们可以通过下面的两行代码进行加载：

Schema.Parser parser = new Schema.Parser();

Schema schema = parser.parse(getClass().getResourceAsStream("StringPair.avscH));

可以使用以下的通用 API 新建一个 Avro 记录实例：

GenericRecord datum = new GenericData.Record(schema); datum.put("left", "L"); datum.put(Hright"R");

接下来，我们将记录序列化到输出流中：

ByteArrayOutputStream out = new ByteArrayOutputStream(); DatumWriter<GenericRecord> writer =

new GenericDatumWriter<GenericRecord>(schema);

Encoder encoder = EncoderFactory.get().binaryEncoder(outnull); writer.write(datum, encoder);

encoder.flush();

out.close();

其中有两个重要的对象：DatumWriter和 Encoder。DatumWriter对象将数据对 象翻译成 Encoder 对象可以理解的类型，然后由后者写入输出流。这里，我们使 用了 GenericDatumWriter对象，它将 GenericRecord 字段的值传递给 Encoder对象。由于没有重用先前构建的 encoder，此处我们将 null 传递 encoder

工厂。

在本例中，只有一个对象需要写到输出流，但如果需要写若干个对象，可以调用 write()方法，然后再关闭输入流。

我们需要将该模式传递给 GenericDatumWriter 对象，因为它会根据模式来确定 将数据对象中的哪些数值写到输出流。在调用 writer 的 write()方法后刷新 encoder，然后关闭输出流。

我们也可以使用反向的处理过程来从字节缓冲区中读回对象:

DatumReader<GenericRecord> reader =

new GenericDatumReader<GenericRecord>(schema);

Decoder decoder = DecoderFactory.get().binaryDecoder(out.toByteArray(), null);

GenericRecord result = reader.readCnull, decoder);

assertThat(result.get(Hleftn).toString(), is("L"));

assertThat (result, get (•• right"). toSt ring (), is (" R"));

我们需要给 binaryDecoder()和 read()的调用传递空值(null)， 重用对象(分别是 decoder 或记录)。

为这里没有



由干 result• get(11 left11)^0 result.get("right")返回的对象是 Utf8 类型

的，



此我们需要通过调用 toString()方法将它们转型为 Java String类型

###### 特定 API

现在，让我们来看看使用特定 API 的等价代码。通过使用 Avro 的 Maven 插件编 译模式，我们可以根据模式文件生成 StringPair 类。以下是与 Maven Project Object Model(POM)相关的部分:

<project>

• 參 •

<build>

<plugins>

<plugin>

<groupld>org.apache.avro</groupld>

<artifactld>avro-maven-plugin</artifactld>

<version>${avro.version}</version> <executions>

<execution>

*

<id>schemas</id>

<phase>generate-sources</phase>

<goals>

<goal>schema</goal>

</goals>

〈configuration〉

<includes>

<include>StringPair.avsc</include>

</includes>

<stringType>String</stringType>

<sourceDirectory>src/main/resources</sourceDirectory>

<outputDirectory>${project.build.directory}/generated-sources/java

</outputDirectory>

〈/configuration〉

</execution>

</executions>

</plugin>

</plugins>

</build>

• •參

</project>

也可以不使用 Maven ，而是使用 Avro 的 Ant 任务(org.apache.avro. specific.SchemaTask)或者 Avro 的命令行工具®来为一个模式生成 Java 代码。

在序列化和反序列化的代码中，我们通过构建一个 StringPair 实例来替代 GenericRecord对象(使用 SpecificDatumWriter 类将该对象写入数据流，并使 用 SpecificDatumReader 类读回数据):

StringPair datum = new StringPair(); datum.setLeft(HLH); datum.setRight("R");

ByteArrayOutputStream out = new ByteArrayOutputStream();

DatumWriter<StringPair> writer =

new SpecificDatumWriter〈StringPair>(StringPair•class);

Encoder encoder = EncoderFactory.get().binaryEncoder(out, null); writer.writeCdatum^ encoder);

encoder.flush(); out.close();

DatumReader<StringPair> reader =

new SpecificDatumReader〈StringPair>(StringPair•class);

Decoder decoder = DecoderFactory.get().binaryDecoder(out.toByteArray(), null);

①可以下载获得 Avro 的源文件和二进制文件，网址为[http://avro.apache.org/releases.html](http://avro.apache.org/releases.html%e3%80%82%e9%94%ae%e5%85%a5)[。键入](http://avro.apache.org/releases.html%e3%80%82%e9%94%ae%e5%85%a5) 命令 java -jar avro-tools-*. jar，即可获得使用指南。

StringPair result = reader.read(nullJ decoder); assertThat(result.getLeft()^ is("L")); assertThat(result.getRight(), is(’’R"));

##### 12.3 Avro数据文件

Avro的对象容器文件格式主要用于存储 Avro 对象序列。这与 Hadoop 顺序文件的 设计非常相似，详见 5.4.1节，它们之间的最大区别在于 Avm 数据文件主要是面 向跨语言使用而设计的，因此，我们可以用 python 语言写入文件，并用 C 语言来 读取文件，下一节将详细探讨。

在数据文件的头部中含有元数据，它包括一个 Avro 模式和一个叹 war/rer（同步 标识），紧接着是一系列包含序列化 Avro 对象的数据块（压缩可选）。数据块通过 sync marker分隔，而 sync marker对该文件来说是唯一的（特定文件的标识信息存

储在文件头部），并允许在文件中搜索到任意位置之后通过块边界快速地重新进行 同步。因此，Avro数据文件是可切分的，适合 MapReduce 快速处理。

将 Avro 的对象写到数据文件与写到数据流类似。就像前面一样，我们需要使用 DatumWriter，但并没有用到 Encoder，而是通过 DatumWniter 来创建一个 DataFileWriter实例。然后便可以新建一个数据文件(该文件一般有.avro扩展 名)，并向它附加新写入的对象:

File file = new File(Hdata.avroH);

DatumWriter<GenericRecord> writer =

new GenericDatumWriter<GenericRecord>(schema);

DataFileWriter<GenericRecord> dataFileWriter =

new DataFileWriter<GenericRecord>(writer); dataFileWriter.createCschema^ file); dataFileWriter.append(datum); dataFileWriter.close();

写入数据文件的对象必须遵循相应的文件模式，否则在调用 append（）方法时会抛 出异常。

这个例子演示了如何将对象写到本地文件（前面代码段中的 java.io.File），但如果使 用重载的 DataFileWriter 的 create（）方法，则可以将数据对象写到任何一个 java.io.OutputStream 对象中。例如，通过对 FileSystem 对象调用 create（） 方法，可以返回 OutputStream 对象，进而将文件写入 HDFS，更多详情可以参 见 3.5.3节 0

从数据文件读取对象与前面例子中从内存数据流读取数据类似，只有一个重要的 区别：我们不需要指定模式，因为可以从文件的元数据中读取它。事实上，还可 以对 DataFileReader 实例调用 getSchema()方法来获取模式，并验证该模式是 否和原始写入对象的模式相同：

DatumReader<GenericRecord> reader = new GenericDatumReader<GenericRecord>(); DataFileReader<GenericRecord> dataFileReader =

new DataFileReader<GenericRecord>(file, reader); assertThat("Schema is the same", schema, is(dataFileReader.getSchema()));

DataFileReader对象是一个常规的 Java 迭代器，由此我们可以调用 hashNext() 和 next()方法来迭代数据对象。下面的代码检查是否只有一条记录以及该记录是 否有期望的字段值：

assertThatCdataFileReader.hasNextO^ is (true)); GenericRecord result = dataFileReader.next(); assertThat(result.get("left").toString(), is(•• L")); assertThat(result.get(•’right")• toString(), is(•• R")); assertThat(dataFileReader.hasNext()is(false));

但是，更适合的做法是使用重载并将返回对象实例作为输入参数(该例中，为 GenericRecord对象)，而非直接使用 next()方法，因为这样可以重用对象，减 少对象分配和垃圾回收所产生的开销，特别是当文件中包含有很多对象时。代码 如下所示：

GenericRecord record = null; while (dataFileReader.hasNext()) {

record = dataFileReader.next(record);

// process record

}

如果对象重用不是那么重要，则可以使用如下更简短的形式：

for (GenericRecord record : dataFileReader) {

// process record

}

如果只是普通的从 Hadoop 文件系统中读取文件，可以使用 Avro 的 Fslnput 对象 来指定使用 Hadoop Path对象作为输入对象。事实上，DataFileReader对象提供对 Avro数据文件的随机访问(通过 seek()和 sync()方法)，不过在大多数情况下， 顺序访问数据流就足够了，而此时应当使用 DataFileStream 对象。 DataFileStream对象可以从任意 Java InputStream对象中读取数据。

##### 12.4互操作性

为了说明 Avro 的语言互操作性，让我们试着用一种语言(python)来写入数据文 件，并用另一种语言(Java)来读取这个文件。

###### 12.4.1 python API

范例 12-1中的程序从标准输入中读取由逗号分隔的字符串并将其以 StringPair 记录的方式写入 Avro 数据文件。与写数据文件的 Java 代码类似，我们需要新建 一个 DatumWriter 对象和一个 DataFileWriter 对象，注意，我们在代码中嵌入 了 Avro模式，尽管没有这个模式，我们仍然可以从文件中正确读取数据。

python以目录形式表示 Avro 记录，从标准输入中读取的每一行都被转换为 diet 对象并附加到 DataFileWriter 对象的末尾。

范例 12-1.这个 python 程序将 Avro 的成对形式的记录写入一个数据文件

import os import string import sys

| fromfromfrom | avroavroavro | importimportimport | schemaiodatafile |
| ------------ | ------------ | ------------------ | ---------------- |
| if _         | _name_       | 番                 | _main_'          |

if len(sys.argv) != 2:

sys.exit('Usage: %s <data_file>' % sys>argv[0]) avro一 file = sys.argv[l] writer = open(avro_file, 'wb*) datum一 writer = io<DatumWriter() schema一 object = schema.parse(H\

{ "type": "record"，

"name": "StringPair",

"doc": nA pair of strings.",

"fields":[

{"name": "left", "type": "string"},

{"name": "right", "type.•: "string"}

]

}••)

dfw = datafile.DataFileWriter(writer\ datum一 writer, schema一 object) for line in sys.stdin.readlines():

(left, right) = string, split (line. strip()    •/)

dfw.append({* left1:left, 'right1:right});

dfw.close()

在运行该程序之前，我们需要为 python 安装 Avro：

% easy_install avro

为了运行该程序，我们需要指定文件名输出结果会写到这个文件）， 并通过标准输入来发送输入的成对记录，结束文件输入时键入快捷键 Ctrl-D：

% python chl2-avro/src/main/py/write_pairs.py pairs.avro

a,    l

b>3

b,    2

AD

###### 12.4.2 Avro 工具集

下面我们将使用 Avro 工具（Java）显示 pairs.avro的内容。JAR工具可以从 Avro 网 站上获得，此处假设它已经被存放在本地目录似中。使用 tojson 命令 可以将 Avro 数据文件中的内容转储为 JSON 并打印输出到控制台。

% java -jar $AVRO_HOME/avro-tools-*.jar tojson pairs•avro

{.•leftWVrighPvi.，}

{"left":"c'"right":"2"}

{"left":"b\"right":"3"}

这样，我们便成功交换了两个 Avro 实现（python和 Java）的复杂数据。

##### 12.5模式解析

我们可以选择使用不同于写入数据的模式（writer的模式）来读回数据（reader的模

式）。这非常有用，因为它意味着模式的演化。例如，为了便于说明，我们考虑新 增一个 description 字段，从而形成一个新的模式：

{

"type": "record")

"name": "StringPair",

"doc": nA pair of strings with an added field.",

"fields":[

{"name": "left", "type••: "string"},

{"name": "right", "type": "string"},

{"name": "description", "type": "string", "default": ••••}

]

}

由于已经为 description 字段指定了一个默认值（空字符串®）以供 Avro 在读取没 有定义字段的记录时使用，因此我们可以使用该模式来读取之前已经序列化的数 据。如果忽略 default 属性，那么在读取旧数据时会报错。

要想将默认值设为 null 而非空字符串，我们需要使用具有 Avro 的 null 类型 的并集来定义 description 字段：

{"name": "description", Mtype": ["null", "string"], "default": null}

当读模式不同于写模式时，则需要调用 GenericDatumReader 的构造函数，它以 两个模式对象作为输入参数，即 reader 的模式对象和 writer 的模式对象，并按照 以下顺序传递：

DatumReader<GenericRecord> reader =

new GenericDatumReader<GenericRecord>(schemaJ newSchema);

Decoder decoder = DecoderFactory.get().binaryDecoder(out.toByteArray() null);

GenericRecord result = reader.readCnull^ decoder); assertThat(result.get(,,left,,).toString()>isCL'1))； assertThat(result.getC^ight'^.toStringO, isC'R"))； assertThat(result • get("description•’)• toString(), is(••"));

对于 writer 的模式已经被存储在元数据中的数据文件，我们只需要显式指定其 reader的模式，具体做法是将 null 作为 writer 的模式传递：

DatumReader<GenericRecord> reader = new GenericDatumReader<GenericRecord>(null, newSchema);

模式不同的另外一种常见用法是去掉记录中的某些字段，这种操作也可以称为投 影(projection)。当记录中包含大量的字段，但需要读取的只是其中的一部分时， 这种做法非常有用。例如，使用下面这个模式可以仅读取 StringPair 对象中的 right字段:

{

"type": "record",

"name": "StringPair",

••doc": "The right field of a pair of strings..、

"fields":[

{"name": "right", "type": "string"}

]

}

①使用 JSON 对字段默认值进行编码。参见 Avro 规范中对每个数据类型进行编码描述。

模式解析规则可以直接解决模式从一个版本演化为另一个版本时可能产生的问 题。在 Avro 规范中对所有 Avro 类型均有详细说明。表 12-4从类型读/写（客户端 和服务器端）的角度总结了记录演化规则。

表 12-4.记录的模式解析

新模式    写入读取操作

增加的字段    旧    新    通过默认值读取新字段，因为写入时没有该字段

_新    旧    读取时不知道新写入的新字段，所以忽略该字段（投影）

删除的字段    旧    新    读取时忽略已删除的字段（投影）

新 旧    写入时不写入已删除的字段。如果旧模式对该字段有默认值，那

么读取时可以使用该默认值，否则报错。在这种情况下，最好同 时更新读取模式或在更新写入模式之前更新读取模式

对于 Avro 模式演化来说，另一种有用的技术是使用别名（6///仍打）。别名允许你在 读 Avro 数据的模式与写 Avro 数据的模式中使用不同的字段名称。例如，下面这

个 reader 的模式能够以新的字段名称（Bp first和 second）来读取 StringPair 数 据，而非写入数据时所使用的字段名称（即 left 和 right）。

{

"type": "record",

"name": "StringPair",

udoc": "A pair of strings with aliased field names.’’，

"fields":[

{"name": "first", "type": "string", "aliases": ["left"]},

{"name": "second", "type": Hstring' "aliases": [•’right'1]}

]

}

注意，别名的主要作用是（在读取的时候）将 writei•的模式转换为 reader 的模式，但

是别名对读取程序是不可见的。在上述例子中，读取程序无法再使用字段名称 left和 right，因为它们已经被转换为 first 和 second。

##### 12.6排列顺序

Avro定义了对象的排列顺序。大多数 Avro 类型的排列顺序与用户期望符合，例 如，数值型按照数值的升序进行排序。而其他有一些类型则没那么巧妙了，例 如，枚举通过符号的定义而非符号字符串的值来排序。

除了 record之外，所有类型均按照 Avro 规范中预先定义的规则来排序，这些规 则不能被用户改写。但对于记录，可以通过指定 order 属性来控制排列顺序，它 有三个值:ascending（默认值）、descending（降序）或 ignore（如此一来，在排序 比较时可以忽略此字段。） 例如，通过将 right 字段的排列顺序设置为 descending，可以使下述模式 （SortedStringPair.avsc）定义饱 StringPair 记录按降序排序。在排序时忽略了 left字段，但它依旧保留在投影中：

{

"type": "record",

’■name••: "StringPair’、

ndocn: "A pair of strings, sorted by right field descending.", "fields":[

{’•name": .’left' "type": "string' "order": "ignore"}, {"name": "right", "type": "string", "order": "descending"}

]

}

按照文档中 reader 的模式所指定的顺序，记录中的字段两两进行比较，因此，通 过指定一个恰当的 reader 的模式，可以对数据记录使用任意顺序。下面这个模式 （Sw/Zc/zec/S/nTigAz/rawc）定义的是先按 right 字段，再按 left 字段排序：

{

"type": "record",

"name": "StringPair",

••doc••: nA pair of strings, sorted by right then left.’、

"fields":[

{"name”： "right", "type": "string"},

{"name": "left", "type": "string"}

]

}

Avro实现了高效的二进制比较。也就是说，Avro不需要将二进制对象反序列化为 对象即可实现比较，因为它可以直接对字节流进行操作。®在使用 StringPair 模 式的情况下（没有 order 属性），Avro按以下方式实现二进制比较。

第一个字段（即 left 字段）使用 UTF-8编码，由此 Avro 可以根据字母表顺序进行 比较。如果它们不同，则判定其顺序，且 Avro 可以在该处停止比较。否则，如果 这两个字节顺序是相同的，那么比较第二个字段（SP right字段），同样在字节尺 度上使用字母表序排列，因为该字段同样也使用 UTF-8编码。

①该属性的一个有用结果是，我们可以根据对象或相应的二进制表示（后者在 BinaryData 对象 上使用 hashCode（）静态方法）算出 Avro 数据的哈希代码，两种情况下结果相同。

注意，这里描述的比较方法在逻辑上与 5.3.3节所描述的二进制比较器相同。更重 要的是 Avro 已经为我们提供了比较器，所以无需重写和维护这部分代码，同时我 们也可以通过修改 reader 的模式来简单修改排列顺序。对于 SortedStringPair.avsc 或 SwitchedStringPair.avsc模式来说，Avro所使用的比较方法本质上与刚才所描述 的是一致的，只不过要考虑比较哪个字段，考虑使用哪种顺序，是升序还是降 序。

在本章稍后部分，我们会将 Avro 的排序逻辑与 MapReduce 联合使用，实现 Avro 数据文件的并行排序。

##### 12.7 关于 Avro MapReduce

为了方便在 Avro 数据上运行 MapReduce 程序，Avro提供了一些 API 类。我们将 使用 org.apache.avro.mapreduce 包中的新 MapReduce API 类，你也可以从 org. apache, avro.mapred 包中找到（旧风格的）MapReduce API 类。

这次，我们使用 Avro MapReduce API来重写找出天气数据集中每年最高温度的 MapReduce程序。我们用下列模式来表征天气记录：

{

"type": "record",

’’name": "WeatherRecord",

’’doc": "A weather reading.",

"fields":[

{"name••: "year", "type": "int"},

{"name": •■temperature' "type": "int"}， {’•name": "stationld", "type": "string"}

]

}

范例 12-2中的程序读取文本输入（文本格式在前面几章出现过），并输出包含天气 记录的 Avro 数据文件。

范例 12-2.该 MapReduce 找出最高气温，输出的是 Avro 文件

public class AvroGenericMaxTemperature extends Configured implements Tool {

private static final Schema SCHEMA = new Schema.Parser().parse(

"\"type\": \"record\"," +

"\"name\": \"WeatherRecord\"," +

H \"doc\": \HA weather reading.\"/• +

•• \"fields\+

" {\"name\": \"year\", \"type\":    +

" {\"name\••: \,,temperature\,\ \"type\": \"int\"}," + " {\"name\••: \"stationld\", \"type\": \"string\"}" +

•• ]n +

• • J H

)； public static class MaxTemperatureMapper

extends Mapper<LongWritable, Text, AvroKey<Integer>,

AvroValue<GenericRecord>> {

private NcdcRecordParser parser = new NcdcRecordParser(); private GenericRecord record = new GenericData.Record(SCHEMA);

(^Override

protected void map(LongWritable key. Text value. Context context) throws IOException^ InterruptedException {

parser.parse(value.toString()); if (parser.isValidTemperature()) {

record.put(..yearn，parser.getYearlnt()); record .put (•■temperature1、parser. getAirTemperature()); record.put(••stationld.’，parser.getStationId()); context.write(new AvroKey<Integer>(parser.getYearlnt()),

new AvroValue<GenericRecord>(record));

}

}

}

public static class MaxTemperatureReducer

extends Reducer<AvroKey<Integer>> AvroValue<GenericRecord>^

AvroKey<GenericRecord>, NullWritable> {

^Override

protected void reduce(AvroKey<Integer> key,

Iterable<AvroValue<GenericRecord>> values. Context context) throws IOException^ InterruptedException {

GenericRecord max = null;

for (AvroValue<GenericRecord> value : values) {

GenericRecord record = value<datum(); if (max == null ||

(Integer) record.get(''temperature1') > (Integer) max.get(''temperature")) {

max = newWeatherRecord(record);

}

}

context.write(new AvroKey(max), NullWritable.get());

}

private GenericRecord newWeatherRecord(GenericRecord value) {

GenericRecord record = new GenericData.Record(SCHEMA); record• put(Hyear*\ value.get(..year•■)); record.put(’• temperaturevalue.get("temperature")); record.put("stationlcT, value.get(Hstationldn)); return record;

}

}

^Override

public int run(String[] args) throws Exception { if (argSelength != 2) {

System.err.printf("Usage: %s [generic options] <input><output>\n,,J getClass().getSimpleName());

ToolRunner.printGGnericCommandUsage(System.err); return -1;

}

Job job = new〕ob(getConf(), ’’Max temperature"); job.set3arByClass(getClass());

job.getConfiguration().setBoolean(

]ob.MAPREDUCE一]OBJJSERJZLASSPATH—FIRST, true);

FilelnputFormat.addInputPath(job, new Path(args[0]));

FileOutputFormat•setOutputPath(job, new Path(args[l]));

AvroDob.setMapOutputKeySchema(job, Schema.create(Schema.Type.INT)); AvroDob.setMapOutputValueSchema(job, SCHEMA);

Avro〕ob•setOutputKeySchema(job, SCHEMA);

job.setInputFormatClass(TextInputFormat.class); job.setOutputFormatClass(AvroKeyOutputFormat.class);

job.setMapperClass(MaxTemperatureMapper.class); job.setReducerClass(MaxTemperatureReducer.class);

return job•waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception {

int exitCode = ToolRunner.run(new AvroGenericMaxTemperature(), args); System.exit(exitCode);

}

}

这个程序使用的是通用 Avro 映射（Generic Avro mapping）。这样就不需要通过生成 代码来表征数据记录，从而避免了损失类型安全性（通过字符串值来引用字段名 称，例如“temperature”）的代价\为了方便起见，天气记录的模式已加入到 代码中（读取 SCHMA 常量）。不过在实际情况下，从驱动器本地文件中读取模式， 并通过 Hadoop 作业配置将模式传递给 m 叩 per 和 reducer，可以提高代码的可维护 性。（具体技巧可以参见 9.4节。） 该 API 与常规的 Hadoop MapReduce API有两个较大不同之处。第一个不同是对 Avro Java类型封装的使用。针对这个 MapReduce 程序，键是年份（一个整数），值

①通过生成类的方式使用指定映射（specific mapping）的例子，请参见示例代码中的 AvroSpecificiMaxTemperature 类 0

358 第 12 章

是天气记录，由 Avro 的 GenericRecord 表征。在 map 输出（以及 reduce 输入） 中，键类型被转型为 AvroKey<Integer> ，值类型被转型为 AvroValue <GenericRecord>0

MaxTemperatureReducer针对每个键（年份）所对应的所有记录执行迭代运算，并 找到那条有最高温度的记录。有必要对目前找到的最高温度的记录做一个备份， 因为该迭代运算为了达到高效的目的需要重用该实例，并且只更新相关字段。

与传统 MapReduce 的第二个差异是，它使用 Avro]ob来配置作业。AvroZIob类非 常适用于为输入、map输出以及最后输出数据指定 Avm 模式。在上述程序中没有 设置输人模式，因为我们是从文本文件中读取数据。map输出的键模式是 Avro int，值模式是天气记录模式。最后输出数据模式是天气记录模式，并且写入 Avro数据文件中的输出格式是 AvroOutputFormat 格式，值被忽略，该值为 NullWritable。

下面的命令行代码显示了如何在一个小型采样数据集上运行该程序:

% export HADOOP_CLASSPATH=avro-examples.jar

% export HADOOP_USER_CLASSPATH_FIRST=true # override version of Avro in Hadoop

% hadoop jar avro-examples.jar AvroGenericMaxTemperature \ input/ncdc/sample>txt output

执行完成之时，我们可以使用 Avm 工具 JAR 来查看输出结果，该结果是 JSON 格 式的 Avro 数据文件，每行一条记录如下：

% java -jar $AVRO_HOME/avro-tools-*.jar tojson output/part-00000•avro

{"year":1949,"temperature":111,"stationld":n012650-99999"}

{"year":1950,"temperature":22,"stationld":"011990-99999"}

在上述例子中，我们读取的是一个文本文件，然后创建一个 Avm 数据文件，当然 其他组合也是可行的，这有利于将数据格式在 Avro 格式和其他格式之间来回转 换，例如 SequenceFile。详情参见 Avro MapReduce包的说明文档。

##### 12.8使用 Avro MapReduce进行排序

在本节中，我们利用 Avro 的排序能力，并结合使用 MapReduce，写一段对 Avro 数据文件进行排序的程序（范例 12-3）。

范例 12-3.对 Avro 数据文件进行排序的 MapReduce 程序 public class AvroSort extends Configured implements Tool {

static class SortMapper<K> extends Mapper<AvroKey<K>J NullWritable, AvroKey<K>, AvroValue<K>> {

^Override

protected void map(AvroKey<K> key, NullWritable value.

Context context) throws IOException, InterruptedException { context.write(key, new AvroValue<K>(key.datum()));

}

}

static class SortReducer<K> extends Reducer<AvroKey<K>J AvroValue<K>J AvroKey<K>, NullWritable〉 {

^Override

protected void reduce(AvroKey<K> key, Iterable<AvroValue<K>> values, Context context) throws IOException, InterruptedException {

for (AvroValue<K> value : values) { context.write(new AvroKey(value.datum())NullWritable.get());

}

}

}

^Override

public int run(String[] args) throws Exception {

if (args.length != 3) {

System.err.printf(

’.Usage: %s [generic options] <inputxoutputxschema-file>\n*\ getClass().getSimpleName());

ToolRunner.printGenericCommandllsage( System, err); return -1;

}

String input = args[0];

String output = args[l];

String schemaFile = args[2];

Dob job = new 〕ob(getConf(), HAvro sort"); job.setDarByClass(getClass());

job•getConfiguration().setBoolean(

]ob.MAPREDUCEJJOBJJSER一 CLASSPATH—FIRST, true);

FilelnputFormat.addInputPath(job^ new Path(input));

FileOutputFormat.setOutputPath(job, new Path(output));

Avro〕ob.setDataModelClass(job, GenericData.class);

Schema schema = new Schema.Parser().parse(new File(schemaFile)); Avro3ob.setInputKeySchema(job, schema);

AvroJob.setMapOutputKeySchema(jobschema);

Avro]ob.setMapOutputValueSchema(job, schema);

AvroDob.setOutputKeySchema(job, schema);

job.setInputFormatClass(AvroKeyInputFormat.class);

job.setOutputFormatClass(AvroKeyOutputFormat.class);

job.setOutputKeyClass(AvroKey.class);

job.setOutputValueClass(NullWritable.class); job.setMapperClass(SortMapper.class); job.setReducerClass(SortReducer.class);

return job.waitForCompletion(true) ? 0 : 1;

}

public static void main(String[] args) throws Exception { int exitCode = ToolRunner.run(new AvroSort(), args); System.exit(exitCode);

}

}

这个程序（使用了通用的 Avro 映射，因此无需生成任何代码）能够对由通用类型参 数 K 表示的任何 Java 类型的 Avro 记录进行排序。我们选择值的类型与键的类型 相同，以便在值按照键分组后，可以对有多值对应于一个键的情况（根据排序函数）输 出同一个键对应的所有值，不丢失任何记录。"Mapper输出了封装在 AvroKey 和

AvroValue中的输入键。Reducer作为一个识别器，将值作为输出键传递，并写 入 Avro 数据文件。

排序发生在 MapReduce 的混洗过程中，排序函数由传入程序中的 Avro 模式确 定。下面我们使用该程序对先前创建的 pairs.avro文件进行排序，根据 SortedStringPair.avsc模式，我们将按 right 字段的降序进行排序。首先，使用 Avro工具 JAR 检査输入数据：

% java -jar $AVRO HOME/avro-tools-*.jar tojson i叩 ut/avro/pairs.avro

{" lef tHa11/'right"

{"left":"c\"right":"2"}

{"left":"b\"right":"3"}

然后运行排序程序:

% hadoop jar avro-examples.jar AvroSort input/avro/pairs.avro output \ chl2-avro/src/main/resources/SortedStringPair.avsc

最后，检查输出并査看是否正确排序。

% java -jar $AVRO_HOME/avro-tools-*.jar tojson output/part-r-00000.avro

{nleftn    right

{nleft":"b","right":"2"}

①如果此处我们使用 identity mapper和 reducer，那么程序会在排序的同时删除重复的键。9.2.4 节遇到过在同一键情况下复制值对象来实现信息复制的想法。

{"left ••: "c"/*right":"2"}

##### 12.9其他语言的 Avro

除了 java语言之外，



还有其他语言和框架也可以使用 Avro 数据



AvroAsText Input Format 被设计用来允许 Hadoop Streaming 程序读取 Avro 数据 文件。文件中的每条数据均被转化为一个字符串，通过 JSON 格式或者原始字节 （如果是 Avrobytes 类型的话）来表示。另一方

你可以指定



AvroTextOut put Format作为 Streaming 作业的输出格式，并按照 bytes 模式创

建 Avro 数据文件，其中每条记录是从 Streaming 输出的、由制表符分隔的键-值 对。这两个类均可以在 org.apache.avro.mapred包中找到。

运用 Pig、Hive、Crunch和 Spark 等框架来处理 Avro 数据文件也值得考虑，因为 它们都可以通过指定适合的数据存储格式来读/写 Avro 数据文件。详情可以参见 本书中的相关章节。


