引用wiki：

```
Daemon是一个运行于后台的程序，而不是由使用者直接控制的交互程序。
```

运行于Java虚拟机和一些支持库上的Gradle需要的初始化时间不是常量级别的。因此，有时候它看起来启动的非常缓慢。这个问题的解决方式就是Gradle守护进程\(Daemon\)：长时间存活在后台的进程来执行构建任务会非常快。我们通过避免昂贵的引导过程，使用项目中的缓存数据来实现Gradle守护进程机制。使用不使用Gradle守护进程在操作上是没有一点不同的，简单的配置就可以实现Gradle守护进程的使用与否，其他的都是由Gradle自动实现的，对用户不可见。

## 6.1 激活守护进程

Gradle守护进程默认是不使用的，但是我们强烈建议开发者的电脑上总是使用这个守护进程\(在持续迭代服务器上可以不使用\)。有很多种方式来激活Gradle 守护进程，但是最常用的方式是在 `$USER\_HOME\/.gradle\/gradle.properties`文件中添加:

```
org.gradle.daemon=true
```

其中$USER\_HOME是电脑的用户主目录，这与你所使用的电脑的系统有关:

* C:\Users\&lt;username&gt; \(Windows Vista & 7+\)

* \/Users\/&lt;username&gt; \(Mac OS X\)

* \/home\/&lt;username&gt; \(Linux\)


如果文件不存在，手动创建一个就可以了。你可以在以下的FAQ中找到关于激活、关闭Daemon进程更详细的方式，包括守护进程是如何工作的信息。一旦你通过这种方式配置了全局的激活Gradle守护进程，那么构建任务相对来说就会有些加速，不论使用什么版本构建。

## 6.2 关闭Daemon

正如上文所说，守护进程是一个后台进程。你不必担心在构建过程中创建的程序。每一个Gradle守护进程在3小时不是就会自动关闭。如果你想手动停止守护进程，可以使用 _ gradle --stop _

** Note：只能关闭当前Gradle创建的守护进程，并不能关闭其他Gradle命令创建的守护进程 **

它将会终止我们所使用Gradle创建的所有的守护进程，如果你安装了JDK，你可以通过 **jps**命令查看Daemon的状态。Gradle守护进程的名字为**GradleDaemon**。

## 6.3 FAQ
### 6.3.1 可以启动Gradle Daemon 的其他方式

有两个建议使用的环境变量的方式来激活Gradle 守护进程

* 通过添加标志:`-D org.gradle.daemon=true`

### 6.3.5 守护进程需要多少内存？我可以给它额外的吗？
如果没有指定构建环境所需要的最大堆，守护进程将最大可用1G堆内存。将会使用JVM的默认最小堆值，1GB对于大多数构建来说都是足够的。如果需要构建的项目有很多的子项目，有很多的配置，需要很多源码，或者需要很多的性能提升，那么就会需要更多的内存。
可以通过指定运行环境所需要的合适的标志位，可以增加Daemon所使用的内存。

### 6.3.6 我如何停止Daemon
Daemon将会在空闲3个小时后自动杀死。如果在这之前你希望停止守护进程，可以通过`gradle --stop`命令或系统命令杀死进程。`--stop`将会停止所有运行的守护进程，使用相同版本命令创建的守护进程。
## 6.4 什么时候不应该使用Gradle守护进程
建议开发者在自己的开发机上使用Gradle守护进程，建议不要在持续迭代服务器上使用Gradle守护进程。Gradle守护进程可以使构建过程加速，这对于在屏幕前等待构建过程结束的开发者来说是非常重要的，但是对于CI构建，稳定性与可预测性是最重要的，每一次构建都使用全新的运行环境可以使每一次构建都更加可靠，与前一次的构建独立出来。

## 6.5 工具&IDE


## 6.6 Gradle守护进程如何使构建过程加速
Gradle 守护进程是长时间存活在后台的进程，在两次构建之间处于空闲状态，等待着下一次构建活动的发生。对于每一次构建来说，只需要加载一次Gradle运行环境就可以完成多次构建，有着很明显的性能提升
另外就是JVM使用运行时代码优化所带来的性能提升。例如，Hotspot 在运行时应用代码优化，这个优化是渐进的而不是瞬时的，也就是说代码在执行过程中逐步被优化，意味着随后的构建过程可以更快。实验表明，主要在第5次与第10次构建过程中进行优化。所以,Gradle构建过程在第一次与第10次之间有着很明显的性能差异。
守护进程也使用内存缓存来加速构建过程，例如，构建过程所需要的classes文件在构建中可以保存在内存中，类似的，Gradle也会缓存构建过程中产生的数据文件，用于随后的构建过程。

### 6.6.1 未来提升的可能性
目前来说，守护进程采用内存缓存和JVM优化来加速构建过程，未来的Gradle版本，守护进程可以变得更加智能，可以先人一步执行一些操作。例如，它可以在build脚本编写完成就开始下载所依赖的库，基于build将要运行的，新添加的、改变的。在未来的Gradle版本也会有其他的方式来加速构建过程。















