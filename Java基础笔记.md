# Java基础笔记



## 1.javaAPI

[彻底搞懂 ThreadLocal](https://mp.weixin.qq.com/s/F4in6CDuZV-C130QBgXs3Q)

[解读ThreadLocal的实现原理及应用场景 - 知乎 (zhihu.com)](https://www.zhihu.com/zvideo/1305267261868552192)

[面试官问我：你确定用了BigDecimal后，计算结果一定精确？](https://mp.weixin.qq.com/s/xU4VZN2r7WsERqudTz5apQ)

[如何优雅的使用线程池！！](https://mp.weixin.qq.com/s/WvaKRznQDXDVdEEo5v-R_w)

[Java 线程池配置的常见误区 ](https://mp.weixin.qq.com/s?__biz=Mzg5MDEzMjEwNw==&mid=2247495151&idx=3&sn=15172f19286693f79e37461aff01c145&chksm=cfe3ee0bf894671dcc393505a6e24c6183a9aea8c052cd695be8e4fc16b7dd53a6fd0f7ae1fe&scene=132#wechat_redirect)

[面试被问，一千万个整数里面快速查找某个整数，你会怎么去做？](https://mp.weixin.qq.com/s/Ljygh8B9XEqywINtk1tGbw)

[javaIo](https://mp.weixin.qq.com/s/J_11wYusexAjwa1cuckyfw)

## 2.问题记录

多个set求并集：flatMap

```java
Set<String> set = Arrays.asList("1", "2", "3").stream().collect(Collectors.toSet());
Set<String> set1 = Arrays.asList("3", "4", "5").stream().collect(Collectors.toSet());
Set<String> collect = Stream.of(set, set1).flatMap(Set::stream).collect(Collectors.toSet());
System.out.println("collect = " + collect); //[1, 2, 3, 4, 5]
```

