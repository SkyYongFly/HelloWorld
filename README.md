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

### 4. Servlets

#### 4.1. Servlet API

![1491012282282](README.assets/1491012282282.png)

*  Servlet接口是核心接口，是所有Serlvet都必须直接或间接实现的一个接口

* Servlet接口定义了Servlet与Servlet容器之间的约定，总的来说就是Servlet容器会把Servlet类加载到内存中，并在Servlet实例中调用特定方法，在一个应用程序中，每个Servlet类型只能有一个实例。当用户的请求引发service方法，并给这个方法传入一个ServletRequest实例和一个ServletResponse实例。

* ServletRequest封装当前的HTTP请求，让开发者不必去解析和操作原始的HTTP数据

* ServletResponse表示当前用户的HTTP响应，它的作用是使得将响应回传给用户更容易。

* Servlet容器还为每个应用程序创建一个ServletContext实例。这个对象封装context（应用程序）的环境细节，而每个context只有一个ServletContext。

* 每个Servlet实例还有一个封装Servlet配置信息的ServletConfig。

#### 4.2. **Servlet**

Servlet定义了5个方法：

![1491012354677](README.assets/1491012354677.png)

* init 第一次请求我们编写的Serlvet时，Servlet容器调用此方法，后续不在调用，可以利用这个方法做一些初始化的工作。在调用这个方法时，Servlet容器会传递一个ServletConfig。一般会将这个ServletConfig赋给一个类级变量，以方便其他方法也可以使用这个对象。

* service 每次用户请求service时，servlet容器都会调用这个方法，我们对请求的处理就是在这里完成的。

* destroy 要销毁Servlet时，Servlet容器就会调用这个方法，它通常发生在卸载应用程序，或者关闭Servlet容器的时候，这里一般我们会写一些资源清理相关的代码

* getServletInfo 就是字面意思，返回Servlet的描述

* getServletConfig 这个方法返回由Servlet容器传给init方法的ServletConfig，上面说了，一般在init方法中将ServletConfig赋给一个类级变量，免得本方法返回null。

**TIP**:

注意Servlet线程安全性，Servlet不是线程安全的，而一个应用程序中所有的用户公用一个Servlet实例，因此不建议使用类级别的变量（只使用局部变量最好），除非是只读的或者java.utilconcurrent.atomic包中的成员。

#### 4.3.  编写Servlet应用

1) Servlet需要运行在Servlet容器中，所以需要一个Servlet容器，我们使用免费且强大的Tomcat

2) 示例程序

​	目录结构：

![1491012448108](README.assets/1491012448108.png)

Ø 应用程序中一般会有JSP、HTML、图像等其他资源，这些都应该放在应用程序的目录下面，并且经常放在子目录下，如上图，html放在html文件下，jsp放在jsp目录下。

Ø 放在应用程序目录下的任何资源，用户可以通过资源的URL直接访问（放在应用程序目录下当然要可以访问了）

Ø 如果希望某个资源可以被Servlet访问，但不能被用户访问，那就应该放在WEB-INF目录下（是不是找到该目录的作用了）。

* 程序

```java
1.package com.example.servlet;  
2.  
3.import java.io.IOException;  
4.import java.io.PrintWriter;  
5.  
6.import javax.servlet.Servlet;  
7.import javax.servlet.ServletConfig;  
8.import javax.servlet.ServletException;  
9.import javax.servlet.ServletRequest;  
10.import javax.servlet.ServletResponse;  
11.import javax.servlet.annotation.WebServlet;  
12.  
13./** 
14. * 自定义Servlet，实现Servlet接口 
15. *  
16. * @author zhuyong 
17. */  
18.@WebServlet(name="MyServlet",urlPatterns="/myServlet")  
19.public class MyServlet implements Servlet{  
20.    //Servlet配置信息对象  
21.    private ServletConfig servletConfig = null;  
22.  
23.    /** 
24.     * 初始化方法，第一次请求时调用 
25.     */  
26.    @Override  
27.    public void init(ServletConfig config) throws ServletException {  
28.        this.servletConfig = config;  
29.          
30.        System.out.println("MyServlet 被初始化了...");  
31.    }  
32.  
33.    /** 
34.     * 核心逻辑方法，每次请求都调用 
35.     */  
36.    @Override  
37.    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {  
38.        servletResponse.setCharacterEncoding("UTF-8");  
39.        servletResponse.setContentType("text/html");  
40.          
41.        PrintWriter printWriter = servletResponse.getWriter();  
42.        printWriter.write("<html><head></head><body>Hello ,This Is MyServlet!</body></html>");  
43.          
44.        printWriter.close();  
45.    }  
46.      
47.    /** 
48.     * 销毁方法 
49.     */  
50.    @Override  
51.    public void destroy() {  
52.        System.out.println("MyServlt被销毁了...");  
53.    }  
54.  
55.    /** 
56.     * 获取ServletConfig方法 
57.     */  
58.    @Override  
59.    public ServletConfig getServletConfig() {  
60.        return servletConfig;  
61.    }  
62.  
63.    /** 
64.     * 获取Servlet的基本描述 
65.     */  
66.    @Override  
67.    public String getServletInfo() {  
68.        return "This is MyServlet!";  
69.    }  
70.}  
```

