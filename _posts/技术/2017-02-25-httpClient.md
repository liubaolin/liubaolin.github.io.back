---
layout: post
title: HttpClient4.5
category: 技术
tags: HttpClient
keywords:
description:
---
# HttpClient简介    

HTTP 协议可能是现在 Internet 上使用得最多、最重要的协议了，越来越多的 Java 应用程序需要直接通过 HTTP 协议来访问网络资源。虽然在 JDK 的 java.net 包中已经提供了访问 HTTP 协议的基本功能，但是对于大部分应用程序来说，JDK 库本身提供的功能还不够丰富和灵活。HttpClient 是 Apache Jakarta Common 下的子项目，用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。HttpClient 已经应用在很多的项目中，比如 Apache Jakarta 上很著名的另外两个开源项目 Cactus 和 HTMLUnit 都使用了 HttpClient。更多信息请关注http://hc.apache.org/

# HttpClient功能介绍     

以下列出的是HttpClient提供的主要功能,要知道所有详细的功能可以参见[HttpClient的主页](http://hc.apache.org/httpcomponents-client-4.5.x/index.html)    

* 实现了所有HTTP的方法(GET,PUT,POST,HEAD等)    
* 支持自动转向    
* 支持HTTPS等    
* 支持代理服务器等      

# 第一章:基础    

## 1.1 执行请求    

HttpClient最基本的功能是执行HTTP方法，一个 HTTP 方法的执行包含一个或多个 HTTP 请求/HTTP 相应的交换，通常由 HttpClient的内部来处理。使用者被要求提供一个Request对象来执行，HttpClient就会把请求传送给目标服务器并返回一个相对应的response对象，如果执行不成功，将会抛出一个异常。    

显然，HttpClient API 的主要切入点就是定义描述上述契约的HttpClient接口。    

一个简单的请求执行事例：    

    CloseableHttpClient httpclient = HttpClients.createDefault();  
    HttpGet httpget = new HttpGet("http://localhost/");  
    CloseableHttpResponse response = httpclient.execute(httpget);  
    try {  
       <...>  
    } finally {  
       response.close();  
    }  

### 1.1.1 HTTP请求(Request)    

所有 HTTP 请求都有一个请求起始行，这个起始行由方法名，请求 URI 和 HTTP 协议版本组成。HttpClient很好地支持了HTTP/1.1规范中所有的HTTP方法：GET，HEAD， POST，PUT， DELETE， TRACE 和 OPTIONS。每个方法都有一个特别的类：HttpGet，HttpHead， HttpPost，HttpPut，HttpDelete，HttpTrace和HttpOptions。URI是统一资源标识符的缩写，用来标识与请求相符合的资源。HTTP 请求URI包含了一个协议名称，主机名，可选端口，资源路径，可选的参数，可选的片段。    

    HttpGet httpget = new HttpGet(  
         "http://www.google.com/search?hl=en&q=httpclient&btnG=Google+Search&aq=f&oq=");      

HttpClient提供了URIBuilder工具类来简化创建、修改请求 URIs。    


    URI uri = new URIBuilder()  
              .setScheme("http")  
              .setHost("www.google.com")  
              .setPath("/search")  
              .setParameter("q", "httpclient")  
              .setParameter("btnG", "Google Search")  
              .setParameter("aq", "f")  
              .setParameter("oq", "")  
              .build();  
    HttpGet httpget = new HttpGet(uri);  
    System.out.println(httpget.getURI());</span>  

输出:    

    http://www.google.com/search?q=httpclient&btnG=Google+Search&aq=f&oq=  

### 1.1.2 HTTP响应(Response)    

HTTP响应是服务器接收并解析请求信息后返回给客户端的信息,它的起始行包含了一个协议版本,一个状态码和一个描述状态的短语.    


    HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1  
                                 ,HttpStatus.SC_OK, "OK");  
    System.out.println(response.getProtocolVersion());  
    System.out.println(response.getStatusLine().getStatusCode());  
    System.out.println(response.getStatusLine().getReasonPhrase());  
    System.out.println(response.getStatusLine().toString());     

输出:    

    HTTP/1.1  
    200  
    OK  
    HTTP/1.1 200 OK  

