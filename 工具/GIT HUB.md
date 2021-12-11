###  GIT HUB 简介

  软件开发原工具 Git (源码管理工具)

GitHub 和  GitLab 都是基于 web 的 Git 仓库，使用起来二者差不多，它们都提供了分享开源项目的平台，为开发团队提供了存储、分享、发布和合作开发项目的中心化云存储的场所。

API(软件层次的接口) Interface(开发层面的接口)

(1).分布式版本控制系统下的本地仓库包含代码库还有历史库，在本地就可以查看版本历史

(2).而集中式版本控制系统下的历史仓库是存在于中央仓库，每次对比与提交代码都必须连接到中央仓库

(3).多人开发时，如果充当中央仓库的Git仓库挂掉了，任何一个开发者都可以随时创建一个新的中央仓库然后同步就可以恢复中央仓库

### GIT HUB ACTION
Github Actions是Github推出的一个新的功能，可以为我们的项目自动化地构建工作流，例如代码检查，自动化打包，测试，发布版本等等。入口在项目pull request的旁边。

#### 基本概念

GitHub Actions 有一些自己的术语。

（1）workflow （工作流程）：持续集成一次运行的过程，就是一个 workflow。

（2）job （任务）：一个 workflow 由一个或多个 jobs 构成，含义是一次持续集成的运行，可以完成多个任务。

（3）step（步骤）：每个 job 由多个 step 构成，一步步完成。

（4）action （动作）：每个 step 可以依次执行一个或多个命令（action）。

#### workflow 文件

GitHub Actions 的配置文件叫做 workflow 文件，存放在代码仓库的 .github/workflows目录。

workflow 文件采用 YAML 格式，文件名可以任意取，但是后缀名统一为 .yml，比如 foo.yml。一个库可以有多个 workflow 文件。GitHub 只要发现 .github/workflows 目录里面有 .yml 文件，就会自动运行该文件。

#### 创建一个workflow

方法一: 通过git hub的可视化界面创建

- 点击`actions`入口
- 点击create a new workflow`按钮，进入自定义`workflow`页面：

方法二:编写main.workflow文件

* 在项目根目录新建`.github`文件夹，在`.github`文件夹新建`main.workflow`文件，编写完成后推送到`master`分支即可。

这是一个简单的`main.workflow`文件:

```

workflow "Build, Test, and Publish" {
  on = "push"
  resolves = ["Publish"]
}

action "Build" {
  uses = "actions/npm@master"
  args = "install"，
  env = {
    LOG_FILE = "log.txt"
  }
}

action "Test" {
  needs = "Build"
  uses = "actions/npm@master"
  args = "test"
}

action "Publish" {
  needs = "Test"
  uses = "actions/npm@master"
  args = "publish --access public"
  secrets = ["NPM_AUTH_TOKEN"]
}

```

一些简单的属性:

`workflow`的属性：

- on： 定义什么情况下会触发这个workflow，例如，push, pull_request等。
- resolves： 定义要调用的`actions`，可以是字符串或者一个字符串数组，若是只有一个字符串，表示最后调用的`action`，若是一个字符串数组，则表示完成这个`workflow`需要执行完这几个`actions`。

`action`的属性：

- needs：定义执行本`action`前需要成功执行的`action`，可以是字符串或者一个字符串数组，如果`needs`的`action`不止一个，那么这些`actions`会并行执行。
- uses：定义执行本`action`需要运行的`docker`镜像，如`uses = "node:10"`，如果不是使用`docker hub`提供的镜像，而是选择自己编写`Dockerfile`的话（后面会具体说明），这里的路径则为本地`Dockerfile`的路径。
- runs：指定在`docker`镜像中要执行的命令，若指定，则会覆盖`Dockerfile`里面的`ENTRYPOINT`，若不指定，则默认执行`Dockerfile`里面`ENTRYPOINT`的命令。

> 假如我们想要在action里面执行npm install，那么只要指定: `runs: "npm install"`即可。

- args：指定要传递给`action`的参数，可以是一个字符串或者一个字符串数组，若指定，则会覆盖`Dockerfile`里面的`CMD`。

> 假如我们想要在action里面执行npm run lint，那么只要指定: `args: "run lint"`即可。

- env：设置action运行时需要的环境变量，一般是自己写`Dockerfile`时会需要用到，具体说明请看[官方文档](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.github.com%2Factions%2Fcreating-workflows%2Fworkflow-configuration-options%2F)。

#### 怎样创建一个action

1. 使用官方提供的action
   在github action界面点击右侧的actions列表，通过点击use按钮使用该action，根据需求填写runs, args, env等参数即可。
2. 自己写dockerfile(自定义程度高)


### 踩坑日志

1. entrypoint.sh需要执行权限
通过自己写dockerfile的方式来创建action的话，需要执行以下代码，不然会出错。

chmod +x ./actions/entrypoint.sh（替换成自己项目entrypoint.sh的路径）

2. npm install yarn -g ?
本来是想通过安装yarn，然后使用yarn来安装依赖的，但是因为容器的独立性，每个action都是独立的，不存在全局环境，所以无法实现，而github官方提供了一个公共的存储空间，npm install下载完成的文件就放在公共的存储空间，因此可以提供给后面的action使用，这也意味着如果需要在action之间传递信息，暂时也只能利用公共的存储空间，使用文件读写的方式来传递。

3. npm run test需要使用浏览器来跑单元测试，启动失败
浏览器无法直接在docker容器里面启动，使用xvfb-run npm test 成功解决。


