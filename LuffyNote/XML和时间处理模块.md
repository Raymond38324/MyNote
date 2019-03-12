# XML模块

xml的格式如下，就是通过<>节点来区别数据结构的:

```xml
<?xml version="1.0"?>
<data>
    <country name="Liechtenstein">
        <rank updated="yes">2</rank>
        <year>2008</year>
        <gdppc>141100</gdppc>
        <neighbor name="Austria" direction="E"/>
        <neighbor name="Switzerland" direction="W"/>
    </country>
    <country name="Singapore">
        <rank updated="yes">5</rank>
        <year>2011</year>
        <gdppc>59900</gdppc>
        <neighbor name="Malaysia" direction="N"/>
    </country>
    <country name="Panama">
        <rank updated="yes">69</rank>
        <year>2011</year>
        <gdppc>13600</gdppc>
        <neighbor name="Costa Rica" direction="W"/>
        <neighbor name="Colombia" direction="E"/>
    </country>
</data>
```

## python操作xml 

### 查看

```python
import xml.etree.ElementTree as ET

tree = ET.parse("xmltest.xml")
root = tree.getroot()
print(root.tag)

#遍历xml文档
for child in root:
    print(child.tag, child.attrib)
    for i in child:
        print(i.tag,i.text)

#只遍历year 节点
for node in root.iter('year'):
    print(node.tag,node.text)
```

### 修改和删除

```python
import xml.etree.ElementTree as ET

tree = ET.parse("xmltest.xml")
root = tree.getroot()

#修改
for node in root.iter('year'):
    new_year = int(node.text) + 1
    node.text = str(new_year)
    node.set("updated","yes")

tree.write("xmltest.xml")


#删除node
for country in root.findall('country'):
   rank = int(country.find('rank').text)
   if rank > 50:
     root.remove(country)

tree.write('output.xml')
```

### 创建xml文档

```python
import xml.etree.ElementTree as ET


new_xml = ET.Element("namelist")
name = ET.SubElement(new_xml,"name",attrib={"enrolled":"yes"})
age = ET.SubElement(name,"age",attrib={"checked":"no"})
sex = ET.SubElement(name,"sex")
sex.text = '33'
name2 = ET.SubElement(new_xml,"name",attrib={"enrolled":"no"})
age = ET.SubElement(name2,"age")
age.text = '19'

et = ET.ElementTree(new_xml) #生成文档对象
et.write("test.xml", encoding="utf-8",xml_declaration=True)

ET.dump(new_xml) #打印生成的格式
```

# time & datetime 模块

## time模块

### **表示时间的几种方式：**

1.  时间戳
2.  格式化的时间字符串
3.  元组（struct_time）共九个元素。由于Python的time模块实现主要调用C库，所以各个平台可能有所不同。

### 几个关于时间的定义

**UTC（Coordinated Universal Time，世界协调时）**亦即格林威治天文时间，世界标准时间。在中国为UTC+8。DST（Daylight Saving Time）即夏令时。

**时间戳**（timestamp）的方式：通常来说，时间戳表示的是从1970年1月1日00:00:00开始按秒计算的偏移量。我们运行“type(time.time())”，返回的是float类型。

**元组（struct_time）方式**：struct_time元组共有9个元素，返回struct_time的函数主要有gmtime()，localtime()，strptime()。

#### 元组中的几个元素：

|索引（Index） |  属性（Attribute）   | 值（Values）|
|----|----|----|
|0   |  tm_year（年）  |               比如2011 |
|1|     tm_mon（月）     |        1 - 12|
|2  |   tm_mday（日）             |    1 - 31|
|3    | tm_hour（时）            |     0 - 23|
|4     |tm_min（分）            | 0 - 59|
|5     |tm_sec（秒）            | 0 - 61|
|6     |tm_wday（weekday）           | 0 - 6（0表示周日）|
|7     |tm_yday（一年中的第几天）   | 1 - 366|
|8     |tm_isdst（是否是夏令时）        |    默认为-1|

### time模块的方法