### 1.1.3 处理消息头(Headers)    

一个HTTP报文包含许多描述报文内容的报文头,比如内容长度,内容类型等.HttpClient提供了一些方法用来取出,添加,移动,枚举报文头.    

    HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,HttpStatus.SC_OK, "OK");  
    response.addHeader("Set-Cookie","c1=a; path=/; domain=localhost");  
    response.addHeader("Set-Cookie","c2=b; path=\"/\", c3=c; domain=\"localhost\"");  
    Header h1 = response.getFirstHeader("Set-Cookie");  
    System.out.println(h1);  
    Header h2 = response.getLastHeader("Set-Cookie");  
    System.out.println(h2);  
    Header[] hs = response.getHeaders("Set-Cookie");  
    System.out.println(hs.length);    

输出:    

    Set-Cookie: c1=a; path=/; domain=localhost
    Set-Cookie: c2=b; path="/", c3=c; domain="localhost"
    2    

获得所有指定类型首部最有效的方式是使用HeaderIterator接口    

    HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,HttpStatus.SC_OK, "OK");  
    response.addHeader("Set-Cookie","c1=a; path=/; domain=localhost");  
    response.addHeader("Set-Cookie","c2=b; path=\"/\", c3=c; domain=\"localhost\"");  
    HeaderIterator it = response.headerIterator("Set-Cookie");  
    while (it.hasNext()) {  
         System.out.println(it.next());  
    }     

输出:    

    Set-Cookie: c1=a; path=/; domain=localhost
    Set-Cookie: c2=b; path="/", c3=c; domain="localhost"

ttpClient也提供了其他便利的方法吧HTTP报文转化为单个的HTTP元素。    

    HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,HttpStatus.SC_OK, "OK");  
    response.addHeader("Set-Cookie","c1=a; path=/; domain=localhost");  
    response.addHeader("Set-Cookie","c2=b; path=\"/\", c3=c; domain=\"localhost\"");  
    HeaderElementIterator it = new BasicHeaderElementIterator(  
    response.headerIterator("Set-Cookie"));  
    while (it.hasNext()) {  
         HeaderElement elem = it.nextElement();  
         System.out.println(elem.getName() + " = " + elem.getValue());  
         NameValuePair[] params = elem.getParameters();  
         for (int i = 0; i < params.length; i++) {  
              System.out.println(" " + params[i]);  
         }  
    }

输出:    

    c1 = a
    path=/
    domain=localhost
    c2 = b
    path=/
    c3 = c
    domain=localhost    

### 1.1.4 HTTP消息体    

HTTP报文能够携带与请求或者响应相关的消息体.消息体存在于某些请求和响应中,它们是可选的.HTTP规定了两种请求可以包含消息体:PUT和POST.而HTTP响应总是包含消息体(但有些响应不符合这一规则，比如，对HEAD方法的响应和状态为204 No Content, 304 Not Modified, 205 Reset Content的响应).    

依据实体的内容来源，HttpClient区分出三种实体：    
* 流式实体（streamed）：内容来源于一个流，或者在运行中产生【译者：原文为generated on the fly】。特别的，这个类别包括从响应中接收到的实体。流式实体不可重复。    
* 自包含实体（self-contained）：在内存中的内容或者通过独立的连接     
* 包装实体（wrapping）：从另外一个实体中获取内容。    
#### 1.1.4.1 可重复实体    

一个实体能够被重复，意味着它的内容能够被读取多次。它只可能是自包含实体（像ByteArrayEntity或StringEntity）    

#### 1.1.4.2 使用HTTP消息体    

实体能够表现为二进制和字节内容,支持二进制编码.    
实体将会在一些情况下被创建：当执行一个含有内容的请求时或者当请求成功，响应体作为结果返回给客户端时。为了读取实体的内容，可以通过HttpEntity.getContent() 方法取出输入流，返回一个Java.io.InputStream，或者提供一个输出流给HttpEntity.writeTo(OutputStream) 方法，它将会返回写入给定流的所有内容。    

