### **1.** **Web基础**

#### **1.1.** **概念**

* Java Servlet技术简称Servlet技术，是Java开发Web应用的底层技术

* Servlet在创建后（被第一次请求时）就一直保存在内存中

* Spring MVC 又叫 Spring Web MVC,是Spring 框架的一个模块，用于快速开发Web应用

*  MVC代表 Model-View-Controller

#### **1.2.** **Servlet/JSP应用架构**

* Servlet是一个Java程序，一个Servlet应用有一个或者多个Servlet程序

*  JSP页面会被转换编译成Servlet程序

*  Servlet应用无法独立运行，必须运行在Servlet容器中，如 Tomcat

* Web服务器和Web客户端之间通过HTTP协议通信

* Servlet/JSP容器是一个可以同时处理Servlet和静态内容的Web容器

#### **1.3.** **HTTP**

* 通过URL连接或者地址来访问资源，如http://google.com/index.html

* 不同的域名可以指向同一个IP

*  一个HTTP请求包含三个部分

1) 方法-URI-协议/版本

2) 请求头信息

3) 请求正文

4) 请求方法包括7种类型：GET、POST、HEAD、OPTIONS、PUT、DELETE、TRACE

* URI定义了一个互联网资源，通常解析为服务器根目录的相对路径，通常用/符号打头

* URL是URI的一个具体类型

* HTP响应包含三个部分

1) 协议-状态码-描述

2) 响应头信息

3) 响应正文

* HTTP响应报文的正文是HTML文档

### **2. SAX解析xml文档**

#### **2.1. 解析思想**

* SAX（Simple API for XML） 

读文档时候就能操作

*  解析原理

通过读取器读取XML文档，当读取到文档的某一部分时，（文档的开始，元素的开始，元素的结束，文档的结束）， 都会调用事件处理器的对应方法，读取的数据，以形参的方式传递给相应的方法。

#### **2.2. SAX解析优缺点**

1) 优点

Ø 不需要像DOM解析方式将整个文档加载进内存中，减小系统内存消耗。

Ø 当解析到某个标签内容时可以自动触发相应的方法进行处理，效率高

2) 缺点

Ø 只能执行查找动作，不能增删改

Ø 每次解析只能处理一次，要想再次处理需要再次解析

#### 2.3. 示例代码

例如解析如下XML文档：city.xml
（注意文档在工程中的位置，直接放在工程根目录下，否则在查找文档时候需要加载绝对路径）

```xml
1.<?xml version="1.0" encoding="utf-8"?>  
2.<county>   
3.  <province name="江苏">   
4.    <capital>南京</capital>    
5.    <city>苏州</city>   
6.  </province>    
7.  <province name="山东">   
8.    <capital>济南</capital>    
9.    <city>青岛</city>   
10.  </province>   
11.</county>  
```

##### 2.3.1 解析步骤

解析分为以下5个步骤：  

1) 获取解析工厂

2) 获取解析器

3) 获取读取器

4) 注册事件处理器

5) 开始解析

```java
1.public class SaxDemo1 {  
2.    public static void main(String[] args) throws Exception {   
3.        // 获取解析工厂  
4.        SAXParserFactory factory = SAXParserFactory.newInstance();   
5.        // 获取解析器  
6.        SAXParser parser = factory.newSAXParser();   
7.        // 获取读取器  
8.        XMLReader reader = parser.getXMLReader();  
9.        // 注册事件处理器  
10.        reader.setContentHandler(new MyHandler());   
11.        // 开始解析  
12.        reader.parse("city.xml");  
13.    }  
14.}  
15.  
16.class MyHandler implements org.xml.sax.ContentHandler {  
17.    @Override  
18.    public void startDocument() throws SAXException {  
19.        System.out.println("文档解析开始了");  
20.    }  
21.  
22.    @Override  
23.    public void startElement(String uri, String localName, String qName, Attributes atts) throws SAXException {  
24.        System.out.println("解析到标签" + qName);  
25.    }  
26.  
27.    @Override     
28.    public void characters(char[] ch, int start, int length) throws SAXException {  
29.        // 打印   
30.        System.out.println(new String(ch,start,length));   
31.    }  
32.      
33.    @Override  
34.    public void endElement(String uri, String localName, String qName) throws SAXException {  
35.        System.out.println("解析到结束标签" + qName);  
36.    }  
37.  
38.    @Override  
39.    public void endDocument() throws SAXException {  
40.        System.out.println("文档解析结束");  
41.    }  
42.  
43.    @Override  
44.    public void setDocumentLocator(Locator locator) {  
45.    }  
46.  
47.    @Override  
48.    public void startPrefixMapping(String prefix, String uri) throws SAXException {  
49.    }  
50.  
51.    @Override     
52.    public void endPrefixMapping(String prefix) throws SAXException {  
53.        // TODO Auto-generated method stub   
54.    }  
55.      
56.    @Override     
57.    public void ignorableWhitespace(char[] ch, int start, int length)   throws SAXException {   
58.        // TODO  
59.        // Auto-generated  
60.        // method  
61.        // stub  
62.        //   
63.    }  
64.  
65.    @Override     
66.    public void processingInstruction(String target, String data)   throws SAXException {   
67.        // TODO  
68.        // Auto-generated  
69.        // method  
70.        // stub  
71.        //   
72.    }  
73.  
74.    @Override  
75.    public void skippedEntity(String name) throws SAXException {  
76.        // TODO Auto-generated method stub  
77.    }  
78.}  
```

