# Cookie

**1.用途**  
Cookie意为“甜饼”，是由W3C组织提出，最早由Netscape社区发展的一种机制。目前Cookie已经成为一种标准，所有的主流浏览器都支持Cookie。
浏览器大多数时候通过HTTP协议进行信息传输，而HTTP是一种无状态的协议，服务器端单从网络连接上无从知道客户身份，因此服务器端会给客户端（浏览器）返回一些信息（一般为jsessionID）存储在Cookie中，在以后访问服务器时都必须携带这些信息，这样服务器就能通过Cookie来确定访问者的身份。
由于浏览器每次请求服务器都会携带Cookie，因此Cookie内容不宜过多，否则影响速度。  
**2.JavaWeb操作Cookie**  
通过request.getCookie()可以获取客户端发送的请求中的所有Cookie（以Cookie[]数组形式返回），通过 *response.addCookie(Cookiecookie)* 向客户端设置Cookie。  
**3.JavaScript操作Cookie**  
Cookie是保存在浏览器端的，因此浏览器具有操作Cookie的先决条件。浏览器可以使用脚本程序如JavaScript等操作Cookie。例如下面的代码会输出本页面所有的Cookie。

    <script>document.write(document.cookie);</script>

同时Cookie的不可跨域名性保证了A网站的JavaScript程序读写B网站的Cookie不会有任何结果。  
**4.不可跨域名性**  
客户端会访问很多不同域名的网站，这些网站在客户端可能都会生成一些Cookie，Cookie的不可跨域名性是指浏览器在访问www.A.com时只会携带A网站在客户端生成的Cookie，B网站无法操作到A网站在客户端生成的Cookie。
Cookie在客户端是由浏览器来管理的,通过Cookie的这种不可跨域名性从而保证用户的隐私安全。  
**5.Cookie的安全属性**  
HTTP协议不仅是无状态的，而且是不安全的。使用HTTP协议的数据不经过任何加密就直接在网络上传播，有被截获的可能。如果不希望Cookie在HTTP等非安全协议中传输，可以设置Cookie的secure属性为true，浏览器只会在HTTPS和SSL等安全协议中传输此类Cookie。下面为Java代码的演示：

    Cookie cookie = new Cookie("time", "20080808");   // 新建Cookie
    cookie.setSecure(true);                           // 设置安全属性
    response.addCookie(cookie);                       // 输出到客户端

提示：secure属性并不能对Cookie内容加密，因而不能保证绝对的安全性。如果需要高安全性，需要在程序中对Cookie内容加密、解密，以防泄密。


# Session

**1.简介及用途**  
在WEB开发中，服务器端可以为每个用户浏览器创建一个会话对象（session对象），默认情况下一个浏览器独占一个session对象。在创建出session对象的同时会返回给客户端一个JSessionID保存在Cookie中，以后再访问服务器端都会携带这个ID用于识别请求者的身份。  
session中也可以保存用户的一些信息，比如用户登录成功后，可以从数据库中把用户信息读取出来存入session中，以后后台代码可以直接从session中读取来提高效率，降低从数据库中读取数据的性能消耗。  
对于JavaWeb程序，session是由tomcat来进行管理的，一般来说只要请求到后台就会自动创建一个对应的session。在代码中调用request.getSession(true)可以根据HTTP请求中的JsessionID获取到对应的session对象或者强制创建session对象  

**2.Session和Cookie区别**  

 - Cookie保存在客户端（浏览器）
 - Session保存在服务器端（tomcat管理的一块内存或用于session持久化的工具中）
 - Session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果考虑到减轻服务器性能，应当尽量使用Cookie
 - Cookie不是很安全，其他人可以分析存放在本地的Cookie并进行Cookie欺骗

**3.Session的生命周期**  
Session保存在服务器端，为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session，如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。  
Session在用户第一次访问服务器的时候自动创建但如果只访问HTML、IMAGE等静态资源并不会创建Session（使用框架对静态资源进行权限限制时除外），Session生成后，只要用户继续访问，服务器就会不断更新Session的最后访问时间，并维护该Session。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session活跃了一次。  
Session的超时时间可以在web.xml中配置，一旦session处于不活跃状态的时间超过这个配置，session就会失效，这个时候如果用户又进行了访问后台会重新创建session对象。不过一般项目会通过框架设置一个Filter，一旦发现请求对应的session对象为空，就会将用户跳转到登录页面重新登录。  
通过调用Session的invalidate()方法可以使Session失效。  
**3.Cookie禁用**  
URL地址重写是对客户端不支持Cookie的解决方案。URL地址重写的原理是将该用户Session的id信息重写到URL地址中，服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。如下代码所示

    <a href="<%=response.encodeURL("index.jsp?c=1&wd=Java") %>"> 

该方法会自动判断客户端是否支持Cookie，如果客户端支持Cookie，会将URL原封不动地输出来，如果客户端不支持Cookie，则会将用户Session的id重写到URL中。但这是JSP页面进行页面重写的实现方法，暂时没找到静态H5页面如何进行url重写。  
注：TOMCAT判断客户端浏览器是否支持Cookie的依据是请求中是否含有Cookie。    
**4.Session共享（Redis）**  
redis是一个key-value存储系统，数据直接存储在内存中，非常适合用于持久化session。这里的session共享指的是不同服务间共享同一套session对象，而不是多台服务器间的session共享（当然也可以这样做）。  
当服务器端是由多个服务组成（微服务或项目融合）的时候，session共享就非常有意义了，如果不使用session共享，每个服务都会创建自己的session用于维持和客户端的连接，这可能导致session的维护和单点登录的实现异常麻烦。比如服务器端是由A、B两个项目组成，但对于客户端来说时同一套页面，用户初次访问这个页面时，先向A项目发送了一个HTTP请求，结果A项目检测到用户对应的session为空，这个时候就会重定向到cas登录页面，登录成功后A项目会维护一个session对象，这时用户又通过页面向B项目发送了一个HTTP请求，B项目又会重复一遍上述流程，这显然不是我们想要的效果。而通过Redis可以实现多个服务共享同一个用户的session对象，前提是这些服务部署在同一个域内，不会涉及Cookie的不可跨域性问题。  
实现的方法很简单，只需要两步：  

 - 第一步：先下载jar包，需要下载的jar包有以下几种：
   commons-pool2-2.4.2.jar、jedis-2.6.1.jar、tomcat-redis-session-manager.jar这三个jar包，然后将这些jar包放到tomcat目录下的lib目录里，注意是在tomcat的lib里面不是所谓的应用系统的lib里面
 - 第二步：修改tomcat目录下的conf目录中的context.xml文件，将以下内容放在标签里面：

     < Manager 
    className="com.rediadesign.catalina.session.RedisSessionHandlerValue" 
    hostName="localhost" 
    port="6379" 
    database="0"
    maxInactiveInterval="60" />

还有一种功能更强大的实现方法，使用Spring Session+Redis来实现session共享，这里不做示范  


