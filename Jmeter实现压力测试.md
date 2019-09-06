# Jmeter实现压力测试
## 测试背景：

登录接口只做一次，其他接口做压测。

## 测试思路：

要想将登录与其他接口分开，可以使用多线程来实现。

## 测试问题：

登录接口获得cookie，其他接口需要使用cookie，线程变量作用域为该线程。

## 问题解决：

登录线程获得cookie后，将其设置成全局变量。其他接口线程直接使用全局变量。从而实现测试需求。


## 实现步骤

### 步骤1


登录线程Cookie管理器与其他接口线程的位置关系如下图：


![show](https://github.com/caimel/vue2Experience/blob/master/picture/show.png)
### 步骤2

在登录线程里添加登录请求，获取cookie变量，将cookie变量设置成全局变量，
获取cookie变量：在登录请求级别下添加Regular Expression Extractor(正则表达式提取器)配置如下图：

![show](https://github.com/caimel/vue2Experience/blob/master/picture/show.png)

将cookie变量设置成全局变量：在登录线程的最后添加BeanShell PostProcessor变量名由原来的cookie变为newcookie配置如下：


![show](https://github.com/caimel/vue2Experience/blob/master/picture/show.png)
### 步骤3

在俩线程组之间添加HTTP Cookie管理器，通过函数${__property(newcookie,,)}调用newcookie，其中${DatabaseHost}为测试请求ip，配置如下所示：


![show](https://github.com/caimel/vue2Experience/blob/master/picture/show.png)

### 步骤4

在其他接口线程中的开头添加与上一步相同的HTTP Cookie管理器


### 步骤5

在其他接口线程中添加需要做压力测试的接口


### 步骤6

最后配置压力测试参数：在1秒内执行完三个线程，并且循环3次。配置如下图：

![show](https://github.com/caimel/vue2Experience/blob/master/picture/show.png)
