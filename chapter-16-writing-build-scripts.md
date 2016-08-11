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

| 名称 | 类型 | 默认值 |
| :--- | :--- | :--- |
| project | Project | Project实例 |
| name | String | 项目目录名 |
| path | String | 项目的绝对路径 |
| description | String | 项目的描述信息 |
| projectDir | File | 包含构建脚本的目录 |
| buildDir | File | projectDir\/build |
| group | Object | unspecified |
| version | Object | unspecified |
| ant | AntBuilder | AntBuilder实例 |

# 16.3 Script API

当Gradle执行构建脚本时，它将脚本编译成实现了`Script`的类文件，这就是说定义在Script接口中的属性和方法也可以在脚本中访问。

# 16.4 变量定义

构建脚本中可以定义两种类型的变量：局部变量和额外变量。

## 16.4.1 局部变量
`def`关键字可以用来定义局部变量，这有在当前的范围中才可以访问，局部变量是Groovy语言的一大特点。

Example 16.2 使用局部变量
```
def dest = "dest"
task copy(type:Copy){
    from "source"
    into "dest"
}
```
##  16.4.2 额外变量

Gradle对象模型中可以使用用户额外定义的属性。包括，但不限制与projects，tasks,sourceSets。额外属性可以被添加，读取，设置通过所持有对象的`ext`属性，除此之外，一个`ext`代码块可以一次添加多个属性。

Example 16.3 使用额外变量
```
apply plugin: "java"
ext{
    springVersion = "3.1.0.RELEASE"
    emailNotification = "build@master.org"
}


sourceSets.all(ext.purpose=null)

sourceSets{
    main{
        purpose = "production"
    }
    test{
        purpose = "test"    
    }
    plugin{
        purpose = "production"
    }
}

task printProperties{
    println springVersion
    println emailNotification
    sourceSets.matching{it.purpose = "production"}
            .each{pringln it.name}
}
```

`gradle -q` 输出结果：
```
> gradle -q printProperties
3.1.0.RELEASE
build@master.org
main
plugin
```
本例中，ext代码块添加了两个额外的属性到project对象，另外，`purpose`属性被添加到每一个source set中，使用`ext.purpose to null`. 


#16.5 配置任意的对象

Example 16.4. Configuring arbitrary objects
build.gradle
```
task configure << {
 def pos = configure(new java.text.FieldPosition(10)) {
 beginIndex = 1
 endIndex = 5
 }
 println pos.beginIndex
 println pos.endIndex
}
```

Output of gradle -q configure
```
> gradle -q configure
1
5
```

##16.6 使用ext脚本配置任意对象

build.gradle
```
task configure << {
 def pos = new java.text.FieldPosition(10)
 // Apply the script
 apply from: 'other.gradle', to: pos
 println pos.beginIndex
 println pos.endIndex
}
```
other.gradle
```
// Set properties.
beginIndex = 1
endIndex = 5
```
Output of gradle -q configure
```
> gradle -q configure
1
5
```


#16.7 一些Groovy基础

##16.7.1 Groovy JDK
Groovy 在java的基础上添加了很多有用的方法，例如，迭代
```
// Iterable gets an each() method
configurations.runtime.each { File f -> println f }
```
##16.7.2 属性访问器
Groovy可以自动转换对象到合适的对象
```
// Using a getter method
println project.buildDir
println getProject().getBuildDir()

// Using a setter method
project.buildDir = 'target'
getProject().setBuildDir('target')
```
#16.8默认导的包
为了方便使用，Gardle自动添加了一系列包声明。也就意味着可以任意使用这些对象。如下列举了一些比较关键的。

