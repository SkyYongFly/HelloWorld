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

### 6. ServletConfig

#### 6.1. 基础概念

代表当前Servlet在web.xml中的配置信息，可以在web.xml中<servlet>中配置

```xml
1.<servlet>  
2.        <servlet-name>Demo5Servlet</servlet-name>  
3.        <servlet-class>cn.itheima.Demo5Servlet</servlet-class>  
4.        <init-param>  
5.            <param-name>data1</param-name>  
6.            <param-value>value1</param-value>  
7.        </init-param>  
8.  </servlet>  
```

#### 6.3. **相关方法**

> String getServletName()：获取当前Servlet在web.xml中配置的名字

> String getInitParameter(String name)：获取当前Servlet指定名称的初始化参数的值

> Enumeration getInitParameterNames()：获取当前Servlet所有初始化参数的名字组成的枚举

> ServletContext getServletContext()：获取代表当前web应用的ServletContext对象

#### 6.3. 作用

不想在servlet中写死的内容可以配置到此处。

#### 6.4. 程序

作为Init的参数，可以先定义类变量，复写父类Init()方法，在  doGet() 方法中进行相关操作

```java
1.package com.example.servlet;  
2.  
3.import java.io.IOException;  
4.import java.util.Date;  
5.  
6.import javax.servlet.ServletConfig;  
7.import javax.servlet.ServletException;  
8.import javax.servlet.http.HttpServlet;  
9.import javax.servlet.http.HttpServletRequest;  
10.import javax.servlet.http.HttpServletResponse;  
11.  
12.public class ServletDemo extends HttpServlet {  
13.    private ServletConfig config = null;  
14.
15.    @Override  
16.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
17.            throws ServletException, IOException {  
18.        //对config进行相关操作  
19.    }  
20.  
21.    @Override  
22.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
23.            throws ServletException, IOException {  
24.        // TODO Auto-generated method stub  
25.        super.doPost(req, resp);  
26.    }  
27.  
28.    @Override  
29.    public void init(ServletConfig config) throws ServletException {  
30.        this.config = config;  
31.    }  
32.  
33.      
34.}  
```