当实体内部含有信息时，使用HttpEntity#getContentType()和HttpEntity#getContentLength()方法将会读取一些基本的元数据，比如Content-Type和Content-Length这样的首部（如果他们可用的话），由于Content-Type首部能够包含文本MIME类型（像 text/plain 或text/html），它也包含了与MIME类型相对应的字符编码，HttpEntity#getContentEncoding()方法被用来读取这些字符编码。如果对应的首部不存在，则Content-Length的返回值为-1，Content-Type返回值为NULL。    

    StringEntity myEntity = new StringEntity("important message",  
                              ContentType.create("text/plain", "UTF-8"));  
    System.out.println(myEntity.getContentType());  
    System.out.println(myEntity.getContentLength());  
    System.out.println(EntityUtils.toString(myEntity));    

输出:    

    Content-Type: text/plain; charset=utf-8
    17
    important message
    17

###１.1.5 确保释放低级别的资源　　　　

为了确保正确的释放系统资源，你必须关掉与实体相关的内容流，还必须关掉响应本身。　　　　

    CloseableHttpClient httpclient = HttpClients.createDefault();  
    HttpGet httpget = new HttpGet("http://localhost/");  
    CloseableHttpResponse response = httpclient.execute(httpget);  
    try {  
         HttpEntity entity = response.getEntity();  
         if (entity != null) {  
            InputStream instream = entity.getContent();  
            try {  
                // do something useful  
            } finally {  
                instream.close();  
            }  
       }  
    } finally {  
         response.close();  
    }　　　　


关闭内容流和关闭响应的不同点是：前者将会通过消费实体内容保持潜在的连接，而后者迅速的关闭并丢弃连接。　　　　　

请注意，一旦实体被HttpEntity#writeTo(OutputStream)方法成功地写入时，也需要确保正确地释放系统资源。如果方法获得通过HttpEntity#getContent()，它也需要在一个finally子句中关闭流。　　　　　

当使用实体时，你可以使用EntityUtils#consume(HttpEntity)方法来确保实体内容完全被消费并且使潜在的流关闭。　　　　

某些情况，整个响应内容的仅仅一小部分需要被取出，会使消费其他剩余内容的性能代价和连接可重用性代价太高，这时可以通过关闭响应来终止内容流。【注意：例子中可以看出，实体输入流仅仅读取了两个字节，就关闭了响应，也就是按需读取，而不是读取全部响应】


    CloseableHttpClient httpclient = HttpClients.createDefault();  
    HttpGet httpget = new HttpGet("http://localhost/");  
    CloseableHttpResponse response = httpclient.execute(httpget);  
    try {  
    HttpEntity entity = response.getEntity();  
    if (entity != null) {  
            InputStream instream = entity.getContent();  
        int byteOne = instream.read();  
            int byteTwo = instream.read();  
        // Do not need the rest  
    }  
    } finally {  
        response.close();  
    }

这样，连接将不会被重用，它分配的所有级别的资源将会被解除。　　　　

### 1.1.6 消费实体内容　　　　

为了消费实体内容，推荐的方式是使用HttpEntity#getContent()或者 HttpEntity#writeTo(OutputStream)方法。HttpClient也提供了一个EntityUtils类，它有几个静态方法更容易的从实体中读取内容或信息。取代了直接读取java.io.InputStream，你可以通过这个类的方法取出全部内容体并放入一个String 中或者byte数组中。可是，强烈不建议使用EntityUtils，除非响应实体来自于信任的HTTP服务器并且知道它的长度。　　　　

    CloseableHttpClient httpclient = HttpClients.createDefault();  
    HttpGet httpget = new HttpGet("http://localhost/");  
    CloseableHttpResponse response = httpclient.execute(httpget);  
    try {  
    HttpEntity entity = response.getEntity();  
    if (entity != null) {  
            long len = entity.getContentLength();  
            if (len != -1 && len < 2048) {  
                 System.out.println(EntityUtils.toString(entity));  
        } else {  
            // Stream content out  
        }  
    }  
    } finally {  
        response.close();  
    }