此处简单地打印出标签和内容，但是可以看到继承ContentHandler要复写很多方法，较为繁琐

那有木有好的方法呢？当然是有的，就是适配器设计模式。

##### 2.3.2 适配器模式

何为适配器设计模式？简单的说就是这个类已经实现了ContentHandler接口，我们使用这个类时不需要复写所有的方法，只将自己需要的方法复写即可。

```java
1.//适配器设计模式  
2.class MyHandler  extends DefaultHandler{      
3.    //定义一字符串，用于保存标签名称     
4.    private String eleName = null;   
5.      
6.    //如果只想打印第二个符合条件的标签主体内容，可以加上计数器   
7.    private int    iNum = 0;       
8.      
9.    @Override     
10.    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {   
11.        //将开始标签名称保存           
12.        eleName = qName;      
13.    }     
14.      
15.    @Override     
16.    public void characters(char[] ch, int start, int length) throws SAXException {   
17.        //判断是哪一个标签，并采取相应的动作   
18.        //  if("capital".equals(eleName))   
19.        //  System.out.println(new String(ch,start,length));      
20.          
21.        //如果只想打印第二个符合条件的标签主体内容，可以加上计数器        
22.        if("capital".equals(eleName) && iNum++ == 1)              
23.            System.out.println(new String(ch,start,length));      
24.    }     
25.      
26.    @Override     
27.    public void endElement(String uri, String localName, String qName)  throws SAXException {     
28.        //结束标签后要将字符串初始化，否则会打印结束标签到下一开始标签前的空格内容        
29.        eleName = null;       
30.    }     
31.    
32.}  
```

### 3. **DOM解析xml文档**

#### 3.1. **基础概念**

**1) DOM模型 **(document object model)

DOM解析器在解析XML文档时，会把文档中的所有元素，按照其出现的层次关系，解析成一个个Node对象(节点)，并以树的结构组织起来，存储到内存中。

**2) JAXP中的文档对象**

Document：文档

Element：元素

Attr：属性

CharacterData：标签体

**3) Node节点**

Node是一个接口，代表文档树中的单个节点，其他文档类都是Node接口的实现

Node接口上提供了获取父节点、获取子节点的方法，由此可以遍历文档树。

Node接口定义了增删改查节点方法由此可以修改文档树。

#### 3.2. **DOM解析的优缺点**

1) 优点

> 十分便于增删改查的操作

> 只需一次解析拿到dom对象后可以重复使用，减少解析次数

2) 缺点

> 解析过程慢，需要将整个文档解析完成后才能进行操作

> 需要将整个文档载入内存中，消耗内存 ，尤其当文档较大时候

#### 3.3. **示例代码**