但是每次需要重写方法等较为麻烦，好在HttpServlet的父类GenericServlet中封装了getServletConfig方法，可以直接调用

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
12.public class ServletDemo extends HttpServlet {  
13.  
14.    @Override  
15.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
16.            throws ServletException, IOException {  
17.        //对config进行相关操作  
18.        ServletConfig config = this.getServletConfig();  
19.          
20.        //获取Servlet在配置中的名称  
21.        String  name = config.getServletName();  
22.        System.out.println(name);  
23.          
24.        //获取当前Servlet指定名称的初始化参数的值  
25.        String  value = config.getInitParameter("demo1");  
26.        System.out.println(value);  
27.          
28.        //获取当前servlet所有初始化名称的枚举  
29.        Enumeration<String>  en = config.getInitParameterNames();  
30.        while(en.hasMoreElements()){  
31.            String nam = en.nextElement();  
32.            String valu = config.getInitParameter(nam);  
33.            System.out.println(nam+" : "+valu);  
34.        }  
35.    }  
36.  
37.    @Override  
38.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
39.            throws ServletException, IOException {  
40.        super.doPost(req, resp);  
41.    }  
42.  
43.  
}  
```

### 7. ServletContext

#### 7.1. **基础概念**

代表当前web应用对象

#### 7.2. 作用

**1) 域对象**

作为域对象使用，在不同servlet之间传递数据,作用范围是整个web应用

**2) 生命周期**

当web应用被加载进容器时创建代表整个web应用的ServletContext对象。当服务器关闭或web应用被移除出容器时，ServletContext对象跟着销毁。

**3) 域**

一个域就理解为一个框，这里面可以放置数据，一个域既然称作域，他就有一个可以被看见的范围，这个范围内都可以对这个域中的数据进行操作，那这样的对象就叫做域对象。

**4) 初始化参数**

在web.xml可以配置整个web应用的初始化参数，利用ServletContext去获得

```xml
1.<context-param>  
2.    <param-name>param1</param-name>  
3.    <param-value>pvalue1</param-value>  
4.</context-param>  
```

​	this.getServletContext().getInitParameter("param1")

​	this.getServletContext().getInitParameterNames()

**5) 在不同servlet之间进行转发**

​	this.getServletContext().getRequestDispatcher("/servlet/Demo10Servlet").forward(request, response);

​	方法执行结束，service就会返回到服务器，再有服务器去调用目标servlet，其中request会重新创建，并将之前的request的数据拷贝进去。

**6) 读取资源文件**

* 由于相对路径默认相对的是java虚拟机启动的目录，所以我们直接写相对路径将会是相对于tomcat/bin目录，所以是拿不到资源的。如果写成绝对路径，当项目发布到其他环境时，绝对路径就错了。

* 为了解决这个问题ServletContext提供了this.getServletContext().getRealPath("/1.properties")，给进一个资源的虚拟路径，将会返回该资源在当前环境下的真实路径。this.getServletContext().getResourceAsStream("/1.properties")，给一个资源的虚拟路径返回到该资源真实路径的流。

* 当在非servlet下获取资源文件时，就没有ServletContext对象用了，此时只能用类加载器。

  classLoader.getResourceAsStream("../../1.properties")

  此方法利用类加载器直接将资源加载到内存中，有更新延迟的问题，以及如果文件太大，占用内存过大。

  ​	classLoader.getResource("../1.properties").getPath()

  直接返回资源的真实路径，没有更新延迟的问题。

#### 7.3.示例代码

**1)** 作用一：**作为域对象可以在整个web应用范围共享数据**

ServletContextDemo1.java:

```java
1.public class ServletContextDemo1 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        this.getServletContext().setAttribute("xiaoming", "child");  
7.  
8.    }  
9.}  
```

ServletContextDemo2.java:

```java
1.public class ServletContextDemo2 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        String value =  (String)this.getServletContext().getAttribute("xiaoming");  
7.        System.out.println(value);  
8.    }  
9.}  
```

在览器中先访问1（http://localhost:8080/day04/servlet1），

再访问2 ，访问的url设置如下

```xml
1. <servlet>  
2.        <servlet-name>ServletDemo1</servlet-name>  
3.        <servlet-class>com.example.servlet.ServletContextDemo1</servlet-class>  
4.</servlet>  
5.<servlet>  
6.        <servlet-name>ServletDemo2</servlet-name>  
7.        <servlet-class>com.example.servlet.ServletContextDemo2</servlet-class>  
8.</servlet>  
9.<servlet-mapping>  
10.        <servlet-name>ServletDemo1</servlet-name>  
11.        <url-pattern>/servlet1</url-pattern>  
12.</servlet-mapping>  
13.<servlet-mapping>  
14.        <servlet-name>ServletDemo2</servlet-name>  
15.        <url-pattern>/servlet2</url-pattern>  
16.</servlet-mapping>  
```

可以得到输出

![1491642143755](README.assets/1491642143755.png)

**2)** **获取web应用初始化参数**

Xml:

```xml
1.<context-param>  
2.    <param-name>contextName</param-name>  
3.    <param-value>contextValue</param-value>  
4. </context-param>  
```

ServletContextDemo3.java  :

```java
1.public class ServletContextDemo3 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        String value = this.getServletContext().getInitParameter("contextName");  
7.        System.out.println(value);  
8.    }  
9.}  
```

访问3，得出：

![1491642178949](README.assets/1491642178949.png)

**3)** **实现Servlet的转发**

所请求的方法不进行处理，转而将请求转到另一个方法中进行处理

ServletContextDemo4.java  :

```java
1.public class ServletContextDemo4 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        this.getServletContext().getRequestDispatcher("/servlet5").forward(req, resp); 
7.    }  
8.      
9.}  
```

Xml:

```xml
1.<servlet>  
2.    <servlet-name>ServletDemo4</servlet-name>  
3.    <servlet-class>com.example.servlet.ServletContextDemo4</servlet-class>  
4.</servlet>  
5.<servlet-mapping>  
6.    <servlet-name>ServletDemo4</servlet-name>  
7.    <url-pattern>/servlet4</url-pattern>  
8.</servlet-mapping>  
```

将请求转移到 /servlet5 对应的方法中

```java
1.public class ServletContextDemo5 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        resp.getWriter().write("this is zhuanfa");  
7.    }  
8.  
9.}  
```

Xml:

```xml
1.</servlet>  
2.    <servlet>  
3.    <servlet-name>ServletDemo5</servlet-name>  
4.    <servlet-class>com.example.servlet.ServletContextDemo5</servlet-class>  
5.</servlet>  
6.<servlet-mapping>  
7.    <servlet-name>ServletDemo5</servlet-name>  
8.    <url-pattern>/servlet5</url-pattern>  
</servlet-mapping>     
```

浏览器访问：

![1491642267436](README.assets/1491642267436.png)

**4) 加载资源文件**

* 例如读取工程目录中的context.properties文件

  其在工程中的具体位置如下：

![1491642284711](README.assets/1491642284711.png)

内容：

*name=zhangsan*

*home=Earth*

ServletContextDemo6.java :

```
1.public class ServletContextDemo6 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        Properties prop = new Properties();  
7.        prop.load(new  FileReader(this.getServletContext().getRealPath("context.properties")));  
8.        System.out.println("name = "+prop.getProperty("name"));  
9.        System.out.println("home = "+prop.getProperty("home"));  
10.    }  
11.  
12.}  
```

​	浏览器访问，程序输出：

![1491642315534](README.assets/1491642315534.png)

* 当在非servlet下获取资源文件时，就没有ServletContext对象用了，此时只能用类加载器

  Context.properties 所在目录

![1491642329124](README.assets/1491642329124.png)

ServletContextDemo7.java  :

```
1.public class ServletContextDemo7 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        try {  
7.            new service().method1();  
8.        } catch (Exception e) {  
9.            e.printStackTrace();  
10.        }  
11.    }  
12.  
13.}  
```

method():

```
1.package com.example.servlet1;  
2.  
3.import java.io.FileReader;  
4.import java.util.Properties;  
5.  
6.public class service {  
7.    public void method1() throws Exception{  
8.        Properties prop = new Properties();  
9.        prop.load(new FileReader(service.class.getClassLoader().getResource("com/example/servlet/context.properties").getPath()));  
10.        System.out.println(prop.getProperty("name"));  
11.        System.out.println(prop.getProperty("home"));  
12.    }  
13.}  
```

输出：

![1491642372864](README.assets/1491642372864.png)

### 8. ServletResponse

#### 8.1.  基础概念

1) Resonse的继承结构：ServletResponse--HttpServletResponse

2) Response代表**响应**，于是响应消息中的 状态码、响应头、实体内容都可以由它进行操作,由此引伸出

3) 利用Response输出数据到客户端

​	**response.getOutputStream().write("中文".getBytes())输出数据**，

​	这是一个字节流，是什么字节输出什么字节，而浏览器默认用平台字节码打开服务器发送的数据，如果服务器	端使用了非平台码去输出字符的字节数据就需要明确的指定浏览器编码时所用的码表，以防止乱码问题。

​	**response.addHeader("Content-type","text/html;charset=gb2312")**

​	response.getWriter().write(“中文”);输出数据，这是一个字符流，response会将此字符进行转码操作后输出到	浏览器，这个过程默认使用ISO8859-1码表，而ISO8859-1中没有中文，于是转码过程中用?代替了中文，导致	乱码问题。可以指定response在转码过程中使用的目标码表，防止乱码。	

​	**response.setCharcterEncoding("gb2312");**

​	其实response还提供了setContentType("text/html;charset=gb2312")方法，此方法会设置content-type响应	头，通知浏览器打开的码表，同时设置response的转码用码表，从而一行代码解决乱码。

4) 利用Response 设置 content-disposition头实现文件下载

* 设置响应头content-disposition为“attachment;filename=xxx.xxx”

* 利用流将文件读取进来，再利用Response获取响应流输出

* 如果文件名为中，一定要进行URL编码，编码所用的码表一定要是utf-8

5) refresh头控制定时刷新

* 设置响应头Refresh为一个数值，指定多少秒后刷新当前页面

* 设置响应头Refresh为 3;url=/Day05/index.jsp,指定多少秒后刷新到哪个页面

* 可以用来实现注册后“注册成功，3秒后跳转到主页”的功能

* 在HTML可以利用<meta http-equiv= "" content="">标签模拟响应头的功能。

6) 利用response设置expires、Cache-Control、Pragma实现浏览器是否缓存资源，这三个头都可以实现，但是由于历史原因，不同浏览器实现不同，所以一般配合这三个头使用

* 控制浏览器不要缓存（验证码图片不缓存）设置expires为0或-1设置 Cache-Control为no-cache、Pragma为no-cache

* 控制浏览器缓存资源。即使不明确指定浏览器也会缓存资源，这种缓存没有截至日期。当在地址栏重新输入地址时会用缓存，但是当刷新或重新开浏览器访问时会重新获得资源。

  如果明确指定缓存时间，浏览器缓存是，会有一个截至日期，在截至日期到期之前，当在地址栏重新输入地址或重新开浏览器访问时都会用缓存，而当刷新时会重新获得资源。

7) Response实现请求重定向

* 古老方法：

  response.setStatus(302);

  response.addHeader("Location","URL");

* 快捷方式：

  response.sendRedirect("URL");

8) 输出验证码图片

9) Response注意的内容：

* 对于一次请求，Response的getOutputStream方法和getWriter方法是互斥，只能  调用其一，特别注意forward后也不要违反这一规则。

* 利用Response输出数据的时候，并不是直接将数据写给浏览器，而是写到了Response的缓冲区中，等到整个service方法返回后，由服务器拿出response中的信息组成HTTP响应消息返回给浏览器。

  service方法返回后，服务器会自己检查Response获取的OutputStream或者Writer是否关闭，如果没有关闭，服务器自动帮你关闭，一般情况下不要自己关闭这两个流。	

#### 8.2. 示例程序

##### 8.2.1. 输出数据

```java
1.public class response extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //resp.setHeader("Content-Type", "text/html;charset=utf-8");  
7.        //resp.getOutputStream().write("中国".getBytes("utf-8"));  
8.          
9.        //resp.setCharacterEncoding("utf-8");  
10.        //resp.setHeader("Content-Type", "text/html;charset=utf-8");  
11.        //resp.getWriter().write("江苏");  
12.          
13.         resp.setCharacterEncoding("utf-8");//可以不写这句，但是为了让看的人更清楚  
14.        resp.getWriter().write("世界");  
15.    }  
16.  
17.    @Override  
18.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
19.            throws ServletException, IOException {  
20.        doGet(req, resp);  
21.    }  
22.}  
```

##### 8.2.2. 设置回应头

例如通知浏览器以下载方式保存图片

```java
1.public class ResponseDemo1 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //浏览器直接显示图片  
7.        FileInputStream  inputStream = new FileInputStream(this.getServletContext().getRealPath("1.jpg"));  
8.        OutputStream     outputStream = resp.getOutputStream();  
9.        byte[]  bs = new byte[1024];  
10.        int i =0;  
11.        while((i=inputStream.read(bs))!=-1){  
12.            outputStream.write(bs,0,i);  
13.        }  
14.        inputStream.close();  
15.    }  
16.  
17.}  
```

```xml
1.<servlet>  
2.    <servlet-name>response</servlet-name>  
3.    <servlet-class>com.example.response.ResponseDemo1</servlet-class>  
4.</servlet>  
5.  
6.<servlet-mapping>  
7.    <servlet-name>response</servlet-name>  
8.    <url-pattern>/response</url-pattern>  
9.</servlet-mapping>      
```

![1491996857142](README.assets/1491996857142.png)

```java
1.@Override  
2.protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
3.            throws ServletException, IOException {  
4.        //让浏览器以下载的方式保存图片  
5.        //resp.setHeader("Content-Disposition", "attachment;filename="+URLEncoder.encode("1.jpg"));  
6.      
7.        //当文件名包含 中文时候，要制定编码方式  
8.        resp.setHeader("Content-Disposition", "attachment;filename="+URLEncoder.encode("啊.jpg","utf-8"));  
9.        //浏览器直接显示图片  
10.        FileInputStream  inputStream = new FileInputStream(this.getServletContext().getRealPath("1.jpg"));  
11.        OutputStream     outputStream = resp.getOutputStream();  
12.        byte[]  bs = new byte[1024];  
13.        int i =0;  
14.        while((i=inputStream.read(bs))!=-1){  
15.            outputStream.write(bs,0,i);  
16.        }  
17.        inputStream.close();  
18.          
19.  }  
```

![1491996882356](README.assets/1491996882356.png)

![1491996892232](README.assets/1491996892232.png)

##### 8.2.3. 定时刷新页面

```java
1.public class RefreshServlet extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //时钟显示  
7.        //resp.getWriter().write(new Date().toLocaleString());  
8.        //resp.setHeader("Refresh","1");  
9.          
10.        //刷新页面  
11.        resp.setContentType("text/html;charset=utf-8");  
12.        resp.getWriter().write("登录成功，三秒钟后回到主界面");  
13.        resp.setHeader("Refresh", "3;url=/ServletResponseTest/index.jsp");  
14.    }  
15.  
16.}  
```

##### 8.2.4 不缓存资源

```java
1.public class CacheServlet extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //设置头，不缓存  
7.        resp.setIntHeader("Expires",-1);  
8.        resp.setHeader("Cache-Control", "no-cache");  
9.        resp.setHeader("Pragma","no-cache");  
10.        //可以准确显示当前时间  
11.        resp.setContentType("text/html;charset=utf-8");  
12.        resp.getWriter().write("当前时间是"+new Date().toString());  
13.    }  
14.  
15.}  
```

```java
1.public class CacheServlet extends HttpServlet {  
2.    @Override  
3.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
4.            throws ServletException, IOException {        
5.        //设置浏览器一定时间期限内使用缓存的资源  
6.        resp.setDateHeader("Expires", System.currentTimeMillis()+10l*3600*24);//10天之内使用缓存的图片资源，注意数字后缀加l变成long型，防止int溢出  
7.        //浏览器直接显示图片  
8.        FileInputStream  inputStream = new FileInputStream(this.getServletContext().getRealPath("1.jpg"));  
9.        OutputStream     outputStream = resp.getOutputStream();  
10.        byte[]  bs = new byte[1024];  
11.        int i =0;  
12.        while((i=inputStream.read(bs))!=-1){  
13.            outputStream.write(bs,0,i);  
14.        }  
15.        inputStream.close();  
16.    }  
17.  
}  
```

##### 8.2.5. 实现请求重定向

```java
1.public class RedirectServlet extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //resp.setStatus(302);  
7.        //resp.setHeader("Location", "/ServletResponseTest/index.jsp");  
8.          
9.        //可以直接设置  
10.        resp.sendRedirect("/ServletResponseTest/index.jsp");  
11.    }  
12.  
13.}  
```

当在浏览器输入http://localhost/ServletResponseTest/redirect时，浏览器自动跳转到

http://localhost/ServletResponseTest/index.jsp

### 9. ServletRequest

#### 9.1. 基础概念

HttpServletRequest对象代表客户端的请求，当客户端通过HTTP协议访问服务器时，HTTP请求中的所有信息都封装在这个对象中，开发人员通过这个对象的方法，可以获得客户这些信息。

#### 9.2. 通过Request对象进行的常用操作

* 获取客户机信息

*  获取请求头信息

*  获取请求参数

*  利用请求域传递对象

#### 9.3. **实际例子**

##### 9.3.1. 获取客户机信息

```java
1.package com.example.request;  
2./* 
3. * request获取客户机信息 
4. */  
5.import java.io.IOException;  
6.  
7.import javax.servlet.ServletException;  
8.import javax.servlet.http.HttpServlet;  
9.import javax.servlet.http.HttpServletRequest;  
10.import javax.servlet.http.HttpServletResponse;  
11.  
12.public class RequestDemo1 extends HttpServlet {  
13.  
14.    @Override  
15.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
16.            throws ServletException, IOException {  
17.        //1、返回客户端发出请求完整URL  
18.        String  urlString = req.getRequestURL().toString();  
19.        System.out.println(urlString);  
20.          
21.        //2、返回请求行中资源名部分  
22.        String uriString = req.getRequestURI();  
23.        System.out.println(uriString);  
24.          
25.        //3、返回请求行中参数部分  
26.        String qString  = req.getQueryString();  
27.        System.out.println(qString);  
28.          
29.        //4、得到客户机请求方式  
30.        String  method = req.getMethod();  
31.        System.out.println(method);  
32.          
33.        //5、得到当前web应用虚拟目录名称(获取web应用名称用此方法)  
34.        String  path = req.getContextPath();  
35.        System.out.println(path);  
36.    }  
37.  
}  
```

浏览器输入 ：http://localhost/ServletRequestTest/request?name=haha

输出：

![1492234438966](README.assets/1492234438966.png)

##### 9.3.2. 获取请求头信息

```java
1.public class RequestGetHeader extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //获取请求头中 host 的内容  
7.        //String hostString = req.getHeader("Host");  
8.        //System.out.println(hostString);  
9.          
10.        //获取所有请求头  
11.        Enumeration<String>  headers = req.getHeaderNames();  
12.        while(headers.hasMoreElements()){  
13.            String nameString = (String) headers.nextElement();  
14.            String valueString = req.getHeader(nameString);  
15.            System.out.println(nameString+" : "+valueString);  
16.        }  
17.    }  
18.  
19.}  
```

![1492234471282](README.assets/1492234471282.png)

##### 9.3.3. 实践—模拟网站防盗链

* 新建一个模拟正常网站

  新建开始页面 163.html

  ![1492234555313](README.assets/1492234555313.png)

```xml
1.<!DOCTYPE html>  
2.<html>  
3.  <head>  
4.    <title>163.html</title>  
5.    <meta name="content-type" http-equiv="content-type" content="text/html; charset=UTF-8">  
6.  </head>  
7.  <body>  
8.        <h1>163重大新闻</h1>我是广告 我是广告 我是广告  我是广告  
9.        <a href="/ServletRequestTest/daolian">  时间的秘密</a>    我是广告  我是广告  
10.  </body>  
11.</html>  
```

浏览器输入 http://localhost/ServletRequestTest/163.html

显示：

![1492234574807](README.assets/1492234574807.png)

点击“时间的秘密”进入具体内容页面：

![1492234590495](README.assets/1492234590495.png)

具体实现程序

```java
1.public class Daolian extends HttpServlet {  
2.    @Override  
3.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
4.            throws ServletException, IOException {  
5.        resp.setContentType("text/html;charset=utf-8");  
6.        resp.getWriter().write("在非洲每过一分钟就会消失60秒");  
7.    }  
8.}  
```

* 创建盗版网站 [www.165.com
  ](http://www.165.com)修改hosts文件（C:\Windows\System32\drivers\etc下）

  加入  127.0.0.1   [www.165.com](http://www.165.com)  使地址指向本地

​	  在Tomcat配置文件server.xml原有 <Host> 标签下加入

​	  <Host  name="www.165.com"    appBase="f:/165"/>

 	在对应的f盘下新建165文件夹，作为缺省应用，里面新建ROOT文件夹，ROOT下新建index.html，作为165网	 址的主页

```xml
1.<h1>165独家新闻</h1><hr>  
2.我是广告  我是广告    我是广告    我是广告    我是广告    
<a   href="http://localhost/ServletRequestTest/daolian">时间的秘密</a>我是广告    我是广告 
```

浏览器输入www.165.com显示

![1492234675759](README.assets/1492234675759.png)

点击“时间的秘密”跳转到

![1492234686847](README.assets/1492234686847.png)

作为正规的163网站要防止其它盗版网站盗用其内容，可以将程序修改为

```java
1.public class Daolian extends HttpServlet {  
2.    @Override  
3.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
4.            throws ServletException, IOException {  
5.        resp.setContentType("text/html;charset=utf-8");  
6.          
7.        String refString = req.getHeader("Referer");  
8.        if(refString == null  || "".equals(refString)  || !refString.startsWith("http://localhost")){  
9.            resp.sendRedirect(req.getContextPath()+"/163.html");  
10.            return;  
11.        }  
12.        resp.getWriter().write("在非洲每过一分钟就会消失60秒");  
13.    }  
14.}  
```

即在显示相关具体内容前判断请求来源，如果不是自家网站，将其他非法请求转到自家网站主页

这样再次输入www.165.com，点击“时间的秘密”会显示163主页

![1492234713126](README.assets/1492234713126.png)

1) 获取请求参数

请求页面 request.html

```xml
1.<!DOCTYPE html>  
2.<html>  
3.  <head>  
4.    <meta http-equiv="content-type" content="text/html; charset=UTF-8">  
5.  
6.  </head>  
7.    
8.  <body>  
9.    <h1>POST提交</h1><hr>  
10.    <form action="/ServletRequestTest/RequestDemo2"  method="POST">  
11.        姓名:<input type="text"  name="username"/>  
12.        地址:<input type="text"  name="addr"/>  
13.        <input type="submit"  value="提交"/>  
14.    </form>  
15.      
16.      <h1>GET提交</h1><hr>  
17.    <form action="/ServletRequestTest/RequestDemo2"  method="GET">  
18.        姓名:<input type="text"  name="username"/>  
19.        地址:<input type="text"  name="addr"/>  
20.        <input type="submit"  value="提交"/>  
21.    </form>  
22.  </body>  
23.</html>  
```

![1492234805652](README.assets/1492234805652.png)

```java
1.public class RequestDemo2 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //获取提交的名称为“username”的参数  
7.        //对于post请求可以设置明确通知服务器以浏览器提交过来的编码方式解析，解决乱码  
8.//      req.setCharacterEncoding("utf-8");  
9.//      String name = req.getParameter("username");  
10.//      System.out.println(name);  
11.          
12.        //对于GET提交的乱码只能手动地进行编解码解决乱码问题  
13.        String name1 = req.getParameter("username");  
14.        String  name = new String(name1.getBytes("iso8859-1"),"utf-8");  
15.        System.out.println(name);  
16.      
17.//  Enumeration<String>  enumeration  = req.getParameterNames();  
18.//  while(enumeration.hasMoreElements()){  
19.//      String name = enumeration.nextElement();  
20.//      String value = req.getParameter(name);  
21.//      System.out.println(name+" = "+value);  
22.//      }  
23.    }  
24.  
25.    @Override  
26.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
27.            throws ServletException, IOException {  
28.            doGet(req, resp);  
29.    }  
30.}  
```

### 10.JavaServer Pages(JSP)

#### 10.1. 基础概念

JSP全称是Java Server Pages，它和servle技术一样，都是SUN公司定义的一种用于开发动态web资源的技术

1) **JSP本质上是一个Servlet**

2) 当一个JSP页面第一次被请求时，Servlet/JSP容器主要做如下两件事情：

* 转换JSP页面到JSP页面实现类

* 如果转换成功，Servlet/JSP容器随后编译该Servlet类，并装载和实例化该类

3) 同一JSP页面后续请求，容器会检查JSP页面是否被修改过，如果是，则该JSP页面会被重新翻译、编译和执行，如果不是，则执行已经在内存中的JSP Servlet。

4) JSP页面可以包含模板元素和语法元素，模板元素指HTML标记和文字

5) 应用程序中的每一个JSP页面都可以直接在浏览器中输入路径页面访问

#### 10.2. 简单例程

1) 工程结构

![1492777676272](README.assets/1492777676272.png)

2）date.jsp：

```html
1.<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>  
2.<%@ page import="java.util.Date" %>  
3.<%@ page import="java.text.DateFormat" %>  
4.<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">  
5.<html>  
6.<head>  
7.    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
8.    <title>显示日期JSP</title>  
9.</head>  
10.<body>  
11.    <%  
12.        DateFormat dateFormat = DateFormat.getDateInstance(DateFormat.LONG);  
13.        String s = dateFormat.format(new Date());  
14.        out.print("今天是" + s);  
15.    %>  
16.</body>  
17.</html>  
```

### 11. Cookie

#### 11.1. 基础概念

Cookie是客户端技术，程序把每个用户的数据以cookie的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的web资源时，就会带着各自的数据去。这样，web资源处理的就是用户各自的数据了

#### 11.2. 实际例程

1) 利用cookie判断用户是否访问过页面

```java
1.public class CookieDemo1 extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        resp.setContentType("text/html;charset=utf-8");  
7.          
8.        //判断之前是否访问过  
9.        Cookie[] ck = req.getCookies();  
10.        Cookie   ck2 = null;  
11.        if(ck != null){  
12.            for(Cookie  c : ck){  
13.                if("lasttime".equals(c.getName()))  
14.                    ck2 = c;  
15.            }  
16.        }  
17.        //根据访问第几次显示不同页面  
18.        if(ck2 !=null){  
19.            resp.getWriter().write("您上次访问的时间是"+new Date(Long.parseLong(ck2.getValue())));  
20.        }else {  
21.            resp.getWriter().write("您是第一次访问");  
22.        }  
23.          
24.        //生成cookie  
25.        Date date = new Date();  
26.        Cookie  cookie = new Cookie("lasttime",date.getTime()+"");  
27.        resp.addCookie(cookie);  
28.    }  
29.  
30.    @Override  
31.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
32.            throws ServletException, IOException {  
33.        doGet(req, resp);  
34.          
35.    }  
36.  
37.}  
```

![1492908939226](README.assets/1492908939226.png)

2) 利用cooie模拟图书阅读过程

定义基础类对象 ：书

```java
1.package com.example.booktest;  
2.  
3.import java.io.Serializable;  
4.  
5.public class Book implements Serializable {  
6.    private String  id;  
7.    private String  name;  
8.    private String author;  
9.    private String price;  
10.      
11.      
12.    public Book() {  
13.    }  
14.      
15.    public Book(String id,String name, String author, String price) {  
16.        super();  
17.        this.id = id;  
18.        this.name = name;  
19.        this.author = author;  
20.        this.price = price;  
21.    }  
22.    public String getId(){  
23.        return id;  
24.    }  
25.    public void setId(String id){  
26.        this.id = id;  
27.    }  
28.    public String getName() {  
29.        return name;  
30.    }  
31.    public void setName(String name) {  
32.        this.name = name;  
33.    }  
34.    public String getAuthor() {  
35.        return author;  
36.    }  
37.    public void setAuthor(String author) {  
38.        this.author = author;  
39.    }  
40.    public String getPrice() {  
41.        return price;  
42.    }  
43.    public void setPrice(String price) {  
44.        this.price = price;  
45.    }  
46.      
47.}  
```

初始化书管理集合，并且可以获取集合或单个对象

```java
1.package com.example.booktest;  
2.  
3.import java.util.LinkedHashMap;  
4.import java.util.Map;  
5.  
6.public class BookColleation {  
7.    private  static Map<String,Book>  bookMap = new LinkedHashMap<String, Book>();  
8.      
9.    public BookColleation(){  
10.          
11.    }  
12.      
13.    static{  
14.        bookMap.put("1", new Book("1","如何一秒钟赚一百万","巴菲特","9.9"));  
15.        bookMap.put("2", new Book("2","通往火星","奥巴马","19.9"));  
16.        bookMap.put("3",new Book("3","冰红茶喝出柠檬味","康帅富","1.0"));  
17.    }  
18.      
19.    public static Map<String, Book>  getBookColleation(){  
20.        return bookMap;  
21.    }  
22.      
23.    public static Book  getBook(String id){  
24.        return   bookMap.get(id);  
25.    }  
26.}  
```

首页，显示所有的书名和之前看过的所有书

```java
1.public class BookCookie extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        resp.setContentType("text/html;charset=utf-8");  
7.        //显示所有的书  
8.        Map<String, Book>  map = BookColleation.getBookColleation();  
9.        for(Map.Entry<String, Book>  m  :  map.entrySet() ){  
10.            Book  book = m.getValue();  
11.            resp.getWriter().write("<a href='"+req.getContextPath()+
12.                                "/bookInfo?id="+book.getId()+"'>"+book.getName()+"</a><br>");  
13.        }  
14.        resp.getWriter().write("<hr>");  
15.          
16.        //显示之前看过的书  
17.        Cookie[] ck = req.getCookies();  
18.        Cookie bkCookie = null;  
19.        if(ck != null){  
20.            for(Cookie  c : ck){  
21.                if("last".equals(c.getName()))  
22.                    bkCookie = c;  
23.            }  
24.        }  
25.        if(bkCookie!=null){  
26.            String id = bkCookie.getValue();  
27.            Book bk = BookColleation.getBook(id);  
28.            String name = bk.getName();  
29.            resp.getWriter().write("您最后看过的书:<br>"+name);  
30.        }else{  
31.            resp.getWriter().write("没有看过任何书");  
32.        }  
33.    }  
34.  
35.    @Override  
36.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
37.            throws ServletException, IOException {  
38.        // TODO Auto-generated method stub  
39.        super.doPost(req, resp);  
40.    }  
41.  
42.}  
```

获取看过的书并将其存入到cookie待首页显示时调用查询

```java
1.public class BookInfo extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        resp.setContentType("text/html;charset=utf-8");  
7.        //获取要查看的书的具体内容，根据id  
8.        String id = req.getParameter("id");  
9.        Book book = BookColleation.getBook(id);  
10.        if(book == null){  
11.            resp.getWriter().write("没有这本书");  
12.            return;  
13.        }else {  
14.            resp.getWriter().write("<h1>书名："+book.getName()+"</h1>");  
15.            resp.getWriter().write("<h3>作者:"+book.getAuthor()+"</h3>");  
16.            resp.getWriter().write("<h3>价格："+book.getPrice()+"</h3>");  
17.              
18.        }  
19.          
20.        //将看过的书信息保存到cookie  
21.        Cookie cookie = new Cookie("last", book.getId());  
22.        cookie.setMaxAge(3600*10);  
23.        cookie.setPath(req.getContextPath());  
24.        resp.addCookie(cookie);  
25.    }  
26.  
27.    @Override  
28.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
29.            throws ServletException, IOException {  
30.        // TODO Auto-generated method stub  
31.        super.doPost(req, resp);  
32.    }  
33.  
34.}  
```

### 12. Session

#### 12.1. **基础概念**

1) 在WEB开发中，服务器可以为每个用户浏览器创建一个会话对象（session对象），注意：**一个浏览器独占一个session对象**(默认情况下)。因此，在需要保存用户数据时，服务器程序可以把用户数据写到用户浏览器独占的session中，当用户使用浏览器访问其它程序时，其它程序可以从用户的session中取出该用户的数据，为用户服务。

2) session是一个域对象，作用范围为整个会话。

3) Session和Cookie的主要区别在于：

**Cookie是把用户的数据写给用户的浏览器**。

**Session技术把用户的数据写到用户独占的session中,服务器端**

#### 12.2. 实际例程

1) 利用session模拟网页支付

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
3.<html>  
4.  <head>  
5.  </head>  
6.   
7.  <body>  
8.    <a href="<%=request.getContextPath() %>/scan?kind=书">书</a>  
9.    <a href="<%=request.getContextPath() %>/scan?kind=汽车">汽车</a>  
10.    <a href="<%=request.getContextPath() %>/pay">支付</a>  
11.  </body>  
12.</html>  
```