Ø 这里我们定义了一个MyServlet，实现了Servlet接口，在初始化方法init()中简单的输出提示信息。

Ø 在核心的service()中，我们通过输出对象ServletResponse输出了一段HTML文本，这段文本被浏览器接收到的话，将被解析展示成对应的网页内容，其实我们定义了返回内容格式：servletResponse.setContentType("text/html")。

Ø 需要注意的是MyServlet上面的注解：@WebServlet(name="MyServlet",urlPatterns="/myServlet") 

@WebServlet 定义了MyServlet要被访问的URL名称，即我们在访问的时候可以通过 /myServlet 来请求到这个Servlet

* 部署访问

将应用部署到Tomcat中，启动Tomcat，然后输入地址：

*http://localhost:8080/Servlet_1/myServlet*

我们的浏览器输出如下内容：

![1491012523472](README.assets/1491012523472.png)

* 我们注意到当第一次请求这个Servlet的时候，后台输出：

![1491012544406](README.assets/1491012544406.png)

而第二次以及之后的请求都不再输出了，说明只有在第一次请求Servlet的时候才调用init()方法。

#### **4.4.  **ServletRequest

* 对于每一个HTTP请求，servlet容器会创建一个封装了HTTP请求的ServletRequest实例传递给servlet的service方法

* 以下为ServletRequest大部分方法：

![1491012660535](README.assets/1491012660535.png)

* ServletRequest中最常用的方法就是：getParameter()，用于返回HTML表单域的值，或者HTTP请求URL查询字符串的值，例如：

  *http**://example/context/servlet?id=123*

​	那我们就可以用如下方法获取请求中id对应的值：

​	String id = request.getParameter(“id);

#### 4.5. ServletResponse

* ServletResponse则表示一个Servlet响应，其影藏了将响应发给浏览器的复杂性。通过ServletRequest的方法你可以获取一些请求相关的参数，而ServletResponse则可以将设置一些返回参数信息，并且设置返回内容。返回内容之前一般会调用setContentType方法设置响应的内容类型，如果没有设置，大多数浏览器会默认以html的形式响应，不过为了避免出问题，我们一般都设置该项。

* 值得注意的是ServletResponse中定义的getWriter方法，它返回可以将文本传给客户端的java.io.PrintWriter。在默认的情况下，PrintWriter对象使用ISO-8859-1编码，这有可能引起乱码。

* 以下为ServletResponse大部分方法：

![1491012762025](README.assets/1491012762025.png)

#### 4.6. ServletConfig

* ServletConfig封装可以通过@WebServlet或者web.xml传给一个Servlet的配置信息，以这种方式传递的每一条信息都称做初始化信息，初始化信息就是一个个K-V键值对。

* 为了从一个Servlet内部获取某个初始参数的值，init方法中调用ServletConfig的getInitParameter方法或getInitParameterNames方法获取，除此之外，还可以通过getServletContext获取ServletContext对象。方法签名：

![1491017151773](README.assets/1491017151773.png)

* 通过WebServlet传递配置信息示例：

