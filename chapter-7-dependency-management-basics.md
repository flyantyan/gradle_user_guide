本章介绍一些Gradle基础的依赖管理方面的知识。

## 7.1 什么是依赖管理

大致来说，依赖管理主要分为两个部分。首先，Gradle需要了解你项目所需要构建和运行的信息，依赖管理可以用来帮助找到这些信息。我们把传入这些信息的文件叫做项目的依赖。其次，Gradle需要去构建，上传项目所产出的文件。接下来详细的了解一下这两部分的内容。

大多数项目并不是完全的自给自足的，在编译或测试的过程中需要其他的项目所构建的文件。例如，在项目中如果使用Hibernate框架，我需要包含Hibernate的jar包当编译代码的时候。为了运行测试用例，我也需要包含一些额外的jar包，例如，JDBC驱动或Ehchche 库。

这些传入的文件就是项目的依赖。Gradle允许你告诉它项目的依赖关系，那么Gradle就会找到这些依赖关系，以便在构建时使用。依赖的来源可能是从远程Maven或者lvy仓库下载的，或者存储在本地目录，或者多项目中其他项目所构建的。我们把这个过程叫做『依赖解决』\(dependency resolution\)

Gradle的这个特点相对于Ant有很多的优势。使用Ant构建时，我们只能在项目中指定依赖相对路径或绝对路径。但是使用Gradle，所需要指定的仅仅是依赖的『名称』，Gradle的其他层就会判断去哪里获取这些依赖。当然，你也可以使用Apache lvy 在Ant中实现相同的行为，但是Gradle比这个做的更好。

大多数情况下，项目的依赖也会有自己的依赖关系。例如，Hibernate核心库需要一些其他的库文件。所以，当Gradle构建项目时，也会找到这些依赖所依赖的。我们把这个叫做『传递依赖\(transitive dependencies\)』

大多数项目的主要目的是构建可供外部项目使用的文件，例如，如果你的项目产出了Java库，你需要构建一个jar包，可能也有源码包，文档，然后发布这些信息。

Gradle也会帮你处理项目的发布。你声明项目的发布信息，Gradle就会构建、发布。明确『发布』意味着依赖于你想做什么。你可能想复制文件到本地目录，或者上传到远程Maven 、Lvy仓库，或者想要在多项目构建中使用这些文件，这个过程叫做『发布』。

## 7.2 依赖的声明

下面看一些基本的依赖声明。

Example 7.1 声明依赖

build.gradle

```
apply plugin:'java'
repositories{
    mavenCentarl()
}
dependencies{
    compile group: 'org.hibernate', name:'hibernate-core',version:'3.6.7.Final'
    testCompile group: 'junit' ,name:'junit' , version:'4.+'
}
```

上面构建脚本表达什么？构建脚本描述了项目的一些信息。首先，陈述了编译时需要Hibernate core 3.6.7.Final依赖库，其中Hibernate所依赖的也会包含进来。其次说明课编译测试需要 jnuit&gt;=4.0的库。最后告诉Gradle在Maven 中心仓库找寻这些依赖信息，接下来会详细论述。

## 7.3 依赖配置

Gradle的依赖也是通过配置项来组织的，一个配置项就是依赖的名称。我们把这个称作依赖配置『Dependcency Configurations』。你可以使用其来定义项目中额外的依赖关系，之后也会看到，也可以用来定义项目的发布内容。

Java 插件定义了一些标准的配置项，这些配置项代表Java插件所使用的类路径。下面列举一些，详细的内容可以在『Chapter 45,5 Java plugin-dependency configurations』。

**compile**

    编译项目源码所需要的依赖关系
**runtime**
    
    运行时所需要的依赖，默认情况下，包含了编译时依赖。
**testCompile**

    