![1492936789881](README.assets/1492936789881.png)

```java
1.public class Scan extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //获取提交的类别，显示具体内容  
7.        String  kind =  req.getParameter("kind");  
8.        kind = new String(kind.getBytes("iso8859-1"),"utf-8");  
9.          
10.        //生成session  
11.        HttpSession  session = req.getSession();  
12.        //手动生成cookie，当用户关闭浏览器重启后依然能够利用cookie信息完成支付  
13.        Cookie  cookie = new Cookie("JSESSIONID",session.getId());  
14.        cookie.setMaxAge(3600*10);  
15.        cookie.setPath(req.getContextPath());  
16.        resp.addCookie(cookie);  
17.          
18.        session.setAttribute("kind",kind );  
19.    }  
20.  
21.    @Override  
22.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
23.            throws ServletException, IOException {  
24.        doGet(req, resp);  
25.    }  
26.}  

1.public class Pay extends HttpServlet {  
2.    @Override  
3.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
4.            throws ServletException, IOException {  
5.        resp.setContentType("text/html;charset=utf-8");  
6.        HttpSession  session = req.getSession();  
7.        String  kind = (String)session.getAttribute("kind");  
8.          
9.        resp.getWriter().write("您购买的"+kind+"价值999999999999999999元");  
10.          
11.    }  
12.    @Override  
13.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
14.            throws ServletException, IOException {  
15.        // TODO Auto-generated method stub  
16.        super.doPost(req, resp);  
17.    }  
18.}  
```

