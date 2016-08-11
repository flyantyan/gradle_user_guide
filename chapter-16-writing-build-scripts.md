# 16.1 The Gradle build language

Gradle使用领域特定语言\(DSL\)。构建语言是基于Groovy，在此基础上添加了额外的信息来使描述构建脚本更容易。构建脚本可以包含任意的Groovy语法，另外，Gradle构建脚本默认是使用UTF-8保存的。

# 16.2 The Project API

在之前的介绍中，我们使用了`apply()`方法，那么这个方法是从哪里来的？对于Gradle构建的每一个项目，Gradle创建了`Project`类型的对象，然后将这个对象与构建脚本关联起来。随着构建脚本的执行，就是在配置`Project`对象:
* 构建脚本中使用构建脚本未定义的方法，都委托给`Project`对象。
* 构建脚本中使用未定义的属性，也委托给`Project`对象。

我们可以做些练习来访问Project对象的属性。

Example 16.1 访问Project对象的属性

**build.gradle**
```
println name
println project.name
```
执行`gradle -q check`的输出结果
```
>gradle -q check
projectApi
projectApi
```

两个println声明打印出的是相同的属性，前者使用自动委托给Project对象，对于没有在构建脚本中定义的属性。后者使用在任意构建脚本中都可以访问的project属性，就会获得与之构建脚本相关联的Project对象。只有当你定义了与Project中相同的属性或方法，否则不建议使用project属性。

## 16.2.1 Project的属性
Project对象提供了许多可以在构建脚本中使用的属性，如下列举了一些常用的属性。

**Table 16.1 Project属性**

|名称|类型|默认值|
|--|--|--|
|project|Project|Project实例|
|name|String|项目目录名|
|path|String|项目的绝对路径|
|description|String|项目的描述信息|
|projectDir|File|包含构建脚本的目录|
|buildDir|File|projectDir/build|
|group|Object|unspecified|
|version|Object|unspecified|
|ant|AntBuilder|AntBuilder实例|

