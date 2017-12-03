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