![1492936803485](README.assets/1492936803485.png)

URL重写

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
3.<html>  
4.  <head>  
5.  </head>  
6.   
7.  <body>  
8.    <%  
9.        request.getSession();  
10.          
11.        String url1 =  request.getContextPath()+"/scan?kind=书";  
12.        url1 = response.encodeURL(url1);  
13.        String url2 =  request.getContextPath()+"/scan?kind=汽车";  
14.        url2 = response.encodeURL(url2);  
15.        String url3 = request.getContextPath()+"/pay";  
16.        url3 = response.encodeURL(url3);  
17.     %>  
18.       
19.    <a href="<%= url1 %>">书</a>  
20.    <a href="<%= url2 %>">汽车</a>  
21.    <a href="<%= url3 %>">支付</a>  
22.  </body>  
23.</html>  
```

2) 利用seesion模拟网站登录、注销过程

网站欢迎首页 welcome.jsp

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
3.<html>  
4.  <head>  
5.  </head>  
6.    
7.  <body>  
8.        <h3>欢迎页面<h3><hr>  
9.        <%  
10.            String username = (String)session.getAttribute("username");  
11.        %>  
12.        <%   
13.            if(username == null ||  "".equals(username)){  
14.         %>  
15.            欢迎您访问本网站  
16.            <a  href="<%=request.getContextPath() %>/login.jsp">登录</a>  
17.            <a  href="#">注册</a>  
18.              
19.         <%  
20.         } else {  
21.         %>  
22.            欢迎您回来 <%=username %>  
23.            <a href="<%=request.getContextPath() %>/logout">注销</a>  
24.         <%  
25.            }  
26.          %>  
27.  </body>  
28.</html>  
```

![1492936841909](README.assets/1492936841909.png)

用户登录：login.jsp

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
3.<html>  
4.  <head>  
5.  </head>  
6.    
7.  <body>  
8.        <h3>登录界面</h3><hr>  
9.          
10.        <form  action="${pageContext.request.contextPath }/login"  method="POST">  
11.            用户名：<input type="text" name="username"/>  
12.            密码:<input type="text" name="password"/>  
13.            <input type="submit" value="立刻登录"/>  
14.        </form>  
15.  </body>  
16.</html>  
```

```java
1.public class Login extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        req.setCharacterEncoding("utf-8");  
7.        resp.setContentType("text/html;charset=utf-8");  
8.        //获取提交的用户名和密码  
9.        String username = req.getParameter("username");  
10.        String password = req.getParameter("password");  
11.        //查询数据库判断是否存在该用户  
12.        if(new UsersInfo().isExist(username, password)){  
13.        //如果正确重定向到主页  
14.            req.getSession().setAttribute("username",username);  
15.            resp.sendRedirect(req.getContextPath()+"/welcome.jsp");  
16.            return;  
17.        }else{  
18.            //如果不正确给出提示  
19.            resp.getWriter().write("登录错误，请重新登录");  
20.            //resp.sendRedirect(req.getContextPath()+"/login.jsp");  
21.        }  
22.    }  
23.  
24.    @Override  
25.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
26.            throws ServletException, IOException {  
27.        doGet(req, resp);  
28.          
29.    }  
30.  
31.}  
```

![1492936868080](README.assets/1492936868080.png)

登录成功：

![1492936880316](README.assets/1492936880316.png)

登录失败：

![1492936964991](README.assets/1492936964991.png)

注销页面：

```java
1.public class Logout extends HttpServlet {  
2.  
3.    @Override  
4.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)  
5.            throws ServletException, IOException {  
6.        //用户注销  
7.        //杀死session  
8.        if(req.getSession(false)!=null && req.getSession().getAttribute("username")!=null){  
9.            req.getSession().invalidate();  
10.        }  
11.          
12.        //重定向到开始主页  
13.        resp.sendRedirect(req.getContextPath()+"/welcome.jsp");  
14.    }  
15.  
16.    @Override  
17.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)  
18.            throws ServletException, IOException {  
19.        doGet(req, resp);  
20.    }  
21.  
22.}  
```

用户信息存储及xml配置：

```java
1.package com.example.session;  
2.  
3.import java.util.HashMap;  
4.import java.util.Map;  
5.  
6.public class UsersInfo {  
7.    private  static Map<String, String>  userMap = new HashMap<String,String>();  
8.      
9.    static{  
10.        userMap.put("奥巴马","123");  
11.        userMap.put("xiaoming","456");  
12.        userMap.put("我是谁","111");  
13.    }  
14.  
15.     public boolean isExist(String username,String password){  
16.         return userMap.containsKey(username)&&userMap.get(username).equals(password);  
17.     }  
18.}  
```

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">  
3.  <display-name>SessionLogin</display-name>  
4.  <servlet>  
5.    <servlet-name>login</servlet-name>  
6.    <servlet-class>com.example.session.Login</servlet-class>  
7.  </servlet>  
8.    <servlet>  
9.    <servlet-name>logout</servlet-name>  
10.    <servlet-class>com.example.session.Logout</servlet-class>  
11.  </servlet>  
12.    
13.  <servlet-mapping>  
14.    <servlet-name>login</servlet-name>  
15.    <url-pattern>/login</url-pattern>  
16.  </servlet-mapping>  
17.    <servlet-mapping>  
18.    <servlet-name>logout</servlet-name>  
19.    <url-pattern>/logout</url-pattern>  
20.  </servlet-mapping>  
</web-app>  
```

