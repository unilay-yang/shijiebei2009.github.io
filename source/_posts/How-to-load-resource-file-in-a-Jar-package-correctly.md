title: "Jar包中如何正确地加载资源文件"
date: 2015-04-22 21:07:22
tags: [Java]
categories: Programming Notes
toc: false

---

JDK：java version "1.8.0_31"
Java(TM) SE Runtime Environment (build 1.8.0_31-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.31-b07, mixed mode)

OS：win7 64bit

在日常开发中，经常需要在项目中加载各种资源，包括图片、文本、声音等资源，在本地项目中运行代码可以正确地加载资源，但是当把本地项目打包成Jar包并发布到web项目中之后，即便将各种资源文件一并打包，依然会出现无法正确加载资源的问题。这说明，在你希望打包的项目中加载资源的方式是错误的。那么你或许会问，为什么在单单的项目中可以正确加载资源，而打包之后就无法加载了呢？这是因为，打包之后，jar包中的所加载的文件路径发生了变化，例如res.txt打包发布之后，其路径变为file:/C:/ResourceJar.jar!/resource/res.txt，如果你在原项目中使用new File(filePath)之类的方法来加载的话，肯定会找不到资源文件。主要是因为jar包是一个单独的文件而非文件夹，绝对不可能通过"file:/e:/.../ResourceJar.jar/resource /res.txt"这种形式的文件URL来定位res.txt。所以即使是相对路径，也无法定位到jar文件内的txt文件。

以将要打包的项目jarProj为例，介绍如何在将要打包的项目中正确的加载资源，jarProj项目目录结构如下
 (conf是Source Folder，创建方法是在项目上右击->New->Other->Java->Source Folder，使用该方式所创建的目录效果和src目录相同，在编译之后，其下的文件和src下的文件都会发布到bin目录下)。
>jarProj-
>> src--
>>> edu/shu/reference/jar/LoadFile.java

>> conf--
>>> reource/text.txt

如果需要在jarProj项目中加载一些资源文件，之后将其打包，发布到其他项目之下，该jarProj项目依然可以正确的加载到资源文件，需要使用到getResource()和getResourceAsStream()两个方法，而这两个方法大多数人都是无法区分的，需要做一个简单介绍

Class.getResource(String path)
path 不以'/'开头时，默认是从此类所在的包下取资源；
path 以'/'开头时，则是从加载该类的class loader下获取；如果该类是由bootstrap class loader加载，那么该方法等效于ClassLoader.getSystemResource(java.lang.String). 

1. 如果使用this.getClass().getResource("myFile.ext")
getResource方法会尽力从相对于该类所在包的路径下去寻找资源

2. 如果使用this.getClass().getResource("/myFile.ext")
getResource方法会把它看成绝对路径，并且会简单的从class loader根下去加载资源

3. 如果使用this.getClass().getClassLoader().getResource("myFile.ext")
这时候，传递的参数不能够以/作为路径的开始，因为所有的ClassLoade路径都是绝对路径，不需要参数以/作为合法的路径开始符号

在上述三项中，2和3是等效的。

当项目被打包之后，其中的资源文件的路径其实已经发生变化，所以用简单的方法去加载肯定是找不到资源的，一般jar包中资源路径如下：file:\D:\devSoft\jarProj.jar!\resource\test.txt，这时有两种解决办法，第一：用URL根据Jar包规范构造输入流（jar包中的资源有其专门的URL形式： jar:<url>!/{entry}），然后进行读取；第二种是使用getResourceAsStream进行加载，获取输入流，之后从输入流中读取，但是这样就无法使用诸如
```java
new BufferedReader(new FileReader(new File(filepath)))
```
一类的方法了。

测试代码如下：
第一种在加载资源类中使用getResourceAsStream()方法

```java
package edu.shu.reference.jar;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.net.JarURLConnection;
import java.net.URL;
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.IOUtils;
import org.junit.Test;

/**
 * 
 * <p>
 * ClassName LoadFile
 * </p>
 * <p>
 * Description 将Java项目打包，发布到Web项目下，依然可以正确的加载资源
 * </p>
 * 
 * @author TKPad wangx89@126.com
 *         <p>
 *         Date 2015年4月22日 下午9:06:07
 *         </p>
 * @version V1.0.0
 *
 */
public class LoadFile {
	public void read() throws IOException {
		getResource();
	}
	public void getResource() throws IOException {
		InputStream is = this.getClass().getResourceAsStream("/resource/test.txt");
		String fileContent = IOUtils.toString(is);
		System.out.println(fileContent);
	}
}
```
将该项目打包，发布到其他项目下，在其他项目下编写测试类，代码如下：
```java
import java.io.IOException;

import edu.shu.reference.jar.LoadFile;

public class Test {
	public static void main(String[] args) throws IOException {
		LoadFile lf = new LoadFile();
		lf.read();
	}
}

```
可以看到正确的输出结果，加载资源没问题！

第二种使用URL加载Jar资源的方式加载
```java
package edu.shu.reference.jar;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.net.JarURLConnection;
import java.net.URL;
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;

import org.apache.commons.io.FileUtils;
import org.apache.commons.io.IOUtils;
import org.junit.Test;

/**
 * 
 * <p>
 * ClassName LoadFile
 * </p>
 * <p>
 * Description 将Java项目打包，发布到Web项目下，依然可以正确的加载资源
 * </p>
 * 
 * @author TKPad wangx89@126.com
 *         <p>
 *         Date 2015年4月22日 下午9:06:07
 *         </p>
 * @version V1.0.0
 *
 */
public class LoadFile {
	public void read() throws IOException {
		String f = "/resource/test.txt";
		URL resource = this.getClass().getResource(f);
		loadJar(resource.getFile());
	}
	/**
	 * 
	 * <p>
	 * Title: loadJar
	 * </p>
	 * <p>
	 * Description: ③ 封装了一个加载Jar包的方法，使用URL根据jar包加载规范获取输入流，并据此输入流做进一步的操作
	 * </p>
	 * 
	 * @param filePath
	 * @throws IOException
	 *
	 */
	public void loadJar(String filePath) throws IOException {
		// 注意在WINDOWS环境一定要使用正斜杠
		filePath = "jar:" + filePath;
		URL url = new URL(filePath);
		JarURLConnection openStream = (JarURLConnection) url.openConnection();
		InputStream inputStream = openStream.getInputStream();
		// 使用IO工具类将输入流输出到控制台
		IOUtils.copy(inputStream, System.out);
		// 使用IO工具类从输入流中按行读取并存入List集合
		List<String> contents = IOUtils.readLines(inputStream);
		System.out.println(contents);
	}
}

```
将该项目打包，发布到其他项目下，在其他项目下编写测试类，代码如下：
```java
import java.io.IOException;

import edu.shu.reference.jar.LoadFile;

public class Test {
	public static void main(String[] args) throws IOException {
		LoadFile lf = new LoadFile();
		lf.read();
	}
}

```
可以看到正确的输出结果，加载资源没问题！


整个项目的下载地址在：http://download.csdn.net/detail/shijiebei2009/8631005


参考资料
【1】http://hxraid.iteye.com/blog/483115
