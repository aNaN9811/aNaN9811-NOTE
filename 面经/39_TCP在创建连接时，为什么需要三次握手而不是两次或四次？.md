# TCP在创建连接时，为什么需要三次握手而不是两次或四次？



一个简单的例子：

- `三次握手`：
   “喂，你听得到吗？”
   “我听得到呀，你听得到我吗？”
   “我能听到你，今天balabala……”
- `两次握手`：
   “喂，你听得到吗？”
   “我听得到呀，你听得到我吗？”
   “喂，你听得到吗？”
   “……谁在说话？”
   “喂，你听得到吗？”
   “……”
- `四次握手`：
   “喂，你听得到吗？”
   “我听得到呀”“你能听到我吗？”
   “……不想跟傻逼说话”

总结：两次握手会可能导致已失效的连接请求报文段突然又传送到了服务端产生错误，四次握手又太浪费资源