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
Wrapper是应该纳入版本控制的。通过将Wrapper纳入项目，项目中的任何一个人都不需要安装Gradle。更好的是，构建项目的参与者保证使用同一个版本的Gradle，当然在持续集成服务器上也不需要任何的配置