### 13. 会话管理

#### 13.1.  概述

1) HTTP是**无状态性的**，一个Web服务器无法区分一个HTTP请求是否是第一次访问

2) 4种不同的状态保存技术：URL重写，隐藏域，cookies和HTTPSession对象，其实就是缓存技术

#### **13.2.**  URL重写

1) URL重写是一种会话跟踪技术，将一个或多个token添加到URL的查询字符串中，每个token为

2) K-V形式，例如：

a) url **? key-1=value-1&key-2=value-2**...

3) URL重写适合tokens无需在太多URL直接传递的情况下

4) URL在某些浏览器上最大长度为2000字符，这就限制了tokens数

5) 某些信息是敏感的，直接放在URL请求中是不合适的，例如用户密码

#### 13.3.  隐藏域

1) 将信息保存到HTML表单的隐藏域中

2) 当表单提交的时候，隐藏域的值也同时提交到服务器端

3) 例如我们在HTML页面中定义一个隐藏标签：

 <input  type=”hidden” name=”id” value=”1”/>

这个标签在页面展示时候并不会显示出来，但是当表单提交的时候，它会被提交到服务端，我们在Servlet里面可以通过  request.getParameter(“id”) 来获取它的值，这里即 1

#### 13.4. Cookies

1) Cookies 是一个很少的信息片段，可自动的在浏览器和Web服务器之间交互，因此cookies可以存储在多个页面之间传递的信息

2) Cookie作为HTTP Header的一部分，其传输由HTTP协议控制

3) 浏览器通常支持每个网站20个cookies

4) 创建cookies:

**Cookie  cookie = new Cookie(name, value);**

5) 可设置Cookie的domain、path和maxAge属性，其中maxAge属性决定cookie何时过期

6) Servlet添加cookie，通过HttpServletResponse的add方法：

**HttpServletResponse.addCookie(cookie)**

服务器会将设置的cookie发送到浏览器

7) Cookies也可以通过客户端的javascript脚本创建和删除

8) 服务器端读取提交的cookies，通过HttpServletRequest的getCookies方法，若查询某个cookie，需要遍历数组

9) 没有直接删除cookie的方法，只能创建一个同名的cookie，并将maxAge属性设置为0，并放到HttpServletResponse中，例如：

**Cookie cookie = new Cookie(“name”, “”);**

**Cookie.setMaxAge(0);**

**Response.addCookie(cookie);**

10)  示例程序

```java
1.package com.example.servlet;  
2.  
3.import java.io.IOException;  
4.import java.io.PrintWriter;  
5.  
6.import javax.servlet.ServletException;  
7.import javax.servlet.annotation.WebServlet;  
8.import javax.servlet.http.Cookie;  
9.import javax.servlet.http.HttpServlet;  
10.import javax.servlet.http.HttpServletRequest;  
11.import javax.servlet.http.HttpServletResponse;  
12.  
13./** 
14. * cookie使用示例servlet 
15. *  
16. * @author zhuyong 
17. */  
18.@WebServlet(urlPatterns="/cookieInfo")  
19.public class CookieInfoServlet extends HttpServlet{  
20.    private static final long serialVersionUID = 7025194241125675597L;  
21.  
22.    @Override  
23.    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
24.        boolean hasPageCookie = false; //是否含有名称为page的cookie  
25.        Cookie[] cookies = req.getCookies();  
26.          
27.        if(null != cookies){  
28.            for(Cookie cookie : cookies){  
29.                String cookieName = cookie.getName();  
30.                if("page".equals(cookieName)){  
31.                    hasPageCookie = true;  
32.                }  
33.            }  
34.        }  
35.          
36.        if(hasPageCookie){  
37.            showMainPage(req,resp);  
38.        }else{  
39.            showWelcomePage(req, resp);  
40.        }  
41.    }  
42.      
43.  
44.    /** 
45.     * 如果客户端并没有指定的cookie缓存，就跳转到欢迎页面 
46.     * @param req 
47.     * @param resp 
48.     */  
49.    private void showWelcomePage(HttpServletRequest req, HttpServletResponse resp) {  
50.        Cookie cookie = new Cookie("page", "infoPage");  
51.        resp.addCookie(cookie);  
52.          
53.        resp.setContentType("text/html");  
54.        resp.setCharacterEncoding("UTF-8");  
55.          
56.        PrintWriter writer = null;  
57.          
58.        try{  
59.            writer = resp.getWriter();  
60.            writer.write("<html><head></head><body>你好，欢迎来到梦幻王国！</body></html>");  
61.        }catch (Exception e) {  
62.            e.printStackTrace();  
63.        }finally {  
64.            if(null != writer){  
65.                writer.close();  
66.            }  
67.        }  
68.    }  
69.      
70.    /** 
71.     * 如果存在指定的cookie，则显示信息页面 
72.     *  
73.     * @param req 
74.     * @param resp 
75.     */  
76.    private void showMainPage(HttpServletRequest req, HttpServletResponse resp) {  
77.        resp.setContentType("text/html");  
78.        resp.setCharacterEncoding("UTF-8");  
79.          
80.        PrintWriter writer = null;  
81.          
82.        try{  
83.            writer = resp.getWriter();  
84.            writer.write("<html><head></head><body>"  
85.                    + "<h1>梦幻王国</h1>"  
86.                    + "<hr>"  
87.                    + "<p>在梦幻王国的每一个地方...</p>"  
88.                    + "</body></html>");  
89.              
90.        }catch (Exception e) {  
91.            e.printStackTrace();  
92.        }finally {  
93.            if(null != writer){  
94.                writer.close();  
95.            }  
96.        }  
97.    }  
98.}  
```

示例程序Servlet 在接收到请求时候判断是否存在指定cookie,如果不存在就创建一个cookie，这个其实就是在第一次请求的时候，展示欢迎页面。后续再次请求，因为存在了cookie，便展示信息页面。

第一次请求：

![1493119445861](README.assets/1493119445861.png)

第二次及后续请求：

![1493119459924](README.assets/1493119459924.png)

我们F12调试跟踪下网络请求：

第一次：

![1493119480338](README.assets/1493119480338.png)

可以看到第一次请求服务器端通过response 返回给客户端cookie

第二次：

![1493119496673](README.assets/1493119496673.png)

第二次请求在请求中携带着之前客户端缓存的cookie信息，同时服务器端也返回这个cookie了。

#### 13.5. HttpSession对象

1) 一个用户**有且最多**有一个HttpSession，并且**不会被其他用户访问到**

2) 这里的用户指客户端，一个客户端例如浏览器访问会生成对应的HttpSession,当客户端关闭或者请求一次后将缓存清除了，再次请求的时候会创建新的HttpSession。

3) HttpSession对象在用户**第一次**访问网站的时候**自动被创建**

4) 可使用HttpServletResponse的**getSession**方法获取该对象

5) 可通过HttpSession的setAttribute方法将值放入到HttpSession中，同名的话新值会覆盖旧值

6) getAttribute()可获取之前存放进去的对象

7) 放入到HttpSession中的值，是存储在**内存**中的，因此不要往HttpSession中存放太多信息

8) 所有存放在HttpSession的数据**不会被发送到客户端**，session是**服务器**端缓存技术

9) Servlet会为每个HttpSession生成唯一的标识，并将该标识发送给给浏览器，或创建一个名为JSESSIONID的cookie，或者在URL后附加一个名为jsessionid的参数。在后续请求中，浏览器会将该标识提交给服务器。可使用HttpSession的getId()获取标识。

10) 设置HttpSession的超时时间：void **setMaxInactiveInterval**(int seconds)，若设置为0，永不过期

![1493211098855](README.assets/1493211098855.png)

11) 示例程序

* **示例程序一**

```java
1.@Override  
2.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)   
3.            throws ServletException, IOException {  
4.        System.out.println(req.getSession().getId());  
5.    }  
```

我们简单的输出创建的session 的ID，我们用不同的浏览器访问，会发现创建的HttpSession是不一样的。同一个客户端多次请求创建的session是一致的（即第一次创建了，后续同样的客户端就不会创建了）。当我们关闭浏览器或者清除浏览器缓存，再次请求，会发现生成了新的session，因为浏览器中的cookie失效了或者被清除了，服务器端识别不出所请求的客户端是之前已经请求过的，所以新建了HttpSession。

我们看下浏览器请求：

第一次：

![1493211144816](README.assets/1493211144816.png)

服务器后台输出：

![1493211157168](README.assets/1493211157168.png)

第一次请求服务器端生成了HttpSession，并且创建了对应名称为JSESSIONID，值为HttpSession的id 的cookie，通过response返回给客户端缓存。

