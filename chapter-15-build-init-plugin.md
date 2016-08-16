```
请注意：构建初始化插件刚刚形成，DSL和其他的配置在随后版本中可能发生改变
```

Gradle初始化插件用于引导创建Gradle 构建进程的创建，支持将其他类型的构建\(Maven\)创建成Gradle构建。Gradle中的插件在使用之前必须在build.gradle中声明`apply:$PLUGIN_NAME`，但是构建初始化插件是一个自动应用的插件，意味着你不需要显示的apply。如果想要运行此插件，仅需要执行init任务即可，不需要为了应用此插件创建build.gradle。本插件的运行也会影响到Wrapper插件，从而执行wrapper任务，这也是说，运行之后Wrapper就被安装到项目中去了。

# 15.1 Tasks

init插件添加了如下的任务

Table 15.1 Build init plugin -tasks

| Task name | Depends on | Type | Description |
| --- | --- | --- | --- |
| init | wrapper | InitBuild | 生成Gradle项目 |
| wrapper | - | Wrapper | 生成wrapper文件 |


# 15.2 What to set up

init支持不同的启动类型，可以通过参数 `--type`来指定。例如:创建java库工程可以执行“gradle init --type java-library”。如果参数没有指定，就会根据工程的环境来推断。例如，如果看到了'pom.xml'就会将其转换成Gradle构建，如果无法推断，就会使用默认的参数`--type base`。所有类型都包括Gradle Wrapper的启动。

# 15.3 Build init types

```
由于此插件还在开发完善中，所以只支持一些类型。未来的版本会支持更多的类型。
```

## 15.3.1 "pom"\(基于Maven约定\)

具有如下特点：

* Uses effective POM and effective settings \(support for POM inheritance, dependency management, properties\) 
* 支持单模块或多模块项目
* 支持自定义模块名称（与目录名称不同）
* 生成元数据：id、description、version
* 应用"maven"、"java"、"war"插件
* 如果需要支持打包成war或jar
* 生成依赖
* 下载仓库
* 可以调整Java编译设置
* 支持源码、测试文件打包
* 支持TestNG
* Generates global exclusions from Maven enforcer plugin settings 

## 15.3.2. "java-library"

"java-library"无法根据环境推断出来，必须显示指定，具有如下优点：

* 使用"java"插件
* 使用"jcenter"依赖仓库
* 使用JUnit测试框架
* 在约定目录有源码
* 包含简单的类以及单元测试文件，如果没有源码或测试文件

可以用`--test-frameword`参数指定测试框架类型，如下:

```
gradle init --type java-library --test-framework spock: Uses Spock for testing instead of JUnit
gradle init --type java-library --test-framework testng: Uses TestNG for testing instead of JUnit
```

## 15.3.3. "scala-library"

"scale-library"无法根据环境推断出来，必须显示指定。具有以下优点:

* 使用"scale"插件
* 使用"jcenter"依赖仓库
* 使用Scale 2.10
* 使用ScaleTest测试框架
* 在约定位置目录有源码
* 包含简单的scala目录以及相关的Scalatest文件，如果不存在源码和测试文件
* 默认使用Zinc Scale编译

## 15.3.4."groovy-library"

"groovy-library"无法根据环境推断出来，必须显示指定。具有以下优点:

* 使用"groovy”插件
* 使用"jcenter"依赖仓库
* 使用Groovy 2.x版本
* 使用Spock测试框架
* 在约定位置有源码目录
* 包含一个简单的Groovy类，以及相关的Spock，如果没有源码或测试文件

## 15.3.5.“basic”

"basic"用于创建一个空的Gradle项目，创建build.gradle文件，以及一些注释或者链接帮助开发者快速开发。此类型用于没有指定任何类型的参数，而且也不能根据环境推断。