xml文档:

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.  
3.<jiyou>   
4.  <country name="America" 政治体制="资产阶级民主制" president="奥巴马">   
5.    <weapon>核弹</weapon>    
6.    <advantage>妹子多</advantage>    
7.    <land>就是大</land>  
8.  </country>    
9.  <country name="Russia">   
10.    <weapon>宙斯盾</weapon>    
11.    <advantage>哥有钱</advantage>   
12.  </country>   
</jiyou>  
```

Java代码：

```java
1.package com.example.xmlTest;  
2.  
3.import java.io.FileInputStream;  
4.import java.io.FileOutputStream;  
5.import java.io.FileWriter;  
6.import java.text.Format;  
7.import java.util.List;  
8.  
9.import javax.swing.ListModel;  
10.  
11.import org.dom4j.Attribute;  
12.import org.dom4j.Document;  
13.import org.dom4j.DocumentHelper;  
14.import org.dom4j.Element;  
15.import org.dom4j.io.OutputFormat;  
16.import org.dom4j.io.SAXReader;  
17.import org.dom4j.io.XMLWriter;  
18.import org.junit.Test;  
19.  
20./* 
21. * 利用dom方式解析xml文档 
22. */  
23.public class XmlDom {  
24.  
25.    @Test  
26.    public   void test() throws Exception{  
27.        //获取解析器  
28.        SAXReader reader = new SAXReader();  
29.        //读取xml文档获取其整个dom对象  
30.        Document document = reader.read("war.xml");  
31.        //获取根元素  
32.        Element root = document.getRootElement();  
33.        //获取标签体内容  
34.        String name = root.element("country").element("weapon").getText();  
35.        System.out.println(name);       
36.    }  
37.      
38.    /* 
39.     * 查找 
40.     */  
41.    @Test  
42.    public  void  find() throws Exception {  
43.        SAXReader reader = new SAXReader();  
44.        Document document = reader.read("war.xml");  
45.          
46.        Element root = document.getRootElement();  
47.        List<Element>  list =  root.elements();  
48.        String advString  = list.get(1).element("advantage").getText();  
49.        System.out.println(advString);  
50.    }  
51.      
52.    /* 
53.     * 增加标签元素 
54.     */  
55.    @Test  
56.    public void add() throws Exception{  
57.        SAXReader   reader = new SAXReader();  
58.        Document    document = reader.read("war.xml");  
59.        Element   root = document.getRootElement();  
60.          
61.        //利用DocumentHelper创建新的标签元素  
62.        Element  land = DocumentHelper.createElement("land");  
63.        land.setText("就是大");  
64.        //将新的元素添加到对应结节点中  
65.        Element  country = root.element("country");  
66.        country.add(land);  
67.          
68.        //上面的修改只是改变的加载在内存中的dom树，还需要通过文件写如将更改应用到实际文档  
69.//      FileWriter writer = new FileWriter("war.xml");  
70.//      document.write(writer);  
71.//      writer.flush();  
72.//      writer.close();  
73.          
74.        XMLWriter  writer =  new XMLWriter(new FileOutputStream("war.xml"),OutputFormat.createPrettyPrint());  
75.        writer.write(document);  
76.        writer.close();  
77.    }  
78.      
79.    /* 
80.     * 删除标签元素 
81.     */  
82.    @Test  
83.    public  void delete() throws Exception{  
84.        SAXReader   reader = new SAXReader();  
85.        Document    document = reader.read("war.xml");  
86.        Element   root = document.getRootElement();  
87.          
88.        @SuppressWarnings("unchecked")  
89.        List<Element>  list = root.elements();  
90.        Element  adv  = list.get(1).element("advantage");  
91.        adv.getParent().remove(adv);  
92.          
93.        XMLWriter  writer =  new XMLWriter(new FileOutputStream("war.xml"));  
94.        writer.write(document);  
95.        writer.close();  
96.    }  
97.      
98.      
99.    /* 
100.     * 修改标签元素 
101.     */  
102.    @Test  
103.    public void modify() throws Exception{  
104.        SAXReader   reader = new SAXReader();  
105.        Document    document = reader.read("war.xml");  
106.        Element   root = document.getRootElement();  
107.          
108.        List<Element>  list = root.elements();  
109.        Element  adv  = list.get(1).element("weapon");  
110.        adv.setText("宙斯盾");  
111.          
112.        XMLWriter writer = new XMLWriter(new FileOutputStream("war.xml"));  
113.        writer.write(document);  
114.        writer.close();           
115.    }  
116.      
117.    /* 
118.     * 为标签   增删改查    属性 
119.     */  
120.    @Test  
121.    public void attribute() throws Exception{  
122.        SAXReader   reader = new SAXReader();  
123.        Document    document = reader.read("war.xml");  
124.        Element   root = document.getRootElement();  
125.        Element countryElement = root.element("country");  
126.          
127.        //增  
128.        countryElement.addAttribute("政治体制", "资产阶级民主制");  
129.          
130.        //查  
131.        String  name = countryElement.attributeValue("name");  
132.        System.out.println(name);  
133.          
134.        //删  
135.        Attribute   president  = countryElement.attribute("president");  
136.        president.getParent().remove(president);  
137.          
138.        //改  
139.        //可由上得出      
140.        XMLWriter writer = new XMLWriter(new FileOutputStream("war.xml"));  
141.        writer.write(document);  
142.        writer.close();        
143.    }  
144.  
145.}  
```

