##14.1 项目和任务
Gradle的世界中有两个基本的概念:项目和任务

每一个Gradle构建中包括一个或者多个项目。一个项目是什么取决于你是用你用Gradle做的什么。例如，一个项目可能代表一个库jar或web应用程序。可能代表将其他项目生成的jar发布成ZIP。项目并不一定代表构建东西，可能代表所要做的事，例如，把应用程序发布到稳定或生产环境。不必担心现在可能对这些概念有些模糊，Gradle是基于约定的构建，支持添加多个项目的定义。
每一个项目是由任务组成的，一个任务代表构建时的一个原子化的操作，可能是编译类文件，创建jar，生成java doc，发布到仓库。

##14.2 Hello World

可以`gradle`命令执行构建。`gradle`命令查找`build.gradle`文件在当前目录下。把这个文件叫做构建脚本，严格上来说，应该叫做构建配置脚本。其中定义了项目和任务。

Example 14.1 第一个构建脚本
build.gradle
```
task hello{
    doLast{
        println 'hello world'    
    }
}
```
在命令行执行`gradle -q hello`
Example 14.2. Execution of a build script
Output of gradle -q hello
```
> gradle -q hello
Hello world!
```