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



