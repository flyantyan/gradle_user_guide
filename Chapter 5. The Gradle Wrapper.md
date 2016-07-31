大多数工具在使用之前需要在本地安装其可执行文件。如果安装方式比较简单的话，那么这一切也就没有什么问题。但是这对于用户来构建多多少少是一个不必要的负担。另外，用户是否安装了正确版本的工具？如果使用旧版本会怎样？这其实都是带来的一些问题。
  Gradle Wrapper(以后简称"Wrapper")可以解决上述的这些问题，而且是Gradle构建工具的首选方式。
##5.1 采用 Wrapper 执行构建任务

    * ./gradlew <task>(类Unix)
    * gradlew <task>(Windows 批处理文件)
  每一个Wrapper与一个指定版本的Gradle相关联，所以当你首次执行指定版本Gradle的命令，它会想在相应的Gradle发行版，然后使用其执行构建命令。
  这不仅意味着你不必手动安装Gradle，而且你也可以确保使用的是正确版本的gradle。保证你的构建可靠。仅仅只需要使用前文描述的合适的语法`gradle...`
  为了确保你不删除重要的文件，一下是Gradle项目工程中与Wrapper相关的文件：
  
  * gradlew (Unix Shell）
  * gradlww.bat(Windows 批处理文件)
  * gradle/wrapper/gradle-wrapper.jar (Wrapper Jar)
  * gradle/wrapper/gradle-wrapper.properties(Wrapper配置文件)

如果你想知道下载后Gradle发行版存储在哪里，可以在`$USER_HOME/.gradle/wrapper/dists`目录下找到。

##5.2 添加 Wrapper
Wrapper是应该纳入版本控制的。通过将Wrapper纳入项目，项目中的任何一个人都不需要安装Gradle。更好的是，构建项目的参与者保证使用同一个版本的Gradle，另外在持续集成服务器上也不需要任何的配置。
你可以通过`wrapper`任务来将wrapper添加到你的项目中去（这个任务通常都是可调用的，即使你没有添加此任务）。
  * `--gradle-version`指定所需要的gradle的版本
  * `--gradle-distribution-url`指定gradle的下载路径

如果没有指定版本和下载的url，Wrapper将按照运行`wrapper`任务的gradle的版本来配置。所以你用Gradle 2.4来运行`wrapper`任务，那么Wrapper将会配置成默认的2.4。

Example 5.1 运行Wrapper任务


>gradle wrapper --gradle-version 2.0
>:wrapper

>BUILD SUCCESSFUL

>Total time: 1 secs


  Wrapper也可以通过配置`Wrapper`任务来自定义
  
  Example 5.2 Wrapper task
  build.gradle
  `task wrapper(type: Wrapper){
      gradleVersion = '2.0'
  }
  
  上述操作之后，你就可以在目录下找到新的或者更新的文件：
  
  >simple/
  >
  >gradlew
  >
  >gradlew.bat
  >
  >gradle/wrapper/
  >
  >      gradle-wrapper.jar
  > 
  >         gradle-wrapper.properties


  这些文件也应该被提交到版本控制系统中，当然这些操作只需要操作一次。这些文件添加到项目中之后，就可以用`gradlew`命令来替换`gradle`了。如果你想要切换到新版本的Gradle，你不需要重新执行`wrapper`任务，只需要修改`gradle-wrapper.properties`，但是你如果想过使用Gradle Wrapper的新特点，那么你需要重新执行wrapper任务来生成wrapper文件。
  
  ##5.3 配置
如果你是用`gradlew`来运行Gradle，那么Wrapper将会首先检测Gradle发行版是否已经存在。如果本地有，那么它会检测gradle命令。如果不存在，它将会首先下载。你可以通过配置来指定你所想要的Gradle版本。`gradlew`命令将会从服务器下载合适的发行版。除此之外，你也可以指定发行版下载的路径，`gradlew`将会使用你所指定的url来下载。如果你两者都没有指定，那么**gradlew**将会下载生成这些wrapper文件相应的Gradle的版本。可以通过[Gradle Wrapper API](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.wrapper.Wrapper.htmlI)来获得更多的配置信息。
如果你使用***gradlew*不想下载，可以添加Gradle发行版的zip压缩包到你本地的版本控制，然后通过本地路径来配置wrapper。这是支持相对路径的，你可以通过相对于gradle-wrapper.properties文件的位置来指定。如果你通过Wrapper来构建，任何已经安装的Gradle都会被忽略。

##5.4 Gradle发行版的校验
Gradle Wrapper允许通过SHA-256哈希码来校验下载的Gradle发行版。提高了安全线，避免中间攻击者攻击。 首先需要计算Gradle发行版的SHA-256哈希值。

Example 5.4 生成SHA-256 哈希值
```
> shasum -a 256 gradle-2.4-all.zip
371cb9fbebbe9880d147f59bab36d61eee122854ef8c9ee1ecf12b82368bcf10  gradle-2.4-all.zip
```

