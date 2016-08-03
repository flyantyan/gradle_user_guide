只有很小的项目有单一的构建脚本和源码结构，大多数都是庞大复杂的应用程序。只有把这个大项目分解成小的，内部依赖的模块，才更容易理解。内部依赖是非常重要的，因为我们可以用一个构建脚本把这些模块连接在一起。

gradle可以通过『多模块构建』`multi-project build`来实现这个场景。


##8.1 多项目构建的结构

* 项目根目录下有`setting.gradle`文件
* 项目根目录下由`build.gradle`文件
* 子项目的目录下有自己的`build.gradle`文件（一些项目的build.gradle全部在项目的根目录下）

`setting.gradle`告诉Gradle项目与子项目之间的结构。幸运的是，你不必阅读这个文件，可以通过命令`gradle projects`就可以很方便的了解项目的架构，以下是在多项目程序中运行该命令的实例：

Example 8.1 陈列多项目
```
>gradle -q projects

---------------------------------------------------
Root project
---------------------------------------------------

Root project 'multiproject'
+---- Project ':api'
+---- Project ':services'
|    +---- Project ':services:shared'
|    +---- Project ':services:webservice'
\--- Project ':shared'

To see a list of the tasks of a project, run gradle <project-path>:tasks
For example, try running gradle :api:tasks
```
从中可以看出，这个项目包括三个直接子项目:api、services、shared。其中项目servies也包含自己的子项目shared、webservice。这个项目结构很清晰，通过阅读这个结构就可以很轻易的找到项目的实际位置。；例如，webservice位于`<root>/serives/webservices`。
默认情况下Gradle使用有setting.gradle文件的目录作为根项目的名称。通常情况下不会有大问题，因为所有的开发者都是从相同的目录名称下检出代码。但是在持续集成服务器(Continuous Integration Servers,CI)，比如Jenkins，目录的名称可能是自动生成的，不与版本控制服务器上的名称相同。由于这个原因，建议每次都设置根目录的名称，即使在单一项目构建中。可以用`rootProject.name`来设置根项目名称。
每一个项目通常都会有自己的`build.gradle`文件，但是也有例外。在上面的例子中，services项目仅仅是其他子项目的一个组合（容器），也就没有`build.gradle`文件，但是，多项目程序中在根目录下必须有一个`build.gradle`。

根目录下的`build.gradle`通常做子项目的公用配置，例如使用相同的插件，依赖。当然也可以做个别子项目的配置，如果希望所有的配置都放在同一个地方。这也就意味着，如果你想了解一个子项目是如何配置的，不要忘了根目录的配置。
另一个需要记住的是，构建脚本可能不是叫做`build.gradle`，很多项目将会用子项目的名称命名，例如api.gradle,service.gradle。这对于IDE开发有很多帮助，因为很难从众多名字一样的`build.gradle`中找到你想要打开的一个。可以通过`setting.gradle`来实现这个操作，不过你不必知道实现细节，只需要在子项目的根目录下查找.gradle结尾的文件即可。

##8.2 多项目构建
从使用者的角度来说，多项目构建也是一系列任务的集合。不同之处在于你可能想要控制执行哪个项目的任务而已。有两个可选的：
* 直接到相应项目的根目录下执行`gradle <task>`
* 可以在任意目录下执行任务构建，不过通常实在项目根目录下。例如`gradle :service:webservice:build`将会构建webservice子项目以及其依赖的项目。



