# 工具类

## Lombok plugin

## Mybatis plugin

* 可以在mapper接口中和mapper的xml文件中来回跳转，就想接口跳到实现类那样简单。

## codehelper.generator

可以让你在创建一个对象并赋值的时候，快速的生成代码，不需要一个一个属性的向里面set,根据new关键字，自动生成掉用set方法的代码，还可以一键填入默认值。

#### GenAllSetter 特性

- 在Java方法中, 根据 new 关键词, 为Java Bean 生成所有Setter方法。
- 按GenAllSetter键两次, 会为Setter方法生成默认值。
- 可在Intellij Idea中为GenAllSetter设置快捷键。

##### 如何使用:

- 将光标移动到 new 语句的下一行。
- 点击主菜单Tools-> Codehelper-> GenAllSetter, 或者按下GenAllSetter快捷键。

#### GenDaoCode 特性

- 根据Pojo 文件一键生成 Dao，Service，Xml，Sql文件。
- Pojo文件更新后一键更新对应的Sql和mybatis xml文件。
- 提供insert，insertList，update，select，delete五种方法。
- 能够批量生成多个Pojo的对应的文件。
- 自动将pojo的注释添加到对应的Sql文件的注释中。
- 丰富的配置，如果没有配置文件，则会使用默认配置。
- 可以在Intellij Idea中快捷键配置中配置快捷键。
- 目前支持MySQL + Java，后续会支持更多的DB。
- 如果喜欢我们的插件，非常感谢您的分享。

#### GenDaoCode 使用方法

主菜单Tools-> Codehelper-> GenDaoCode 按键便可生成代码。

##### 方法一：点击GenDaoCode，然后根据提示框输入Pojo名字，多个Pojo以 | 分隔。

Codehelper Generator会根据默认配置为您生成代码。

##### 方法二：在工程目录下添加文件名为codehelper.properties的文件。

点击GenDaoCode，Codehelper Generator会根据您的配置文件为您生成代码



## GsonFormat

一键根据json文本生成java类，非常方便 

## GenerateAllSetter

GenerateAllSetter一键调用一个对象的所有set方法并且赋予默认值 在对象字段多的时候非常方便，在做项目时，每层都有各自的实体对象需要相互转换，但是考虑BeanUtil.copyProperties()等这些工具的弊端，有些地方就需要手动的赋值时，有这个插件就会很方便，创建完对象后在变量名上面按Alt+Enter就会出来 generate all setter选项。

## CodeGlance

在编辑区的右侧显示的代码地图。 

## restfulToolkit

面向springMVC的url查询工具

# 展示类

## Nyan progress bar

这是一个将你idea中的所有的进度条都变成萌新动画的小插件。 

## Rainbow Brackets

彩虹颜色的括号 看着很舒服 敲代码效率变高