第二次请求：

![1493211171682](README.assets/1493211171682.png)

第二次客户端请求会带着session创建的cookie标识，这个就告诉了服务器端还是之前的那个客户端提交的，这样服务器就知道了不同的请求来自于同一个客户端，同时因为之前创建了这个客户端对应的HttpSession，也就不新建新的了。

*** 示例程序二：用户登录**  

![1493211253383](README.assets/1493211253383.png)

**IndexServlet.java ：**

```java
1./** 
2. * 应用入口 
3. *  
4. * @author zhuyong 
5. */  
6.@WebServlet(urlPatterns="/*")  
7.public class IndexServlet extends HttpServlet{  
8.    private static final long serialVersionUID = 2195807259091461865L;  
9.  
10.    /**  
11.     * 用户请求应用处理，判断是否登录  
12.     */  
13.    @Override  
14.    protected void doGet(HttpServletRequest req, HttpServletResponse resp)   
15.            throws ServletException, IOException {  
16.        HttpSession session = req.getSession();  
17.        String userName = (String) session.getAttribute("username");  
18.        String password = (String) session.getAttribute("password");  
19.          
20.        if(null == userName || "".equals(userName) || null == password || "".equals(password)){  
21.            System.out.println("该客户端：" + req.getRemoteAddr() + " 尚未登录，跳转到登录页面！");  
22.            LoginPage.showLoginPage(req, resp, "请输入用户名或密码");  
23.        }else{  
24.            MainPage.showMainPage(req, resp);  
25.        }  
26.    }  
27.}  
```

**LoginServlet.java：**

```java
1./** 
2. * 登录Servlet 
3. *  
4. * @author zhuyong 
5. */  
6.@WebServlet(urlPatterns="/login")  
7.public class LoginServlet extends HttpServlet{  
8.    private static final long serialVersionUID = -4683621834171104969L;  
9.      
10.    /** 
11.     * 直接访问而非表单提交处理（已经约定表单都用POST提交） 
12.     */  
13.    @Override  
14.    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {  
15.        HttpSession session = req.getSession();  
16.        String userName = (String) session.getAttribute("username");  
17.        String password = (String) session.getAttribute("password");  
18.          
19.        if(null == userName || "".equals(userName) || null == password || "".equals(password)){  
20.            System.out.println("该客户端：" + req.getRemoteAddr() + " 尚未登录，跳转到登录页面！");  
21.            LoginPage.showLoginPage(req, resp, "请输入用户名或密码");  
22.        }else{  
23.            MainPage.showMainPage(req, resp);  
24.        }  
25.    }  
26.  
27.    /** 
28.     * 登录表单提交处理逻辑 
29.     */  
30.    @Override  
31.    protected void doPost(HttpServletRequest req, HttpServletResponse resp)   
32.            throws ServletException, IOException {  
33.        String username = req.getParameter("username");  
34.        String password = req.getParameter("password");  
35.          
36.        if(null == username || "".equals(username) || null == password || "".equals(password)){  
37.            System.out.println("该客户端：" + req.getRemoteAddr() + " 提交用户名或密码为空，跳转到登录页面！");  
38.            LoginPage.showLoginPage(req, resp, "用户名或者密码为空");  
39.        }else{  
40.            System.out.println("用户：" + username + "登录成功！");  
41.            HttpSession session = req.getSession();  
42.            session.setAttribute("username", username);  
43.            session.setAttribute("password", password);  
44.            MainPage.showMainPage(req, resp);  
45.        }  
46.    }  
47.}  
```

 **LoginPage.java：**

```java
1. /** 
2. * 显示登录页面 
3. *  
4. * @author zhuyong 
5. */  
6.public class LoginPage {  
7.    /** 
8.     * 显示登录页面 
9.     *  
10.     * @param req    
11.     * @param resp 
12.     * @param info 提示信息 
13.     */  
14.    public static void showLoginPage(HttpServletRequest req, HttpServletResponse resp, String info) {  
15.        resp.setContentType("text/html");  
16.        resp.setCharacterEncoding("UTF-8");  
17.          
18.        PrintWriter writer = null;  
19.          
20.        try {  
21.            writer = resp.getWriter();  
22.            writer.write("<html>"  
23.                    + "<head><title>用户登录</title></head>"  
24.                    + "<body>"  
25.                    + "<form method='POST' action='login'>"  
26.                    + "<font color='red'>"  
27.                    + info  
28.                    + "</font>"  
29.                    + "</br>"  
30.                    + "用户名：<input name='username' style='width:400px'/>"  
31.                    + "</br></br>"  
32.                    + "密    码：<input name='password' style='width:400px'/>"  
33.                    + "</br>"  
34.                    + "<input type='submit' value='登录'>"  
35.                    + "</form>"  
36.                    + "</body>"  
37.                    + "</html>");  
38.        } catch (IOException e) {  
39.            e.printStackTrace();  
40.        }finally {  
41.            if(null != writer){  
42.                writer.close();  
43.            }  
44.        }  
45.    }  
46.}  
```

**MainPage.java：**

```java
1./** 
2. * 显示用户登录成功后主页面 
3. *  
4. * @author zhuyong 
5. */  
6.public class MainPage {  
7.    /** 
8.     * 显示主页面 
9.     *  
10.     * @param req 
11.     * @param resp 
12.     */  
13.    public static void showMainPage(HttpServletRequest req, HttpServletResponse resp) {  
14.        resp.setContentType("text/html");  
15.        resp.setCharacterEncoding("UTF-8");  
16.          
17.        PrintWriter writer = null;  
18.          
19.        try {  
20.            writer = resp.getWriter();  
21.            writer.write("<html>"  
22.                    + "<head><title>主页面</title></head>"  
23.                    + "<body>"  
24.                    + "欢迎您，"   
25.                    + req.getSession().getAttribute("username")  
26.                    + "</body>"  
27.                    + "</html>");  
28.        } catch (IOException e) {  
29.            e.printStackTrace();  
30.        }finally {  
31.            if(null != writer){  
32.                writer.close();  
33.            }  
34.        }  
35.    }  
36.}  
```

  ①  请求应用：

![1493216193558](README.assets/1493216193558.png)

服务器后台输出：

![1493216207773](README.assets/1493216207773.png)

因为用户还未登录，所以HttpSession中查询不到这个客户端的用户密码信息，所以显示登录界面。

② 假设输入用户名，但是不输入密码：

![1493216221908](README.assets/1493216221908.png)

服务器后台输出：

![1493216238461](README.assets/1493216238461.png)

后台判断出提交的内容为空，所以重新跳转到登录界面。

③ 输入用户名和密码：

![1493216252335](README.assets/1493216252335.png)

点击登录按钮提交2，后台输出：

![1493216294052](README.assets/1493216294052.png)

前端跳转到主页：

![1493216280557](README.assets/1493216280557.png)

说明此时服务器端获取到了提交的用户名和密码。

④ 当我们再次请求/Session 或着 /Session/login 时候，还是一直显示上面的欢迎界面，因为服务器端在我们这个客户端对应的HttpSession中找到了之前提交的用户名和密码，知道登录过了，所以就不用登录了，因此显示这个界面。

⑤ 当我们清除浏览器缓存或者关闭浏览器重新请求这个应用时，又回到了登录界面，因为之前的浏览器端缓存已经失效或者丢失了，服务器端新生成了HttpSession，而新的HttpSession中并没有用户名和密码信息，所以认为客户端没有登录过，因此需要重新登录。

### 14.EL表达式

#### 14.1. 定义

EL 全名为Expression Language

#### 14.2. EL主要作用：

1) 获取数据：

EL表达式主要用于替换JSP页面中的脚本表达式，以从各种类型的web域 中检索java对象、获取数据。(某个web域 中的对象，访问javabean的属性、访问list集合、访问map集合、访问数组)

2) 执行运算：

利用EL表达式可以在JSP页面中执行一些基本的关系运算、逻辑运算和算术运算，以在JSP页面中完成一些简单的逻辑运算。${user==null}

3) 获取web开发常用对象

EL 表达式定义了一些隐式对象，利用这些隐式对象，web开发人员可以很轻松获得对web常用对象的引用，从而获得这些对象中的数据。

4) 调用Java方法

EL表达式允许用户开发自定义EL函数，以在JSP页面中通过EL表达式调用Java类的方法。

#### 14.3. 实例

1) EL获取数据

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ page import="com.example.test.Person" %>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.    
9.  <body>  
10.    <h3>获取常量数据</h3><hr>  
11.    ${"一弦一柱思华年" } ${999} ${true}  
12.      
13.    <h3>获取域中的变量数据</h3><hr>  
14.    <%  
15.        String name = "梅超风";  
16.        pageContext.setAttribute("name", name);  
17.     %>  
18.     ${name }  
19.       
20.     <h3>获取数组数据</h3><hr>  
21.     <%  
22.        String[]  dayDream = {"一手托起喜马拉雅山","飞上云霄","一秒到火星"};  
23.        pageContext.setAttribute("dream", dayDream);  
24.      %>  
25.     ${dream[1] }  
26.       
27.     <h3>获取集合中的数据</h3><hr>  
28.     <%  
29.        List<String>  list = new ArrayList<String>();  
30.        list.add("小红");  
31.        list.add("红花狼");  
32.        list.add("小太郎");  
33.        pageContext.setAttribute("list", list);  
34.          
35.      %>  
36.      ${list[1] }  
37.       
38.      
39.     <h3>获取Map中的数据</h3><hr>  
40.    <%  
41.        Map<String,String> map = new HashMap();  
42.        map.put("name","奥巴马");  
43.        map.put("age","17");  
44.        map.put("gender","男");  
45.        map.put("addr","白宫");  
46.        map.put("name.xiao","小黑黑");  
47.        pageContext.setAttribute("map",map);  
48.        pageContext.setAttribute("name","age");  
49.     %>  
50.     ${map["name"] }            ${map["addr"] }  
51.     ${map.gender }             ${map.age }  
52.     ${map["name.xiao"] }       ${name }  
53.       
54.     <h3>获取javabean的属性</h3><hr>  
55.     <%  
56.        Person p = new Person();  
57.        p.setName("西门吹雪");  
58.        p.setAge("99");  
59.        pageContext.setAttribute("p", p);  
60.      %>  
61.      ${p.age }  ${p.name }  
62.  </body>  
63.</html>  
```

![1493532434225](README.assets/1493532434225.png)

2) EL执行运算

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
3.<html>  
4.  <head>  
5.  </head>      
6.  <body>  
7.    <h3>算术运算：非数字参与运算会被转为数字参与运算</h3><hr>  
8.    ${1+1 }  
9.    ${1+"4" }  
10.    <%--${1+"a" } 错误 --%>  
11.      
12.    <h3>比较运算</h3><hr>  
13.    ${1>3 }  
14.    ${3 le 6 }  
15.      
16.    <h3>逻辑运算</h3><hr>  
17.      
18.    <h3>三元运算</h3><hr>  
19.    ${5>8?"是的":"不是" }  
20.      
21.    <h3>empty 运算符 : 判断一个对象是否为null或字符串是否为空字符串或集合内容是否为空或域中是否没有任何属性</h3><hr>  
22.    <%  
23.        String str = "aaaa";  
24.        pageContext.setAttribute("str",str);  
25.        List<String> list = new ArrayList<String>();  
26.        list.add("");  
27.        pageContext.setAttribute("list",list);  
28.     %>  
29.     ${empty str }  
30.     ${empty list}  
31.     ${empty pageScope }  
32.      
33.      
34.  </body>  
35.</html>  
```

