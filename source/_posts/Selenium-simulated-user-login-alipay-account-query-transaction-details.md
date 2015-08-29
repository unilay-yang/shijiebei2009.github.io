title: "使用Selenium模拟用户登录支付宝账户查询交易详情"
tags: [Selenium]
categories: Tools
date: 2015-08-29 21:39:00

---

###Selenium简介
`Selenium`是一个用于Web应用程序测试的工具。`Selenium`测试直接运行在浏览器中，就像真正的用户在操作一样。支持的浏览器包括IE、Mozilla Firefox、Chrome等。这个工具的主要功能包括：测试与浏览器的兼容性——测试你的应用程序看是否能够很好得工作在不同浏览器和操作系统之上。测试系统功能——创建回归测试检验软件功能和用户需求。支持自动录制动作和自动生成`.Net`、`Java`、`Perl`等不同语言的测试脚本。`Selenium`是`ThoughtWorks`专门为Web应用程序编写的一个验收测试工具。
###Selenium的优势
据`Selenium`主页所说，与其他测试工具相比，使用`Selenium`的最大好处是：`Selenium`测试直接在浏览器中运行，就像真实用户所做的一样。`Selenium`测试可以在`Windows`、`Linux`和`Macintosh`上的`Internet Explorer`、`Mozilla`和`Firefox`中运行。其他测试工具都不能覆盖如此多的平台。使用`Selenium`和在浏览器中运行测试还有很多其他好处。下面是主要的两大好处：
- 通过编写模仿用户操作的`Selenium`测试脚本，可以从终端用户的角度来测试应用程序
- 通过在不同浏览器中运行测试，更容易发现浏览器的不兼容性

`Selenium`的核心，也称`browser bot`，是用`JavaScript`编写的。这使得测试脚本可以在受支持的浏览器中运行。`browser bot`负责执行从测试脚本接收到的命令，测试脚本要么是用`HTML`的表布局编写的，要么是使用一种受支持的编程语言编写的。

###Selenium-WebDriver
`Selenium-WebDriver`支持如下浏览器，在所有支持这些浏览器的操作系统中能都运行良好。

*   Google Chrome 12.0.712.0+
*   Internet Explorer 6, 7, 8, 9 - 32 and 64-bit where applicable
*   Firefox 3.0, 3.5, 3.6, 4.0, 5.0, 6, 7
*   Opera 11.5+
*   HtmlUnit 2.9
*   Android – 2.3+ for phones and tablets (devices & emulators)
*   iOS 3+ for phones (devices & emulators) and 3.2+ for tablets (devices & emulators)

