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
