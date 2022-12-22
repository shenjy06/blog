---
title: Jenkins持续集成
date: 2022-12-16 16:32:30
tags:
  - 持续集成
  - Jenkins

category:
  - DEVOPS
---

### 缘起

由于近期部门在用 `Java` 系框架 `springboot` 做一个项目, 程序发布流程太繁琐了. 下面我说下我司目前的部署步骤:

- 使用本地 `IdeaJ` 或者 `Maven` 命令将源代码打成 `jar` 包
- 然后把 `jar` 包上传到服务器上

最后, 启动与 `Java` 包配套的启动脚本启动程序

也就是说, 只要我写完一些功能, 想把功能发布到测试环境给前端同学调用, 我就得执行上面的三个步骤. 我觉得太麻烦了, 所以我打算使用 `Jenkins` 和 `Gitlab` 实现只要我一提交代码, 或者 `merge` 代码, 我就把代码从代码仓库中拿到测试服务器上, 执行编译, 打包, 发布这几个我之前手动做的功能. 这个操作就能够大大提高自己的效率了, 同时也能提高团队的效率. 虽然, 现在这个操作感觉非常好, 但是在提交代码之前, 我们还要做到基本的步骤, 就是你改完的代码要能在你机器上运行, 或者能够编译通过, 满足这两个步骤之一, 才可以提交代码.

### 搭建环境中遇到的问题

接下来, 我给大家分享下, 这次搭建环境全过程. 还有些注意问题在这里要跟大家交代下:

在 Jenkins 启动之前, 要确保所有的构建环境已经安装. 如果构建软件没有安装, 就要安装构建软件后重新启动 Jenkins.

环境

- Jenkins 2.332.1(官方推荐使用 Java 11)
- Windows Server 2008 R2
- JDK1.8.1
- Maven 3.8.4

因为之前服务器上已经安装了, JDK1.8.1, maven 环境, 这里将不再细说. 下面我主要给大家说下 Jenkins 的一些事情.

虽然是 windows 操作系统, 但是我并没有安装 windows 版本的 Jenkins. 而是使用官网提供的 war 包. 下载地址: https://www.jenkins.io/download/. 在启动 Jenkins 前, 要首先设置下环境变量 JENKINS_HOME , 设置完这个才可以运行启动 jenkins 的代码

```sh
java -war jenkins.war --httpPort=9090
```

#### Gitlab Webhook 配置时遇到的问题

`Jenkins` 启动完成后, 需要安装一些插件, 我这里直接使用 `Jenkins` 默认安装的一些插件. 安装完后, 创建用户, 登录进入 `Jenkins` 就好了. 这是首先要创建一个 `pipeline` , 首先要配置 `pipeline` 中的构建触发器, 主要配置 `Gitlab` 与 `Jenkins` 的联动, 在 `Gitlab` 中还要设置 `Webhook` , 当提交代码或者合并代码时候触发 `Webhook` 执行构建.

![](https://res.cloudinary.com/dnxgtp45y/image/upload/v1671715145/blog/jenkins/01_bybbjb.png)

上图是在 Jenkins 中配置的内容, 下图是在 `Gitlab` 中的配置.

![](https://res.cloudinary.com/dnxgtp45y/image/upload/v1671715146/blog/jenkins/02_lvu51a.png)

配置好 `Webhook` 后要测试下, 这里 `Gitlab` 中可以模拟 `Push events` , 这里还要做一个小小的配置, 配置`-Dhudson.security.csrf.GlobalCrumbIssuerConfiguration.DISABLE_CSRF_PROTECTION=true` ,完整命令如下

```sh
java -jar -Dhudson.security.csrf.GlobalCrumbIssuerConfiguration.DISABLE_CSRF_PROTECTION=true jenkins.war --httpPort=9090
```

除了配置以上内容还要配置如下参数: `Manage Jenkins` → `Configure Global Security` → `授权策略`, 具体如下图:

![](https://res.cloudinary.com/dnxgtp45y/image/upload/v1671715145/blog/jenkins/03_fdkt4c.png)

以上操作完成后, 就可以使用 `Jenkins` 完成构建.

#### 执行完 pipeline 程序并没有起来

这个配置好了, 接下来就是 `pipeline` 了, 代码如下

```bat
pipeline {
    agent any

    environment {
        REPOSITORY="git@sdasalksdfasl.git" // 仓库地址
    }

    stages {
       stage('build and run') {
          steps {
          // 编译, 打包文件
          bat """
            mvn clean package -Dmaven.test.skip=true
            """
          // 在执行前, 先关闭之前的程序
          bat """
            taskkill /FI "WindowTitle eq service1*" /T /F
            """
          // 执行自己的程序
          bat """
            cd target
            xcopy /y xxx-SNAPSHOT.jar d:\\ps-svc
            cd d:/ps-svc
            start.bat
            """
          }
        }
    }
  }
```

以上 `pipeline` 完成, 但是在使用 `Jenkins` 执行的时候也并没有出现什么问题, 但是可以观察到最后的程序可以起来, 但是当 `pipeline` 执行完后, 原来运行起来的 `springboot` 服务也关闭了. 这个问题我在网上查了下, 可以在 `Jenkins` 启动的时候配置参数`-Dhudson.util.ProcessTree.disable=true`. 这时候 `Jenkins` 全部的命令就编程了如下代码.

`start.bat`

```bat
@ECHO OFF
@REM start a service
@REM set BUILD_ID=dontkillme
start "service1" java -jar xxx-SNAPSHOT.jar
```

### 总结

以上就是我在折腾 Jenkins 所遇到的两个问题以及解决方法, 这里分享给你. 当然我这里还是有疑问的:
第一，进程如何起来的？
第二，这里有关操作系统的知识？

Jenkins 如何实现杀掉所有 pipeline 带起来的进程的, 这里可以看看 jenkins 源码.
接下来, 我还会继续查资料来解决我的疑问的, 加油。

### 参考链接

[1] https://ss64.com/nt/start.html
[2] https://www.jenkins.io/doc/book/managing/system-properties/