###Selenium开发环境
如欲使用Java语言，那么JDK的环境是必备的。使用`Selenium`来驱动浏览器，那么必须要有[浏览器驱动包](http://pan.baidu.com/s/1o6quY5G)，同样还需要[Selenium的Jar包](http://pan.baidu.com/s/1o6vKGh8)，点击下载即可。

###Selenium启动浏览器
####启动Firefox
```java
//后一个参数是Firefox的安装路径
System.setProperty("webdriver.firefox.bin", "C:/Program Files (x86)/Mozilla Firefox/firefox.exe");
WebDriver driver = new FirefoxDriver();
driver.get("www.baidu.com");
```

####启动Chrome
```java
//后一个参数是Chrome浏览器的驱动，需要下载
System.setProperty("webdriver.chrome.driver", "D:/chromedriver.exe");
WebDriver driver = new ChromeDriver();
driver.get("www.baidu.com");
```
####启动IE
作为开发人员，建议您应该远离IE，虽然我不太愿意提供IE的启动方式给您！
```java
System.setProperty("webdriver.ie.driver", "D:/IEDriverServer.exe");
WebDriver driver = new InternetExplorerDriver();
driver.get("www.baidu.com");
```

###常见问题的处理方法
>How to select any element from the web element with “display: none” attribute using Selenium ?
特别是在处理因为`<select>`标签中带有`style='display:none'`而无法设置option的情况时很有效！

方法：
>Use execute_script() to set the display property of that element and then use the Selenium Select for selecting a required value.
```java
JavascriptExecutor js = (JavascriptExecutor) driver;//将driver对象强转成JavascriptExecutor
js.executeScript("document.getElementById('J-select-range').style.display='list-item';");//修改display的值，也可以修改为display='list-item'
```

>Element is not currently visible and may not be manipulated

一般这种错误是因为父元素有`style='display:none'`属性或者是页面还没有加载完全，最简单的就是使用`Thread.sleep(1000);`休息一秒之后再操作。当然也可以使用until方法直到其出现再对其进行操作。
```java
WebDriverWait wait = new WebDriverWait(driver, 300);
WebElement selectElement = wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("formLevel:levels_input")));
Select select = new Select(selectElement);
select.selectByVisibleText("SECURITY");
```

###模拟用户登录支付宝查询交易信息的程序
```java
import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;

import java.util.List;

public class Test {

    public static void main(String[] args) {
//        System.setProperty("webdriver.firefox.bin", "C:/Program Files (x86)/Mozilla Firefox/firefox.exe");
        System.setProperty("webdriver.chrome.driver", "D:/chromedriver.exe");
//        WebDriver driver = new FirefoxDriver();
        WebDriver driver = new ChromeDriver();
        driver.get("https://auth.alipay.com/login/index.htm?goto=https%3A%2F%2Fwww.alipay.com%2F");
        driver.findElement(By.id("J-input-user")).clear();
        driver.findElement(By.id("J-input-user")).sendKeys("支付宝账号");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        driver.findElement(By.id("password_rsainput")).clear();
        driver.findElement(By.id("password_rsainput")).sendKeys("支付宝密码");
        try {
            Thread.sleep(8000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("当前URL 0 ：" + driver.getCurrentUrl());
        driver.get("https://www.alipay.com/");
//        个人用户登录
        driver.findElement(By.className("personal-login")).click();
        System.out.println("当前URL 1 ：" + driver.getCurrentUrl());
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        boolean selected = driver.findElement(By.className("am-button-innerNav,button-myalipay")).isSelected();
        System.out.println("isSelected ：" + selected);
        boolean enabled = driver.findElement(By.className("am-button-innerNav,button-myalipay")).isEnabled();
        System.out.println("isEnabled ：" + enabled);
        boolean displayed = driver.findElement(By.className("am-button-innerNav,button-myalipay")).isDisplayed();
        System.out.println("isDisplayed ：" + displayed);
        driver.findElement(By.className("am-button-innerNav,button-myalipay")).click();
        System.out.println("当前URL 2 ：" + driver.getCurrentUrl());s
        //跳转到交易记录界面
        driver.get("https://consumeprod.alipay.com/record/index.htm");
        String currentUrl = driver.getCurrentUrl();
        System.out.println("当前URL 3 ：" + currentUrl);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        WebElement element = driver.findElement(By.id("J-keyword"));//获取输入框
        element.clear();
        element.sendKeys("此处填需要查询的交易号");
        WebElement select = driver.findElement(By.id("keyword"));
        List<WebElement> options = select.findElements(By.tagName("option"));
        for (WebElement op1 : options) {
            System.out.println(op1.getAttribute("seed"));
            System.out.println(op1.getAttribute("style"));
            System.out.println(String.format("Value is : %s", op1.getText()));//空
            System.out.println(op1.isSelected());
        }

        WebElement ahref = driver.findElement(By.className("ui-select-trigger,ui-select-middle-trigger"));
        System.out.println(ahref.getTagName());
        System.out.println(ahref.getText());
        System.out.println(ahref.isSelected());
        JavascriptExecutor js = (JavascriptExecutor) driver;
        js.executeScript("document.getElementById('keyword').style.display='list-item';");
        js.executeScript("document.getElementById('keyword').removeAttribute('smartracker');");
        js.executeScript("document.getElementById('keyword').options[1].selected = true;");

        js.executeScript("document.getElementById('J-select-range').style.display='list-item';");
        Select selectTime = new Select(driver.findElement(By.id("J-select-range")));
        selectTime.selectByIndex(3);
        System.out.println("selectTime.isMultiple() : " + selectTime.isMultiple());
        List<WebElement> allSelectedOptions = selectTime.getAllSelectedOptions();
        System.out.println("----------------");
        for (WebElement allSelectedOption : allSelectedOptions) {
            System.out.println(allSelectedOption.isSelected());
            allSelectedOption.getAttribute("seed");
        }
        selectTime = new Select(driver.findElement(By.id("keyword")));
        selectTime.selectByIndex(1);
        WebElement element1 = driver.findElement(By.id("J-set-query-form"));//拿到搜索按钮
        element1.submit();
        WebElement tr = driver.findElement(By.id("J-item-1"));//先获取tr
        WebElement td = tr.findElement(By.xpath("//*[@id=\"J-item-1\"]/td[5]/p[1]"));
        System.out.println(td.getTagName());
        System.out.println(td.getText());
        System.out.println(td.toString());
    }
}
```

###效果图
![](http://7xig3q.com1.z0.glb.clouddn.com/automatic-query-alipay-treasure-transaction-information.gif)


参考资料
【1】http://baike.baidu.com/subview/478050/6464537.htm
【2】http://blog.csdn.net/wanglha/article/details/39755613
【3】http://webdriver.blog.51cto.com/10517592/1673920
【4】http://blog.csdn.net/pf20050904/article/details/20052485
【5】http://www.open-open.com/doc/view/9f52277e5d9c4dd3b851ae54011e733a