```java
1./** 
2. * 测试ServletConfig 
3. *  
4. * @author zhuyong 
5. */  
6.@WebServlet(name="MyServlet2",  
7.        urlPatterns="/myServlet2",  
8.        initParams={  
9.                @WebInitParam(name = "name", value="小明"),  
10.                @WebInitParam(name = "address", value="江苏省")  
11.        }  
12.)  
13.public class MyServlet2 implements Servlet{  
14.    private ServletConfig servletConfig = null;  
15.  
16.     
17.    @Override  
18.    public void service(ServletRequest servletRequest, ServletResponse servletResponse) 
19.                 throws ServletException, IOException {  
20.        //获取初始化参数  
21.        ServletConfig servletConfig = getServletConfig();  
22.        String name = servletConfig.getInitParameter("name");  
23.        String address = servletConfig.getInitParameter("address");  
24.          
25.        //设置输出属性  
26.        servletResponse.setCharacterEncoding("UTF-8");  
27.        servletResponse.setContentType("text/html");  
28.          
29.        PrintWriter printWriter = servletResponse.getWriter();  
30.        printWriter.write("<html><head></head><body>姓名：" + name + "</br>地址：" + address + "</body></html>");  
31.          
32.        printWriter.close();  
33.    }  
34.     
35.   //*****其他方法和上面的一致，不再给出*********************** 
  } 
```

![1491017181512](README.assets/1491017181512.png)

#### 4.7. ServletContext

* ServletContext是代表了Servlet应用程序。每个Web应用程序只有一个context。

* 在分布式环境中，一个应用程序同时部署到多个容器中，并且每台Java虚拟机都有一个ServletContext对象。

* 有了ServletContext对象后，就可以共享能通过应用程序的所有资源访问的信息，促进Web对象的动态注册，共享的信息通过一个内部Map中的对象保存在ServiceContext中来实现。

* ServletContext中的属性的生命周期从创建开始，到服务器关闭而结束

* 注意事项:因为存在ServletContext中的数据会长时间的保存在服务器，会占用内存，因此我们建议不要想serveltContext中添加过大的数据

* 保存在ServletContext中的对象称作属性。操作属性的方法：

![1491029999797](README.assets/1491029999797.png)

* 因为ServletContext代表整个Servlet应用程序，所以在不同的servlet中设置的属性可以在其它servlet中获取。示例程序：

​	MyServletContext1:

```java
1.@Override  
2.    public void service(ServletRequest request, ServletResponse response)   
3.            throws ServletException, IOException {  
4.        //获取ServletContext  
5.        ServletContext context = this.servletConfig.getServletContext();  
6.          
7.        //设置属性值  
8.        context.setAttribute("name", "小红");  
9.        System.out.println("MyServletContext1成功设置属性！");  
10.    }  
```

MyServletContext2:

```java
1.@Override  
2.    public void service(ServletRequest request, ServletResponse response)   
3.            throws ServletException, IOException {  
4.        //获取ServletContext  
5.        ServletContext context = this.servletConfig.getServletContext();  
6.          
7.        //获取context1中设置的属性值  
8.        String name = (String) context.getAttribute("name");  
9.          
10.        System.out.println("获取设置的属性值name:" + name);  
11.    }  
```

当我们先访问MyServletContext1,设置了属性：

![1491030052779](README.assets/1491030052779.png)

当我们再访问MyServletContext2，获取属性：

![1491030065979](README.assets/1491030065979.png)

#### 4.8. GenericServlet

* 前面编写的Servlet应用中通过实现Servlet接口来编写Servlet，但是我们每次都必须为Servlet中的所有方法都提供实现，还需要将ServletConfig对象保存到一个类级别的变量中，GenericServlet抽象类就是为了为我们省略一些模板代码，实现了Servlet和ServletConfig，完成了一下几个工作：

* 将init方法中的ServletConfig赋给一个类级变量，使的可以通过getServletConfig来获取。

```java
public void init(ServletConfig config) throws ServletException {
     this.config = config;
     this.init();
}
```

* 同时为避免覆盖init方法后在子类中必须调用super.init(servletConfig)，GenericServlet还提供了一个不带参数的init方法，当ServletConfig赋值完成就会被第带参数的init方法调用。这样就可以通过覆盖不带参数的init方法编写初始化代码，而ServletConfig实例依然得以保存（这难道不是适配器模式吗？）

  Ø 为Servlet接口中的所有方法提供默认实现。

  Ø 提供方法来包装ServletConfig中的方法。

* 我们可以通过继承GenericServlet来自定义Servlet(这里不再给出示例程序。。。)