在某些情况下，多次读取实体内容是必要的。在这种情况下，实体内容必须以一些方式缓冲，内存或者硬盘中。为了达到这个目的，最简单的方法是把原始的实体用BufferedHttpEntity类包装起来。这将会使原始实体的内容读入一个in-memory缓冲区。所有方式的实体包装都是代表最原始的那个实体。　　　

    CloseableHttpResponse response = <...>  
    HttpEntity entity = response.getEntity();  
    if (entity != null) {  
        entity = new BufferedHttpEntity(entity);  
    }

### 1.1.7 生产实体内容　　　　

HttpClient提供了几个类，用于通过HTTP连接高效的传输内容．这几个类均与包含消息体的请求（PUT和POST）有关,它们能把实体内容友好的封装进HTTP请求中.对于基本的数据容器:String,byte array,intput stream ,and file.HttpClient为它们提供了几个对应的类StringEntity,ByteArrayEntity,InputStreamEntity,and FileEntity.    

    File file = new File("somefile.txt");  
    FileEntity entity = new FileEntity(file,ContentType.create("text/plain", "UTF-8"));  
    HttpPost httppost = new HttpPost("http://localhost/action.do");  
    httppost.setEntity(entity);      

请注意InputStreamEntity是不可重复的，因为它仅仅能够从数据流中读取一次。一般建议实现一个定制的HttpEntity类来代替使用一般的InputStreamEntity。FileEntity将会是一个好的出发点。    

#### 1.1.7.1 HTML表单    

许多应用需要模仿一个登陆HTML表单的过程，比如：为了登陆一个web应用或者提交输入的数据。HttpClient提供了UrlEncodedFormEntity类来简化这个过程。    


    List<NameValuePair> formparams = new ArrayList<NameValuePair>();  
    formparams.add(new BasicNameValuePair("param1", "value1"));  
    formparams.add(new BasicNameValuePair("param2", "value2"));  
    UrlEncodedFormEntity entity = new UrlEncodedFormEntity(formparams,  
                                                              Consts.UTF_8);  
    HttpPost httppost = new HttpPost("http://localhost/handler.do");  
    httppost.setEntity(entity);     

UrlEncodedFormEntity实例像上面一样使用URL编码方式来编码参数并生成下面的内容：
    param1=value1&param2=value2    

#### 1.1.7.2 内容分块    

通常，我们推荐让HttpClient选择基于被传递的HTTP报文属相最合适的传输编码方式。可能地，可以通过设置HttpEntity#setChunked()为true来通知HttpClient你要进行分块编码。注意HttpClient将会使用这个标志作为提示。当使用一些不支持分块编码的HTTP版本（比如HTTP/1.0.）时，这个值将会忽略。    

    StringEntity entity = new StringEntity("important message",  
                               ContentType.create("plain/text", Consts.UTF_8));  
    entity.setChunked(true);  
    HttpPost httppost = new HttpPost("http://localhost/acrtion.do");  
    httppost.setEntity(entity);      

### 1.1.8 响应处理器    

最简单、最方便的方式来处理响应是使用ResponseHandler接口，它包含了一个handleResponse(HttpResponse response)方法。这个方法减轻使用者对于连接管理的担心。
当你使用ResponseHandler时，无论是请求执行成功亦或出现异常，HttpClient将会自动地确保连接释放回连接管理器中    

    CloseableHttpClient httpclient = HttpClients.createDefault();  
    HttpGet httpget = new HttpGet("http://localhost/json");  
    ResponseHandler<MyJsonObject> rh = new ResponseHandler<MyJsonObject>() {  
        @Override  
        public JsonObject handleResponse(final HttpResponse response) throws IOException {  
            StatusLine statusLine = response.getStatusLine();  
            HttpEntity entity = response.getEntity();  
            if (statusLine.getStatusCode() >= 300) {  
                throw new HttpResponseException(statusLine.getStatusCode(),  
                    statusLine.getReasonPhrase());  
            }  
            if (entity == null) {  
                 throw new ClientProtocolException("Response contains no content");  
            }  
            Gson gson = new GsonBuilder().create();  
            ContentType contentType = ContentType.getOrDefault(entity);  
            Charset charset = contentType.getCharset();  
            Reader reader = new InputStreamReader(entity.getContent(), charset);  
            return gson.fromJson(reader, MyJsonObject.class);  
        }  
    };  
    MyJsonObject myjson = client.execute(httpget, rh);  
