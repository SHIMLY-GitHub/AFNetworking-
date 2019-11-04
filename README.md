#  由浅入深解读AFNetworking

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;自从**ASIHttpRequest** 放弃维护以后，**AFNetworking** 就扛起了 iOS端网络请求的大旗。 只要你的应用有网络请求， 基本上是绕不开这个库了。这几天仔细研究了一下AF的源代码 打算写一篇文章来记录、整理、总结一下。这也是我的第一篇技术文章，希望能通过这样一种方式 ，来锻炼自己的文字表达能力。



## AFNetworking 文件结构

我认为在阅读优秀的源代码之前， 一定要捋一遍文件结构，对源码有一个基本的认知。 这样有助于提高阅读代码的效率。


> **NSURLSession**

* ```AFURLSessionManager```	 用于管理```NSURLSession```的类
* ```AFHTTPSessionManager```    继承于``AFURLSessionManager``，该类负责发起 http请求 ``post``、``get``、``delete``等

> **Serializing**

* ``AFURLRequestSerialization ``    序列化请求数据)
  * ``AFJSONRequestSerializer`` 把请求参数序列化为json格式(默认格式)
  * ``AFPropertyListRequestSerializer  ``  把请求参数序列化为list格式
* ``AFHTTPResponseSerializer``     序列化响应数据
  * ``AFJSONResponseSerializer``  序列化为json类型的数据 也是AF默认的响应数据类型
  * `` AFXMLParserResponseSerializer ``  序列为xmlParser类型(反正自从我开始写代码 就已经用json xml就已经不被使用 感兴趣的同学可以自己研究下这两种格式)[http://commons.apache.org/proper/commons-jelly/apidocs/org/apache/commons/jelly/parser/XMLParser.html]()
  * ``AFXMLDocumentResponseSerializer``  [https://developer.mozilla.org/zh-CN/docs/Web/API/XMLDocument)
]()
  * `` AFPropertyListResponseSerializer `` 搞iOS开发的同学对于plist文件格式应该不会陌生 也是一种key value 的键值对存储方式
  *  `` AFImageResponseSerializer `` 如果响应类型是图片类型(tiff,jpeg,gif,png,ico,x-icon,bmp,x-bmp,x-xbitmap/x-win-bitmap) 这个估计也很少会用到 因为加载网络图片 可能我们用的更多是 SDWebImage 或 YYWebImage
  *  `` AFCompoundResponseSerializer ``  如果服务器返回类型 不止一种 那么可以使用这个进行序列化  

> **Reachability **

* ``AFNetworkReachabilityManager``  网络检测相关，比如要检测当前是否有网络链接,是蜂窝移动网络还是wifi 等等

> **AFSecurityPolicy**

* ``AFSecurityPolicy`` 网络安全相关

   

## Http协议简单、基本的认识

  如果你对http协议已经了如指掌了， 那么关于这个就不要再看了。如果你对http协议认识 仅限于 http://xxxx.com 那么我建议，还是要看一下。可能有同学会问我是搞iOS的 有必要了解这些么？我不知道，好像也不影响我写代码啊。我认为，对http协议有一个最基本，最简单的认识， 有助于解读AF的源代码。