#### 4.9. **HTTPServlet**

* 在编写Servlet应用程序时，大多数都要用到HTTP，也就是说可以利用HTTP提供的特性，javax.servlet.http包含了编写Servlet应用程序的类和接口，其中很多覆盖了javax.servlet中的类型，我们自己在编写应用时大多时候也是继承的HttpServlet，以下为其中的重要成员:

![1491096982295](README.assets/1491096982295.png)

* 从上图看，HttpServlet继承了GenericServlet，HttpServletRequest/Response继承了覆盖了ServletRequest/Response，成为了新的Servlet请求和响应的代表。在HttpServlet中覆盖了GenericServlet的service方法，并用新的Servlet请求和响应代表作为参数添加了一个service方法：

```java
1.//覆盖GenereicServlet中的service  
2.public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException{
3.           HttpServletRequest  request;  
4.           HttpServletResponse response;         
5.           if (!(req instanceof HttpServletRequest &&  
6.                   res instanceof HttpServletResponse)) {  
7.               throw new ServletException("non-HTTP request or response");  
8.           }  
9.           request = (HttpServletRequest) req;  
10.           response = (HttpServletResponse) res;  
11.           service(request, response);  
12.       }  
13.}  
14.  
15.//新service方法签名  
16.protected void service(HttpServletRequest req, HttpServletResponse resp)  
```

* 原始的service方法将请求和响应进行向下转换，分别为HttpServletRequest和HttpServletResponse，并调用新的service方法。看了下2.5版本中的实现，发现没有加以上代码是中的instanceof判断，恩，看来2.5中直接向下转型确实暴力了点，考虑容器不一定总是把请求当做HTTP请求，这样做看起来稳妥了些。新的service方法会查寻HTTP请求的方法从而调用do{Method}来处理请求。

* 总之HttpServlet中有两项特性是GenericServlet中没有的：

> 不覆盖service方法，而是覆盖doGet、doPost等。

> 用HttpServletRequest\Response 替代ServletRequest\Response

* HttpServletRequest，HttpServletResponse由于带有了HTTP的特性，因此除了ServletRequest，ServletResponse中的方法之外还增加了几个可以获取HTTP特性信息的方法。

```java
//获取context的请求URI部分
java.lang.String getContextPath()
//获取Cookie对象数组
Cookie [] getCookies()

//返回指定HTTP标头的值
java.lang.String getHeader(java.lang.String name)
//返回发出这条请求的HTTP方法的名称
java.lang.String getMethod()
//返回请求URL中的查询字符串
java.lang.String getQueryString()
//获取session对象，没找到就新创建
HttpSession getSession()
//返回与这个请求相关的session对象，如果没有，并且create参数为true，创建新的session对象
```

```java
//响应对象添加cookie
void addCookie(Cookie cookie)
//添加标头
void addheader(String name,String value)
//重定向
void sendRedirect(String location)
```

* 示例程序

```java
1.package com.example.servlet;  
2.  
3.import java.io.IOException;  
4.import java.io.PrintWriter;  
5.  
6.import javax.servlet.ServletException;  
7.import javax.servlet.annotation.WebServlet;  
8.import javax.servlet.http.HttpServlet;  
9.import javax.servlet.http.HttpServletRequest;  
10.import javax.servlet.http.HttpServletResponse;  
11.  
12./** 
13. * 继承HttpServlet定义一个Servlet 
14. *  
15. * @author zhuyong 
16. */  
17.@WebServlet(urlPatterns="/myHttpServlet")  
18.public class MyHttpServlet extends HttpServlet{  
19.    private static final long serialVersionUID = 927677281600473562L;  
20.  
21.    @Override  
22.    public void init() throws ServletException {  
23.        System.out.println("MyHttpServlet被初始化了...");  
24.    }  
25.      
26.    @Override  
27.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)   
28.            throws ServletException, IOException {  
29.        resp.setContentType("text/html");  
30.        resp.setCharacterEncoding("UTF-8");  
31.          
32.        PrintWriter writer = resp.getWriter();  
33.        writer.write("<html><head></head><body>Hello ,This Is MyHttpServlet!</br>"  
34.                + "Impossible Is Nothing!</body></html>");  
35.          
36.        writer.close();  
37.    }  
38.}  
```