![1493532466894](README.assets/1493532466894.png)

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.  
3.<%--导入标签库 --%>  
4.<%@ taglib  uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>  
5.  
6.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
7.<html>  
8.  <head>  
9.  
10.  </head>  
11.    
12.  <body>  
13.        <%--利用标签库中提供的方法将字符以大写形式输出 --%>  
14.        ${fn:toUpperCase("asdasdDASasd") }  
15.  </body>  
</html>  
```

![1493532489847](README.assets/1493532489847.png)

### 15. 自定义标签

#### 15.1. 传统标签:

例：显示ip地址

1) 写一个类实现Tag接口

```java
1. public class ShowIpTag implements javax.servlet.jsp.tagext.Tag{  
2.    private PageContext pc = null;  
3.      
4.    @Override  
5.    public int doEndTag() throws JspException {  
6.        return 0;  
7.    }  
8.  
9.    @Override  
10.    public int doStartTag() throws JspException {  
11.        try {  
12.            //获取ip  
13.            String ip = pc.getRequest().getRemoteAddr();  
14.            //输出  
15.            pc.getOut().write(ip);  
16.        } catch (IOException e) {  
17.            e.printStackTrace();  
18.        }  
19.        return 0;  
20.    }  
21.  
22.    @Override  
23.    public javax.servlet.jsp.tagext.Tag getParent() {  
24.        return null;  
25.    }  
26.  
27.    @Override  
28.    public void release() {  
29.    }  
30.  
31.    @Override  
32.    public void setPageContext(PageContext pc) {  
33.        this.pc = pc;  
34.    }  
35.  
36.    @Override  
37.    public void setParent(javax.servlet.jsp.tagext.Tag arg0) {  
38.    }  
39.  
40.}  
```

2) 写一个tld文件,描述写好的类

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8. <tag>  
9.      <name>showip</name>  
10.      <tag-class>com.example.tag.ShowIpTag</tag-class>  <%--所指向的类路径--%>  
11.      <body-content>empty</body-content>  
12. </tag>  
13.   
14.</taglib>  
```

注意新建tld文件时候版本要选择2.0

新建完成后将 xsi:schemaLocation= 后的属性复制到 *web-jsptaglibrary_2_0.xsd*  前面，这样

在文件中才有提示

3) 在jsp页面中引入tld文件,就可以在jsp页面中使用自定义标签了

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.    
9.  <body>  
10.        <h3>java输出ip为：</h3>  
11.        <%=request.getRemoteAddr() %>  
12.          
13.        <h3>自定义标签输出ip为:</h3>  
14.        <MyTag:showip></MyTag:showip>  
15.          
16.        <h3>简单标签输出的ip：</h3>  
17.        <MyTag:showip2></MyTag:showip2>  
18.  </body>  
19.</html>  
```

输出结果：

![1493545072269](README.assets/1493545072269.png)

分为doStartTag 和 doEndTag方法来分别处理发现开始标签和发现结束标签时的代码,在doStartTag可以通过返回值来控制标签体是否允许执行,在doEndTag方法里可以通过返回值控制标签之后的剩余页面是否允许执行；

传统标签的这种开发方式,需要我们分析发现开始标签和发现结束标签时都需要执行什么代码,还需要分析到底要返回什么样的标签体控制程序执行,相对来说相当的繁琐。

#### 15.2. 简单标签

(1) 写一个类实现SimpleTag接口(继承SimpleTag接口的默认实现类SimpleTagSupport)

```java
1.public class SimpleTagDemo  implements SimpleTag{  
2.    private JspContext jc = null;  
3.  
4.    @Override  
5.    public void doTag() throws JspException, IOException {  
6.        //转型获取PageContext对象  
7.        PageContext pContext = (PageContext)jc;  
8.        //获取ip  
9.        String pc = pContext.getRequest().getRemoteAddr();  
10.        //输出  
11.        pContext.getOut().write(pc);  
12.    }  
13.  
14.    @Override  
15.    public JspTag getParent() {  
16.        // TODO Auto-generated method stub  
17.        return null;  
18.    }  
19.  
20.    @Override  
21.    public void setJspBody(JspFragment arg0) {  
22.        // TODO Auto-generated method stub  
23.          
24.    }  
25.  
26.    @Override  
27.    public void setJspContext(JspContext jc) {  
28.        this.jc = jc ;  
29.    }  
30.  
31.    @Override  
32.    public void setParent(JspTag arg0) {  
33.        // TODO Auto-generated method stub  
34.          
35.    }  
36.  
37.}  
```

(2) 写一个tld文件,描述写好的类

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8. <tag>  
9.      <name>showip2</name>  
10.      <tag-class>com.example.simpletag.SimpleTagDemo</tag-class>  
11.      <body-content>empty</body-content>  
12. </tag>  
13.   
14.   
15.</taglib>  
```

(3) 在jsp页面中引入tld文件,就可以在jsp页面中使用自定义标签了

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.    
9.  <body>  
10.        <h3>java输出ip为：</h3>  
11.        <%=request.getRemoteAddr() %>  
12.          
13.        <h3>自定义标签输出ip为:</h3>  
14.        <MyTag:showip></MyTag:showip>  
15.          
16.        <h3>简单标签输出的ip：</h3>  
17.        <MyTag:showip2></MyTag:showip2>  
18.  </body>  
19.</html>  
```

#### 15.3. 控制简单自定义标签的执行

##### 15.3.1. 控制标签体是否执行 

```java
1.public class SimpleTagControl extends SimpleTagSupport{  
2.    private int times ;  
3.     public void setTimes(int times) {  
4.        this.times = times;  
5.    }  
6.  
7.    @Override  
8.    public void doTag() throws JspException, IOException {  
9.        //控制标签体是否执行  
10.        //----标签体不执行：什么都不做标签体就不执行  
11.        //----标签体执行：只要调用封装标签体的JSPFragment的invoke 方法就可以  
12.        JspFragment  jFragment = getJspBody();  
13.        //jFragment.invoke(getJspContext().getOut());  
14.        //invoke()方法里面直接传入null默认也会将内容写入out  
15.        jFragment.invoke(null);  
16.          
17.    }  
18.  
19.}  
```

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8.   
9. <tag>  
10.      <name>ControlTag</name>  
11.      <tag-class>com.example.simpletag.SimpleTagControl</tag-class>  
12.      <body-content>scriptless</body-content>  
13. </tag>  
14.   
15.</taglib>  
```

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.      
9.  <body>  
10.            标签之前  
11.            <MyTag:ControlTag >标签体</MyTag:ControlTag>  
12.            标签之后  
13.  </body>  
14.</html>  
```

![1493902353184](README.assets/1493902353184.png)

##### 15.3.2. 控制标签之后的内容是否执行

```java
1.public class SimpleTagControl extends SimpleTagSupport{  
2.    private int times ;  
3.     public void setTimes(int times) {  
4.        this.times = times;  
5.    }  
6.  
7.    @Override  
8.    public void doTag() throws JspException, IOException {  
9.          
10.        //控制标签之后的内容是否执行  
11.        //----标签之后的内容执行：什么都不做就会执行  
12.        //----标签之后的内容不执行：抛出SkipPageException这个异常就不会执行  
13.        throw new SkipPageException();  
14.        }  
15.  
16.}  
```

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8.   
9. <tag>  
10.      <name>ControlTag</name>  
11.      <tag-class>com.example.simpletag.SimpleTagControl</tag-class>  
12.      <body-content>scriptless</body-content>  
13. </tag>  
14.   
15.</taglib>  
```

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  </head>  
7.      
8.  <body>  
9.            标签之前  
10.            <MyTag:ControlTag >标签体</MyTag:ControlTag>  
11.            标签之后  
12.  </body>  
13.</html>  
```

![1493987111742](README.assets/1493987111742.png)

##### 15.3.3. 控制标签体重复执行

```java
1.public class SimpleTagControl extends SimpleTagSupport{  
2.    private int times ;  
3.     public void setTimes(int times) {  
4.        this.times = times;  
5.    }  
6.  
7.    @Override  
8.    public void doTag() throws JspException, IOException {  
9.          
10.        //控制标签体重复执行  
11.        for(int i=0;i<5;i++)  
12.            getJspBody().invoke(null);  
13.          
14.    }  
15.  
16.}  
```

```xml
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8.   
9. <tag>  
10.      <name>ControlTag</name>  
11.      <tag-class>com.example.simpletag.SimpleTagControl</tag-class>  
12.      <body-content>scriptless</body-content>  
13. </tag>  
14.   
</taglib>  
```

```html
1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.      
9.  <body>  
10.            标签之前  
11.            <MyTag:ControlTag >标签体</MyTag:ControlTag>  
12.            标签之后  
13.  </body>  
14.</html>  
```

![1493991277523](README.assets/1493991277523.png)

##### 15.3.4. 修改标签体后输出

```java
1.public class SimpleTagControl extends SimpleTagSupport{  
2.    private int times ;  
3.     public void setTimes(int times) {  
4.        this.times = times;  
5.    }  
6.  
7.    @Override  
8.    public void doTag() throws JspException, IOException {  
9.        //修改标签体后输出  
10.        StringWriter writer = new StringWriter();  
11.        JspFragment jspFragment = getJspBody();  
12.        jspFragment.invoke(writer);  
13.        String string = writer.toString();  
14.        string = string.toUpperCase();  
15.        getJspContext().getOut().write(string);  
16.          
17.    }  
18.}  
```

```html
1.<?xml version="1.0" encoding="UTF-8"?>  
2.<taglib version="2.0" xmlns="http://java.sun.com/xml/ns/j2ee"  
3. xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee  http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd">  
4. <tlib-version>1.0</tlib-version>  
5. <short-name>MyTag</short-name>  
6. <uri>http://www.example.com/MyTag</uri>  
7.   
8.   
9. <tag>  
10.      <name>ControlTag</name>  
11.      <tag-class>com.example.simpletag.SimpleTagControl</tag-class>  
12.      <body-content>scriptless</body-content>  
13. </tag>  
14.   
15.</taglib>  