```
import org.gradle.*
import org.gradle.api.*
import org.gradle.api.artifacts.*
import org.gradle.api.artifacts.cache.*
import org.gradle.api.artifacts.component.*
import org.gradle.api.artifacts.dsl.*
import org.gradle.api.artifacts.ivy.*
import org.gradle.api.artifacts.maven.*
import org.gradle.api.artifacts.query.*
import org.gradle.api.artifacts.repositories.*
import org.gradle.api.artifacts.result.*
import org.gradle.api.component.*
import org.gradle.api.credentials.*
import org.gradle.api.distribution.*
import org.gradle.api.distribution.plugins.*
import org.gradle.api.dsl.*
import org.gradle.api.execution.*
import org.gradle.api.file.*
import org.gradle.api.initialization.*
import org.gradle.api.initialization.dsl.*
import org.gradle.api.invocation.*
import org.gradle.api.java.archives.*
import org.gradle.api.logging.*
import org.gradle.api.logging.configuration.*
import org.gradle.api.plugins.*
import org.gradle.api.plugins.announce.*
import org.gradle.api.plugins.antlr.*
import org.gradle.api.plugins.buildcomparison.gradle.*
import org.gradle.api.plugins.jetty.*
import org.gradle.api.plugins.osgi.*
import org.gradle.api.plugins.quality.*
import org.gradle.api.plugins.scala.*
import org.gradle.api.plugins.sonar.*
import org.gradle.api.plugins.sonar.model.*
import org.gradle.api.publish.*
import org.gradle.api.publish.ivy.*
import org.gradle.api.publish.ivy.plugins.*
import org.gradle.api.publish.ivy.tasks.*
import org.gradle.api.publish.maven.*
import org.gradle.api.publish.maven.plugins.*
import org.gradle.api.publish.maven.tasks.*
import org.gradle.api.publish.plugins.*
import org.gradle.api.reporting.*
import org.gradle.api.reporting.components.*
import org.gradle.api.reporting.dependencies.*
import org.gradle.api.reporting.model.*
import org.gradle.api.reporting.plugins.*
import org.gradle.api.resources.*
import org.gradle.api.specs.*
import org.gradle.api.tasks.*
import org.gradle.api.tasks.ant.*
import org.gradle.api.tasks.application.*
import org.gradle.api.tasks.bundling.*
import org.gradle.api.tasks.compile.*
import org.gradle.api.tasks.diagnostics.*
import org.gradle.api.tasks.incremental.*
import org.gradle.api.tasks.javadoc.*
import org.gradle.api.tasks.scala.*
import org.gradle.api.tasks.testing.*
import org.gradle.api.tasks.testing.junit.*
import org.gradle.api.tasks.testing.testng.*
import org.gradle.api.tasks.util.*
import org.gradle.api.tasks.wrapper.*
import org.gradle.authentication.*
import org.gradle.authentication.http.*
import org.gradle.buildinit.plugins.*
import org.gradle.buildinit.tasks.*
import org.gradle.external.javadoc.*
import org.gradle.ide.cdt.*
import org.gradle.ide.cdt.tasks.*
import org.gradle.ide.visualstudio.*
import org.gradle.ide.visualstudio.plugins.*
import org.gradle.ide.visualstudio.tasks.*
import org.gradle.ivy.*
import org.gradle.jvm.*
import org.gradle.jvm.application.scripts.*
import org.gradle.jvm.application.tasks.*
import org.gradle.jvm.platform.*
import org.gradle.jvm.plugins.*
import org.gradle.jvm.tasks.*
import org.gradle.jvm.tasks.api.*
import org.gradle.jvm.test.*
import org.gradle.jvm.toolchain.*
import org.gradle.language.assembler.*
import org.gradle.language.assembler.plugins.*
import org.gradle.language.assembler.tasks.*
import org.gradle.language.base.*
import org.gradle.language.base.artifact.*
import org.gradle.language.base.plugins.*
import org.gradle.language.base.sources.*
import org.gradle.language.c.*
import org.gradle.language.c.plugins.*
import org.gradle.language.c.tasks.*
import org.gradle.language.coffeescript.*
import org.gradle.language.cpp.*
import org.gradle.language.cpp.plugins.*
import org.gradle.language.cpp.tasks.*
import org.gradle.language.java.*
import org.gradle.language.java.artifact.*
import org.gradle.language.java.plugins.*
import org.gradle.language.java.tasks.*
import org.gradle.language.javascript.*
import org.gradle.language.jvm.*
import org.gradle.language.jvm.plugins.*
import org.gradle.language.jvm.tasks.*
import org.gradle.language.nativeplatform.*
import org.gradle.language.nativeplatform.tasks.*
import org.gradle.language.objectivec.*
import org.gradle.language.objectivec.plugins.*
import org.gradle.language.objectivec.tasks.*
import org.gradle.language.objectivecpp.*
import org.gradle.language.objectivecpp.plugins.*
import org.gradle.language.objectivecpp.tasks.*
import org.gradle.language.rc.*
import org.gradle.language.rc.plugins.*
import org.gradle.language.rc.tasks.*
import org.gradle.language.routes.*
import org.gradle.language.scala.*
import org.gradle.language.scala.plugins.*
import org.gradle.language.scala.tasks.*
import org.gradle.language.scala.toolchain.*
import org.gradle.language.twirl.*
import org.gradle.maven.*
import org.gradle.model.*
import org.gradle.nativeplatform.*
import org.gradle.nativeplatform.platform.*
import org.gradle.nativeplatform.plugins.*
import org.gradle.nativeplatform.tasks.*
import org.gradle.nativeplatform.test.*
import org.gradle.nativeplatform.test.cunit.*
import org.gradle.nativeplatform.test.cunit.plugins.*
import org.gradle.nativeplatform.test.cunit.tasks.*
import org.gradle.nativeplatform.test.googletest.*
import org.gradle.nativeplatform.test.googletest.plugins.*
import org.gradle.nativeplatform.test.plugins.*
import org.gradle.nativeplatform.test.tasks.*
import org.gradle.nativeplatform.toolchain.*
import org.gradle.nativeplatform.toolchain.plugins.*
import org.gradle.platform.base.*
import org.gradle.platform.base.binary.*
import org.gradle.platform.base.component.*
import org.gradle.platform.base.plugins.*
import org.gradle.play.*
import org.gradle.play.distribution.*
import org.gradle.play.platform.*
import org.gradle.play.plugins.*
import org.gradle.play.plugins.ide.*
import org.gradle.play.tasks.*
import org.gradle.play.toolchain.*
import org.gradle.plugin.devel.*
import org.gradle.plugin.repository.*
import org.gradle.plugin.use.*
import org.gradle.plugins.ear.*
import org.gradle.plugins.ear.descriptor.*
import org.gradle.plugins.ide.api.*
import org.gradle.plugins.ide.eclipse.*
import org.gradle.plugins.ide.idea.*
import org.gradle.plugins.javascript.base.*
import org.gradle.plugins.javascript.coffeescript.*
import org.gradle.plugins.javascript.envjs.*
import org.gradle.plugins.javascript.envjs.browser.*
import org.gradle.plugins.javascript.envjs.http.*
import org.gradle.plugins.javascript.envjs.http.simple.*
import org.gradle.plugins.javascript.jshint.*
import org.gradle.plugins.javascript.rhino.*
import org.gradle.plugins.javascript.rhino.worker.*
import org.gradle.plugins.signing.*
import org.gradle.plugins.signing.signatory.*
import org.gradle.plugins.signing.signatory.pgp.*
import org.gradle.plugins.signing.type.*
import org.gradle.plugins.signing.type.pgp.*
import org.gradle.process.*
import org.gradle.sonar.runner.*
import org.gradle.sonar.runner.plugins.*
import org.gradle.sonar.runner.tasks.*
import org.gradle.testing.base.*
import org.gradle.testing.base.plugins.*
import org.gradle.testing.jacoco.plugins.*
import org.gradle.testing.jacoco.tasks.*
import org.gradle.testkit.runner.*
import org.gradle.util.*
```