![1491097104847](README.assets/1491097104847.png)

#### 4.10.  部署描述符

前面我们设置servlet访问的URL地址或者初始化参数都是用的@WebServlet注解的方式，其实我们还可以用另外一种方式：部署描述符——即配置在应用中的web.xml文件。

部署描述符好处：

① 可以使用@WebServlet中没有的配置元素，例如  load-on-startup

② 需要修改配置值的话，如Servlet的路径，部署描述符不需要重新编译Servlet类

③ 可以将一个初始化参数传递给Servlet，，而不需要重新编译Servlet类

④ 部署描述符中的配置会覆盖Servlet标注中定义的值

示例程序：

1)  应用结构

![1491119190847](README.assets/1491119190847.png)

2) Web.xml

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">  
3.  <display-name>Servlet_3</display-name>  
4.    
5.  <!-- 默认首页 -->  
6.  <welcome-file-list>  
7.    <welcome-file>index.html</welcome-file>  
8.  </welcome-file-list>  
9.    
10.  <!-- 定义应用级别的初始化参数 -->  
11.  <context-param>  
12.    <param-name>appname</param-name>  
13.    <param-value>PersonInfo</param-value>  
14.  </context-param>  
15.    
16.  <!-- 定义servlet描述符信息 -->  
17.  <servlet>  
18.    <servlet-name>PersonInfo</servlet-name>  
19.    <servlet-class>com.example.servlet.PersonInfoServlet</servlet-class>  
20.      
21.    <!-- 定义servlet内的初始化参数，必须定义定义在 load-on-startup 前面 -->  
22.    <init-param>  
23.        <param-name>name</param-name>  
24.        <param-value>小明</param-value>  
25.    </init-param>  
26.      
27.    <init-param>  
28.        <param-name>address</param-name>  
29.        <param-value>淮安</param-value>  
30.    </init-param>  
31.      
32.    <!-- 定义servlet在应用启动的时候就加载 -->  
33.    <load-on-startup>5</load-on-startup>  
34.      
35.  </servlet>  
36.    
37.  <servlet-mapping>  
38.    <servlet-name>PersonInfo</servlet-name>  
39.    <url-pattern>/personInfo</url-pattern>  
40.  </servlet-mapping>  
41.</web-app>  
```

PersonInfoServlet:

```java
1.package com.example.servlet;  
2.  
3.import java.io.IOException;  
4.import java.util.Enumeration;  
5.  
6.import javax.servlet.ServletConfig;  
7.import javax.servlet.ServletException;  
8.import javax.servlet.http.HttpServlet;  
9.import javax.servlet.http.HttpServletRequest;  
10.import javax.servlet.http.HttpServletResponse;  
11.  
12./** 
13. * 示例程序，获取用户信息的Servlet 
14. *  
15. * @author zhuyong 
16. */  
17.public class PersonInfoServlet extends HttpServlet {  
18.    private static final long serialVersionUID = -7442227301433035924L;  
19.      
20.    @Override  
21.    public void init() throws ServletException {  
22.        System.out.println("PersonInfoServlet初始化了...");  
23.    }  
24.  
25.    @Override  
26.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)   
27.            throws ServletException, IOException {  
28.        //获取URL请求地址中携带的参数  
29.        String urlPara = req.getParameter("id");  
30.        System.out.println("获取到URL请求参数id的值：" + urlPara + "\n");  
31.          
32.        //获取部署描述符中的应用级别初始参数  
33.        String appname = this.getServletContext().getInitParameter("appname");  
34.        System.out.println("获取到应用级别初始化参数 appname的值：" + appname + "\n");  
35.          
36.        //获取配置在servlet里的初始化参数  
37.        ServletConfig config = this.getServletConfig();  
38.        Enumeration<String> initParaNames =  config.getInitParameterNames();  
39.        while (initParaNames.hasMoreElements()) {  
40.            //获取参数名称  
41.            String initPara = (String) initParaNames.nextElement();  
42.            //根据名称获取值  
43.            String initParaValue = config.getInitParameter(initPara);  
44.              
45.            System.out.println("获取到初始化参数，名称：" + initPara + "，值：" + initParaValue);  
46.        }  
47.    }  
48.}  
```

3) 输出结果

![1491119243689](README.assets/1491119243689.png)

后台打印信息：

![1491119265943](README.assets/1491119265943.png)

我们可以看到在应用启动的时候打印了我们在init方法中的设置的信息，说明servlet确实被设置到应用启动的时候加载。然后我们请求这个servlet，获取打印了不同域的初始化参数信息。

**Servlet级别的信息保存在ServletConfig 中**

**整个应用级别的信息保存在ServletContext中**

### 5. Servlet应用

#### 5.1.  **基础概念**

Servlet: 由SUN公司提供的一种动态web资源开发技术.本质上就是一段java小程序.可以将Servlet加入到Servlet容器中运行.

#### 5.2. 步骤

主要分为两步：

1) 写一个java程序实现Servlet接口（此处直接继承了默认实现类GenericServlet，因为如果直接实现Servlet接口需要复写其中所有的方法）

2) 将编译好的带包的.class放到WEB-INF/classes下以外，还要配置web应用的 web.xml注册Servlet

#### 5.3. **示例步骤**

##### 5.3.1. 编写FirstServlet.java程序

为了对相关步骤结构由深入的认识，当然用记事本写啦  

```java
1.package  com.example.servlet;  
2.  
3.import javax.servlet.*;  
4.import java.io.*;  
5.  
6.public class FirstServlet  extends  GenericServlet{  
7.  
8.    public void service(ServletRequest  req,ServletResponse  res) 
9.                                               throws IOException,ServletException {  
10.      
11.        res.getWriter().write("Hello ,this is my first servlet !");  
12.    }  
13.}   
```

##### 5.3.2.  编译FirstServlet.java

打开 命令提示符窗口，切换到文件所在目录，输入javac  FirstServlet.java  ,但是显示错误

![1491307801383](README.assets/1491307801383.png)

仔细一看是缺少javax.servlet这个程序包，想一想，我们编译用的是jdk默认的编译环境，并没有这个包，所以需要将相关的包导入编译路径查看现有的编译路径 set classpath

![1491307820304](README.assets/1491307820304.png)

如何将所需要的包加进来呢？

找到Tomcat安装目录下的lib/ servlet-api.jar包，复制其路径

set classpath=%classpath%; (包的详细路径)

![1491307856414](README.assets/1491307856414.png)

这个是临时配置，下次重新启动会失效。再次编译

![1491307873057](README.assets/1491307873057.png)

会看到在目录下生成.class文件，但是我们要直接生成包路径结构，以便于后面配置web应用

***javac  -d  .  FirstServlet.java***

表示在当前目录下生成

![1491307896528](README.assets/1491307896528.png)

可以在目录下看到生成的包名路径文件夹，结构为

***com/example/servlet/FirstServlet.class***

##### 5.3.3.  在Tomcat中配置web应用

在 Tomcat安装目录下的webapps下新建文件夹servlet

我们知道web应用目录为

**servlet  |**

  			**|------WEB-INF** 

  						**|----classes   此处将上面的com文件夹拷贝进来** 

 						 **|----lib      所需依赖库，默认使用Tomcat的，可为空**

 						 **|----web.xml   配置文件**

关键在于配置web.xml文件:

```java
1.<?xml version="1.0" encoding="ISO-8859-1"?>    
2.<web-app xmlns="http://java.sun.com/xml/ns/javaee"    
3.   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
4.   xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"    
5.   version="2.5">     
6.    
7.    <servlet>    
8.        <servlet-name>FirstServlet</servlet-name>    
9.        <servlet-class>com.example.servlet.FirstServlet</servlet-class>    
10.    </servlet>    
11.    <servlet-mapping>    
12.        <servlet-name>FirstServlet</servlet-name>    
13.        <url-pattern>/FirstServlet</url-pattern>    
14.    </servlet-mapping>    
15.    
16.</web-app>    
```

开头不会写没关系直接到其他web应用中找到复制一下

下面两个标签配置:

<servlet-name>起一个名称，上下两个要相同

<servlet-class>中是.class具体路径，也就相当于 包名.（文件.class名）

<url-pattern>里面是映射名称，即在浏览器中输入访问的名称，可以随便起

##### 5.3.4. 浏览器访问

输入http://localhost:8080/servlet/FirstServlet

( [http://本地](Http://本地)端口/(web应用名)/映射名称 )

![1491391894263](README.assets/1491391894263.png)