- time.localtime([secs])：将一个时间戳转换为当前时区的struct_time。secs参数未提供，则以当前时间为准。
- time.gmtime([secs])：和localtime()方法类似，gmtime()方法是将一个时间戳转换为UTC时区（0时区）的struct_time。
- time.time()：返回当前时间的时间戳。
- time.mktime(t)：将一个struct_time转化为时间戳。
- time.sleep(secs)：线程推迟指定的时间运行。单位为秒。
- time.asctime([t])：把一个表示时间的元组或者struct_time表示为这种形式：'Sun Oct 1 12:04:38 2017'。如果没有参数，将会将time.localtime()作为参数传入。
- time.ctime([secs])：把一个时间戳（按秒计算的浮点数）转化为time.asctime()的形式。如果参数未给或者为None的时候，将会默认time.time()为参数。它的作用相当于time.asctime(time.localtime(secs))。
- time.strftime(format[, t])：把一个代表时间的元组或者struct_time（如由time.localtime()和time.gmtime()返回）转化为格式化的时间字符串。如果t未指定，将传入time.localtime()。
  - 举例：time.strftime("%Y-%m-%d %X", time.localtime()) #输出'2017-10-01 12:14:23'
- time.strptime(string[, format])：把一个格式化时间字符串转化为struct_time。实际上它和strftime()是逆操作。
  - 举例：time.strptime('2017-10-3 17:54',"%Y-%m-%d %H:%M") #输出 time.struct_time(tm_year=2017, tm_mon=10, tm_mday=3, tm_hour=17, tm_min=54, tm_sec=0, tm_wday=1, tm_yday=276, tm_isdst=-1)

|format的格式|                             意义                             |
|:--:|:--:|
|%a      |本地(local)简化星期名称|
|%A      |本地完整星期名称|
|%b      |本地简化月份名称|
|%B      |本地完整月份名称|
|%c      |本地相应的日期和时间表示|
|%d     | 一个月中的第几天(01-31)|
|%H     | 一天中的第几个小时(24小时制，00-23)|
|%l      |一天中的第几个小时(12小时制，01-12)|
|%j      |一年中的第几天(01-366)|
|%m     | 月份(01-12)|
|%M     | 分钟数(00-59)|
|%p      |本地am或者pm的相应符|
|%S      |秒(01-61)|
|%U      |一年中的星期数(00-53,星期天是一个星期的开始,第一个星期天之前的所有天数都放在第０周)|
|%w      |一个星期中的第几天(0-6,0是星期天)|
|%W      |和%U基本相同，不同的是%W以星期一为一个星期的开始|
|%x      |本地相应日期|
|%X     | 本地相应时间|
|%y      |去掉世纪的年份(00-99)|
|%Y      |完整的年份       |
|%z     | 用+HHMM或者-HHMM表示距离格林威治的时区偏移(H代表十进制的小时数，M代表十进制的分钟数)|
|%Z     | 时区的名字(如果不存在为空字符)|
|%%  |    %号本身|
|%p |只有与%I配合使用才有效果|


**当使用strptime()函数时，只有当在这年中的周数和天数被确定的时候%U和%W才会被计算**

## datetime模块

相比于time模块，datetime模块的接口则更直观、更容易调用

### **datetime模块定义的类**

- datetime.date：表示日期的类。常用的属性有year, month, day；
- datetime.time：表示时间的类。常用的属性有hour, minute, second, microsecond；
- datetime.datetime：表示日期时间。
- datetime.timedelta：表示时间间隔，即两个时间点之间的长度。
- datetime.tzinfo：与时区有关的相关信息。（这里不详细充分讨论该类，感兴趣的童鞋可以参考python手册）

### **需要记住的方法：**

1. d=datetime.datetime.now() 返回当前的datetime日期类型

```python
d.timestamp(),d.today(), d.year,d.timetuple()等方法可以调用
```

2.datetime.date.fromtimestamp(322222) 把一个时间戳转为datetime日期类型

3.时间运算

```python
>>> datetime.datetime.now()

datetime.datetime(2017, 10, 1, 12, 53, 11, 821218)

>>> datetime.datetime.now() + datetime.timedelta(4) #当前时间 +4天

datetime.datetime(2017, 10, 5, 12, 53, 35, 276589)

>>> datetime.datetime.now() + datetime.timedelta(hours=4) #当前时间+4小时

datetime.datetime(2017, 10, 1, 16, 53, 42, 876275)
```

4.时间替换

```python
>>> d.replace(year=2999,month=11,day=30)

datetime.date(2999, 11, 30)
```