1.<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>  
2.<%@ taglib uri="http://www.example.com/MyTag"  prefix="MyTag"%>  
3.<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">  
4.<html>  
5.  <head>  
6.  
7.  </head>  
8.      
9.  <body>  
10.            标签之前  
11.            <MyTag:ControlTag ><br>标签体zcxzxc<br></MyTag:ControlTag>  
12.            标签之后  
13.  </body>  
14.</html>  
```

![1493991323719](README.assets/1493991323719.png)

#### 15.4. 自定义标签来增加一个属性:

1) 在标签处理类中增加一个javabean属性,这个属性就是要增加的标签的属性,并对外提供setXXX方法

```java
1.public class SimpleTagControl extends SimpleTagSupport{  
2.    private int times ;  
3.     public void setTimes(int times) {  
4.        this.times = times;  
5.    }  
6.  
7.    @Override  
8.    public void doTag() throws JspException, IOException {  
9.        //控制标签体重复执行  
10.        for(int i=0;i<times;i++)  
11.            getJspBody().invoke(null);  
12.    }  
13.  
14.}  
```

2) 在tld文件中这个标签的描述中描述一下该属性 

```xml
1.<tag>  
2.      <name>ControlTag</name>  
3.      <tag-class>com.example.simpletag.SimpleTagControl</tag-class>  
4.      <body-content>scriptless</body-content>  
5.      <attribute>  
6.        <name>times</name>  
7.        <required>true</required>  
8.        <type>int</type>  
9.        <rtexprvalue>false</rtexprvalue>  
10.      </attribute>  
 </tag>  
```

则在jsp文件中引用该标签时自动加上属性 (此处功能相当于标签体执行5次)

```html
1.<body>  
2.            标签之前  
3.            <MyTag:ControlTag times="5"><br>标签体zcxzxc<br></MyTag:ControlTag>  
4.            标签之后  
5.  </body>  
```

### 16.JDBC

#### 16.1. JDBC快速入门

* 导入JDBC  jar包到当前工程lib目录，添加到编译路径

  操作数据库，新建数据库 day10 , 在其中新建表user ，

![1494059972885](README.assets/1494059972885.png)

![1494418590345](README.assets/1494418590345.png)

利用JDBC操作数据库

```java
1.package com.example.jdbc;  
2.  
3.import java.sql.Connection;  
4.import java.sql.DriverManager;  
5.import java.sql.ResultSet;  
6.import java.sql.SQLException;  
7.import java.sql.Statement;  
8.  
9.import com.mysql.jdbc.Driver;  
10.  
11.public class JDBCDemo1 {  
12.  
13.    public static void main(String[] args) throws SQLException{  
14.        //1、注册数据库驱动  
15.        DriverManager.registerDriver(new Driver());  
16.        //2、获取数据库连接  
17.        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/day10","root","root");  
18.        //3、获取传输器对象  
19.        Statement statement = connection.createStatement();  
20.        //4、利用传输器传输sql语句到数据库中执行，获取结果集对象  
21.        ResultSet rs = statement.executeQuery("select * from user");  
22.        //5、遍历结果集查询结果  
23.        while (rs.next()) {  
24.            String name = rs.getString("name");  
25.            System.out.println(name);  
26.              
27.        }  
28.        //6、关闭资源  
29.        rs.close();  
30.        statement.close();  
31.        connection.close();  
32.          
33.    }  
34.}  
```

结果显示数据库中 name 对应的值

#### 16.2. 对数据库进行增删改查操作

* 先将操作中的共性方法封装成类

```java
1.package com.example.util;  
2.  
3.import java.io.FileReader;  
4.import java.sql.Connection;  
5.import java.sql.DriverManager;  
6.import java.sql.ResultSet;  
7.import java.sql.SQLException;  
8.import java.sql.Statement;  
9.import java.util.Properties;  
10.  
11.  
12.public class JDBCUtil {  
13.    private  static Properties properties;  
14.    static{  
15.        try {  
16.            properties = new Properties();  
17.            properties.load(new FileReader(JDBCUtil.class.getClassLoader().getResource("config.properties").getPath()));  
18.        } catch (Exception e) {  
19.            e.printStackTrace();  
20.            throw new RuntimeException();  
21.        }  
22.          
23.    }  
24.  
25.    /* 
26.     * 注册数据库驱动并且获取数据库连接 
27.     * @param null 
28.     * @return 数据库连接 
29.     */  
30.    public static Connection getConnection() throws ClassNotFoundException, SQLException{  
31.        //1、注册数据库驱动  
32.        //DriverManager.registerDriver(new Driver());  
33.        Class.forName(properties.getProperty("driver"));  
34.        //2、获取数据库连接  
35.        return  DriverManager.getConnection(properties.getProperty("uri"),properties.getProperty("user"),properties.getProperty("password"));  
36.          
37.    }  
38.      
39.      
40.    /* 
41.     * 关闭资源 
42.     * @param ResultSet 
43.     * @param Statement 
44.     * @param Connection 
45.     */  
46.    public static void close(ResultSet rs,Statement st,Connection ct){  
47.        if(rs != null){  
48.            try {  
49.                rs.close();  
50.            } catch (SQLException e) {  
51.                e.printStackTrace();  
52.            }finally{  
53.                rs = null;  
54.            }  
55.        }  
56.          
57.        if(st != null){  
58.            try {  
59.                st.close();  
60.            } catch (SQLException e) {  
61.                e.printStackTrace();  
62.            }finally{  
63.                st = null;  
64.            }  
65.        }  
66.          
67.        if(ct != null){  
68.            try {  
69.                ct.close();  
70.            } catch (SQLException e) {  
71.                e.printStackTrace();  
72.            }finally{  
73.                ct = null;  
74.            }  
75.        }  
76.    }  
77.}  
```

相关properties 文件

```
driver=com.mysql.jdbc.Driver
uri=jdbc:mysql://localhost:3306/day10
user=root
password=root
```

测试文件

```java
1.package com.example.jdbc;  
2.  
3.import java.sql.Connection;  
4.import java.sql.ResultSet;  
5.import java.sql.Statement;  
6.  
7.import org.junit.Test;  
8.  
9.import com.example.util.JDBCUtil;  
10.  
11./* 
12. * 利用JDBC对数据库进行增删改查 
13. */  
14.public class JDBCDemo2 {  
15.      
16.    /* 
17.     * 对数据库进行更新数据 
18.     */  
19.    @Test  
20.    public void update() {  
21.        Connection ct = null;  
22.        Statement st = null;  
23.        ResultSet rs = null;  
24.          
25.        try {  
26.            ct = JDBCUtil.getConnection();  
27.            st = ct.createStatement();  
28.            st.executeUpdate("update user set password=2345 where name='lisi'");  
29.              
30.        } catch (Exception e) {  
31.            e.printStackTrace();  
32.        }finally{  
33.            JDBCUtil.close(rs, st, ct);  
34.        }  
35.    }  
36.      
37.    /* 
38.     * 删除数据 
39.     */  
40.    @Test  
41.    public void delete(){  
42.        Connection ct = null;  
43.        Statement st = null;  
44.        ResultSet rs = null;  
45.          
46.        try {  
47.            ct = JDBCUtil.getConnection();  
48.            st = ct.createStatement();  
49.            st.executeUpdate("delete from user where name='lisi'");  
50.        } catch (Exception e) {  
51.            e.printStackTrace();  
52.        }finally{  
53.            JDBCUtil.close(rs, st, ct);  
54.        }  
55.    }  
56.      
57.    /* 
58.     * 查询数据 
59.     */  
60.    @Test  
61.    public void find(){  
62.        Connection ct = null;  
63.        Statement st = null;  
64.        ResultSet rs = null;  
65.          
66.        try {  
67.            ct = JDBCUtil.getConnection();  
68.            st = ct.createStatement();  
69.            rs =st.executeQuery("select * from user where name='zs'");  
70.            while(rs.next()){  
71.                String name = rs.getString("name");  
72.                String password = rs.getString("password");  
73.                System.out.println(name+" : "+password);  
74.            }  
75.        } catch (Exception e) {  
76.            e.printStackTrace();  
77.        }finally{  
78.            JDBCUtil.close(rs, st, ct);  
79.        }  
80.    }  
81.      
82.    /* 
83.     *增加数据 
84.     */  
85.    @Test  
86.    public void add(){  
87.        Connection ct = null;  
88.        Statement st = null;  
89.        ResultSet rs = null;  
90.        try {  
91.            ct = JDBCUtil.getConnection();  
92.            st = ct.createStatement();  
93.            int count = st.executeUpdate("insert into user values (null,'zhaoliu','123456','zhaoliu@qq.com','1999-09-09')");  
94.            if(count > 0){  
95.                System.out.println("执行成功，并且影响的行数为"+count);  
96.            }else{  
97.                System.out.println("执行失败");  
98.            }  
99.                  
100.        } catch (Exception e) {  
101.            e.printStackTrace();  
102.        }finally{  
103.            JDBCUtil.close(rs, st, ct);  
104.        }  
105.    }  
106.}  
```

