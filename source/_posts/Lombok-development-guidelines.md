title: Lombok开发指南
date: 2015-07-30 21:33:11
tags: [Java]
categories: Programming Notes

---
###Lombok简介
在公司接触到了非常多的优秀框架和工具，比如Google Guava和Lombok等，进而一发不可收拾，从此Java开发就离不开它了。Lombok是一个可以减少冗余Java代码的神器。

Lombok主要是通过注解从而免去用户手动编写大量的getter、setter以及toString、equals、hashCode等方法。
Lombok官网地址：https://projectlombok.org/ 里面还提供了一个简短的学习视频。

###eclipse中安装Lombok
####下载lombok
下载地址：http://projectlombok.org/download.html
####安装
![](http://7xig3q.com1.z0.glb.clouddn.com/eclipse-lombok.png)
注意如果eclipse没有安装到默认目录，那么需要点击Specify选择eclipse的安装文件，然后Install即可完成安装。

在新建项目之后，使用Lombok如果程序还报错，那么点击eclipse菜单的Project选项的clean，清理一下。

###IntelliJ安装Lombok
####通过IntelliJ的插件中心安装
![](http://7xig3q.com1.z0.glb.clouddn.com/IntelliJ-plugin-lombok.png)
![](http://7xig3q.com1.z0.glb.clouddn.com/IntelliJ-lombok.png)
注意一点，在IntelliJ中如果创建的是Maven项目，那么在pom.xml文件中添加依赖后，需要设置Maven为自动导入。
![](http://7xig3q.com1.z0.glb.clouddn.com/IntelliJ-maven-auto-import.png)

####通过Jar包安装
该地址提供了最新的[Jar包下载](https://github.com/mplushnikov/lombok-intellij-plugin/releases/tag/releasebuild_0.9.6)

安装方法参见Github上的说明：https://github.com/mplushnikov/lombok-intellij-plugin
###使用Lombok
####Lombok注解说明
- `@Data`：注解在类上，相当于同时使用了`@ToString`、`@EqualsAndHashCode`、`@Getter`、`@Setter`和`@RequiredArgsConstrutor`这些注解，对于`Pojo类`十分有用
- `@NonNull`：给方法参数增加这个注解会自动在方法内对该参数进行是否为空的校验，如果为空，则抛出NPE
- `@CleanUp`：自动生成try-finally这样的代码来关闭流
- `@Getter(lazy=true)`：可以替代经典的Double Check Lock样板代码
- `@Setter`：注解在属性上；为属性提供 setting 方法
- `@Getter`：注解在属性上；为属性提供 getting 方法
- `@Log4j`：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象
- `@NoArgsConstructor`：注解在类上；为类提供一个无参的构造方法
- `@AllArgsConstructor`：注解在类上；为类提供一个全参的构造方法

####示例

```java
import lombok.Data;
@Data
public class Menu {
    private String shopId;
    private String skuMenuId;
    private String skuName;
    private String normalizeSkuName;
    private String dishMenuId;
    private String dishName;
    private String dishNum;
    //默认阈值
    private float thresHold = 0;
    //新阈值
    private float newThresHold = 0;
    //总得分
    private float totalScore = 0;
}
```
在IntelliJ中按下Ctrl+F12就可以看到Lombok已经为我们自动生成了一系列的方法。
![](http://7xig3q.com1.z0.glb.clouddn.com/IntelliJ-lombok-java-demo.png)

当然了，还可以分别使用@Getter和@Setter注解，例如
```java
import lombok.*;
@ToString
@EqualsAndHashCode
public class Menu {
    @Setter
    @Getter
    private String shopId;
    private String skuMenuId;
    private String skuName;
    private String normalizeSkuName;
    private String dishMenuId;
    private String dishName;
    private String dishNum;
    //默认阈值
    private float thresHold = 0;
    //新阈值
    private float newThresHold = 0;
    //总得分
    private float totalScore = 0;

    public void test(@NonNull String test) {
    }
}
```
###eclipse手动安装Lombok
- 将`lombok.jar`复制到`myeclipse.ini/eclipse.ini`所在的文件夹目录下
- 打开`eclipse.ini/myeclipse.ini`，在最后面插入以下两行并保存：
`-Xbootclasspath/a:lombok.jar`
`-javaagent:lombok.jar`
- 重启`eclipse/myeclipse`

最后需要注意的是，在使用`lombok`注解的时候记得要导入`lombok.jar` 包到工程，如果使用的是`Maven Project`，要在`pom.xml`中添加依赖，并设置`Maven`为自动导入，参见上图。
