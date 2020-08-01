#### 千万级应用美团Robust修复原理，手写字节码插件技术



> **码牛学院 让Android没有难学的技术。只为用代码码出牛逼的人生**

插件目前越来越多的运用到了项目中，如Tinker Glide，Rubost。 他主要用来在编译时生成代码。

插件开发有一下几个步骤

> 创建Gradle Module 
>
> 编写Plugin类
>
> 打包到本地Maven
>
> 主Moudle中使用开发好的插件



##### 1.1 创建Gradle Module 

AndroidStudio中是没有新建类似Gradle Plugin这样的选项的，那我们如何在AndroidStudio中编写Gradle插件，并打包出来呢？

> (1) 首先，你得新建一个Android Project
> (2) 然后再新建一个Module，这个Module用于开发Gradle插件，同样，Module里面没有gradle plugin给你选，但是我们只是需要一个“容器”来容纳我们写的插件，因此，你可以随便选择一个Module类型（如Phone&Tablet Module或Android Librarty）,因为接下来一步我们是将里面的大部分内容删除，所以选择哪个类型的Module不重要。
> (3) 将Module里面的内容删除，只保留build.gradle文件和src/main目录。
> 由于gradle是基于groovy，因此，我们开发的gradle插件相当于一个groovy项目。所以需要在main目录下新建groovy目录
> (4) groovy又是基于Java，因此，接下来创建groovy的过程跟创建java很类似。在groovy新建包名，如：com.hc.plugin，然后在该包下新建groovy文件，通过new->file->MyPlugin.groovy来新建名为MyPlugin的groovy文件。
> (5) 为了让我们的groovy类申明为gradle的插件，新建的groovy需要实现org.gradle.api.Plugin接口。如下所示：





```
package  com.hc.plugin

import org.gradle.api.Plugin
import org.gradle.api.Project

public class MyPlugin implements Plugin<Project> {

    void apply(Project project) {
        System.out.println("========================");
        System.out.println("hello gradle plugin!");
        System.out.println("========================");
    }
}
```




> (6) 现在，我们已经定义好了自己的gradle插件类，接下来就是告诉gradle，哪一个是我们自定义的插件类，因此，需要在main目录下新建resources目录，然后在resources目录里面再新建META-INF目录，再在META-INF里面新建gradle-plugins目录。最后在gradle-plugins目录里面新建properties文件，注意这个文件的命名，你可以随意取名，但是后面使用这个插件的时候，会用到这个名字。
>
> 比如，你取名为com.hc.gradle.properties，而在其他build.gradle文件中使用自定义的插件时候则需写成：



> apply plugin: 'com.hc.gradle'

##### 1.2 编写Plugin类

然后在com.hc.gradle.properties文件里面指明你自定义的类 

> implementation-class=com.hc.plugin.MyPlugin


现在，你的目录应该如下：

![自定义插件目录结构](https://img-blog.csdn.net/20160702123302689)

> (7) 因为我们要用到groovy以及后面打包要用到maven,所以在我们自定义的Module下的build.gradle需要添加如下代码：

```
apply plugin: 'groovy'
apply plugin: 'maven'

dependencies {
    //gradle sdk
    compile gradleApi()
    //groovy sdk
    compile localGroovy()
}

repositories {
    mavenCentral()
}
```

##### 1.3 打包到本地Maven

​		前面我们已经自定义好了插件，接下来就是要打包到Maven库里面去了，你可以选择打包到本地，或者是远程服务器中。在我们自定义Module目录下的build.gradle添加如下代码：

```
//group和version在后面使用自定义插件的时候会用到
group='com.hc.plugin'
version='1.0.0'

uploadArchives {
    repositories {
        mavenDeployer {
            //提交到远程服务器：
           // repository(url: "http://www.xxx.com/repos") {
            //    authentication(userName: "admin", password: "admin")
           // }
           //本地的Maven地址设置为D:/repos
            repository(url: uri('D:/repos'))
        }
    }
}
```

​		其中，group和version后面会用到，我们后面再讲。虽然我们已经定义好了打包地址以及打包相关配置，但是还需要我们让这个打包task执行。点击AndroidStudio右侧的gradle工具，如下图所示：

![上传Task](https://img-blog.csdn.net/20160702130539639)

​		可以看到有uploadArchives这个Task,双击uploadArchives就会执行打包上传啦！执行完成后，去我们的Maven本地仓库查看一下：

![打包上传后](https://img-blog.csdn.net/20160702130836877)

​		其中，com/hc/plugin这几层目录是由我们的group指定，myplugin是模块的名称，1.0.0是版本号（version指定）。



##### 1.4 使用自定义的插件

​		接下来就是使用自定义的插件了，一般就是在app这个模块中使用自定义插件，因此在app这个Module的build.gradle文件中，需要指定本地Maven地址、自定义插件的名称以及依赖包名。简而言之，就是在app这个Module的build.gradle文件中后面附加如下代码：

```
buildscript {
    repositories {
        maven {//本地Maven仓库地址
            url uri('D:/repos')
        }
    }
    dependencies {
        //格式为-->group:module:version
        classpath 'com.hc.plugin:myplugin:1.0.0'
    }
}
//com.hc.gradle为resources/META-INF/gradle-plugins
//下的properties文件名称
apply plugin: 'com.hc.gradle'
```


好啦，接下来就是看看效果啦！先clean project(很重要！),然后再make project.从messages窗口打印如下信息：

![使用自定义插件](https://img-blog.csdn.net/20160702131957936)

好啦，现在终于运行了自定义的gradle插件啦！

##### 1.5 开发只针对当前项目的Gradle插件

前面我们讲了如何自定义gradle插件并且打包出去，可能步骤比较多。有时候，你可能并不需要打包出去，只是在这一个项目中使用而已，那么你无需打包这个过程。

只是针对当前项目开发的Gradle插件相对较简单。步骤之前所提到的很类似，只是有几点需要注意：

> 1. 新建的Module名称必须为BuildSrc
> 2. 无需resources目录

目录结构如下所示：

![针对当前项目的gradle插件目录](https://img-blog.csdn.net/20160702135323958)

其中，build.gradle内容为：

```
apply plugin: 'groovy'

dependencies {
    compile gradleApi()//gradle sdk
    compile localGroovy()//groovy sdk
}

repositories {
    jcenter()
}

```

SecondPlugin.groovy内容为：

```
package  com.hc.second

import org.gradle.api.Plugin
import org.gradle.api.Project

public class SecondPlugin implements Plugin<Project> {

    void apply(Project project) {
        System.out.println("========================");
        System.out.println("这是第二个插件!");
        System.out.println("========================");
    }
}
 
```



在app这个Module中如何使用呢？直接在app的build.gradle下加入

> apply plugin: com.hc.second.SecondPlugin


clean一下，再make project，messages窗口信息如下：

![打印信息](https://img-blog.csdn.net/20160702135750329)

由于之前我们自定义的插件我没有在app的build.gradle中删除，所以hello gradle plugin这条信息还会
