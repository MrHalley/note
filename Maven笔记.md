# Maven笔记

## 标签及属性

1. [Maven依赖的type标签pom属性配合scope标签import属性]([(8条消息) Maven依赖的type标签pom属性配合scope标签import属性_baidu_36124158的博客-CSDN博客](https://blog.csdn.net/baidu_36124158/article/details/103988630))

2. [Maven 中optional true和 scope provided之间的区别](https://segmentfault.com/a/1190000019266080)



## 错误信息
### 依赖报红

1.今天遇到一个很奇怪的现象就是maven中ribbonappclient模块引用commons模块的User类，依赖引用报红，检查了都是对的。最后重新命名User包的包名，再改回来就解决了。

![image-20210909142537069](https://typora-images-repository.oss-cn-beijing.aliyuncs.com/typora-img/image-20210909142537069.png)



## 命令

### 入库命令：install

![image-20210918173721521](https://typora-images-repository.oss-cn-beijing.aliyuncs.com/typora-img/image-20210918173721521.png)
