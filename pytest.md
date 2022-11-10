#### pytest单元测试和自动化测试框架的关系：

1.单元测试框架：只是自动化测试框架的组成部分之一。

2.pom设计模式：只是自动化测试框架的组成部分之一。

#### pytest简介

1.非常成熟的python的单元框架，比unitest更灵活，容易上手。

2.可以和selenium,requests,appium结合实现web自动化，接口自动化。

3.实现用例的跳过以及rerun失败用例重试。

4.可以和allure结合生成美观的测试报告。

5.和Jenkins持续集成。

6.有很多强大的插件，可以实现很多的使用操作。

​	pytest-html	生成html格式的自动化测试报告

​	pytest-xdist	测试分布式执行，多cpu分发

​	pytest-ordering	改变测试用例的执行顺序

​	pytest-rerunfailures	用例失败后重跑

​	allure-pytest	生成美观的测试报告

​	安装:放到requirements.txt中，pip install -r requirements.txt

#### 使用pytest的默认规则以及基础应用

1.模块名以`test_`开头或`_test`结尾。

2.测试类以Test开头，并且不能init方法。

#### pytest运行方式

1.主函数模式

（1）运行所有：

```cmd
pytest.main()
```

（2）指定模块：

```python
pytest.main(['-vs','test_login.py'])
```

（3）指定目录：

```python
pytest.main(['-vs','./interface_testcase'])
```

（4）通过nodeid指定用例执行：nodeid由模块名，分隔符，类名，方法名，函数名组成。					

```python
pytest.main(['-vs','./interface_testcase/test_interface.py::test_04_func'])
```

​					

```python
pytest.main(['-vs','./interface_testcase/test_interface.py::TestInterface::test_03_method'])
```

2.命令行模式

（1）运行所有：

```cmd
pytest
```

（2）指定模块：

```cmd
pytest -vs test_login.py
```

（3）指定目录：

```cmd
pytest -vs ./interface_testcase
```

（4）通过nodeid指定用例执行：nodeid由模块名，分隔符，类名，方法名，函数名组成。					

```cmd
pytest -vs ./interface_testcase/test_interface.py::test_04_func
```

参数详解:

-s:表示输出调试信息，包括print打印信息。

-v:显示更多详细过程

-q:显示用例执行的简略过程

-n:支持多线程或者分布式运行测试用例

​	如：pytest -vs ./testcase/test_login.py -n 2

--rerun NUM:失败用例重跑

​	如：pytest -vs ./testcase --rerun 2

-x:表示只要有一个用例报错，测试停止

--maxfail=2 出现

​	如：pytest -vs ./testcase --maxfail 2

-k:根据测试用例的部分字符串指定测试用例

​	如：pytest -vs ./testcase -k "ao"：表示只执行用例名包含"ao"的用例

--html ./report/report.html:生成html的测试报告。

3.通过pytest.ini配置文件运行。

pytest.ini是pytest单元测试框架的核心配置文件。

（1）位置：一般放在项目的根目录

（2）编码：必须是ANSI，可以使用notepad++修改编码格式

（3）作用：改变pytest默认的行为

（4） 运行的规则：不管是主函数的模式运行，命令行模式运行，都会读取这个配置文件。

pytest.ini文件 :

```
[pytest]
addopts=-vs				#命令行的参数，用空格分隔
testpaths=./testcase	#测试用例的路径
python_files=test_*.py	#模块名的规则
python_classes=Test*	#类名的规则
python_functions=test	#方法名的规则
```



#### pytest执行测试用例的顺序

默认从上到下

改变默认的执行顺序：使用mark标记

```python
@pytest.mark.run(order=3)
```

#### 如何分组执行（冒烟，分模块执行，分接口和web执行）

smoke:冒烟用例，分布在各个模块里面

在全局配置文件pytest.ini中加

```
makers=

	smoke:冒烟用例

	usermanage:用户管理模块
```

pytest -vs -m "smoke"  (执行标记为smoke的用例：@pytest.mark.smoke)

pytest -vs -m "smoke or usermanage" (执行标记为smoke的用例和标记为usermanage的用例：@pytest.mark.smoke，@pytest.mark.usermanage)

#### pytest跳过用例

1.无条件跳过

@pytest.mark.skip()

2.有条件跳过

@pytest.mark.skipif (age>=18)



#### 全面认识requests库

requests.get()		get请求，通过params传参，dict格式。

requests.post()		post请求，通过data=None,json=None传参

​	1.使用data传参，那么请求头Content-Type：application/x-www-urlencoded，表示通过表单传参。

格式：a=1&b=2

参数是str类型：Content-Type:text/plain

重要的请求头：

Content-Type：传值的内容格式。

​	application/x-www-urlencoded		表单

​	multipart/from-data							表示里面有文件上传

​	text/plain												文本

​	binary													二进制文件

Accept:客户端接收的数据格式

X-Requested-With:异步请求

User-Agent:客户端的用户类型

Cookie:Cookie信息

2.使用json传参：

不管参数是dict或者str，Conten-Type:application/json 格式：{"a":1,"b":2}



总结：

data传只有键值对的dict或str。

json传参一般是dict，可以是键值对或非键值对

​	 



#### pytest框架管理测试用例

json.loads	#把json格式转化为dict格式

json.dumps	#把dict格式转化为json格式字符串



pytest还有一些前后置功能

每个用例之前和之后，以及每个类之前和之后。

```python
class TeseMashang:
    def setup(self):
        print("在每个用例之前执行一次。如：打开浏览器，加载网页")
        
    def teardown(self):
        print("在每个用例之后执行一次。如：关闭浏览器")
        
    def setup_class(self):
        print("在每个类之前执行一次。如：创建日志对象，创建数据库连接")
        
    def teardown_class(self):
        print("在每个类之后执行一次。如：关闭数据库连接，销毁日志对象")
        
    def test_baili(self):
        print("百里")
        
    def test_weiwei(self):
        print("微微")
```

如果有10000个用例，我希望的是在其中的三个用例做前后置。

部分前后置。使用fixtrue装饰器。

方法如下：

@pytest.fixture(scope="作用域",params="数据驱动",autouse="自动执行",ids="数据驱动时重命名参数名",name="给fixture作用的函数重命名")

1.scope参数：

- function		函数
- class		类
- module		模块
- package/session		会话

部分用例的前后置:

```python
import pytest

@pytest.fixture(scope="function")
def execute_database_sql():
    print("数据库查询")
    yield
    print("数据库校验")
    
class TestMashang:
    def test_baili(self):
        print("百里")
        
    def test_weiwei(self,execute_database_sql):
        print("微微")
```

部分类的前后置：

```python
import pytest

@pytest.fixture(scope="class")
def execute_database_sql():
    print("数据库查询")
    yield
    print("数据库校验")
    
class TestMashang:
    def test_baili(self):
        print("百里")
        
    def test_weiwei(self,execute_database_sql):
        print("微微")
        
@pytest.mark.usefixtures('execute_database_sql')       
class TestAaa:
    def test_aaa(self):
        print("test_aaa")
        
class TestBbb:
    def test_bbb(self):
        print("test_bbb")
```

会话的前后置，一般会结合conftest.py使用

1. conftest.py单独用于存放fixture固件的配置文件。
2. 在conftest.py中固件在使用时不需要导包。
3. 可以有多个conftest.py文件。

```python
import pytest
@pytest.fixture(scope="session",autouse=True)
execute_database_sql():
    print("在所有请求之前执行一次")
    yield
    print("在所有请求之后执行一次")
```



#### 接口关联（需要把所有的全局变量都集中管理）

比较成熟的是：通过一个单独的yaml文件管理。

接口自动化框架封装的第一步：接口关联封装。

```python
def read_yaml(s):
    with open(os.getcwd() + '/extract.yml', encodeing="uf-8") as f:
        value = yaml.load(stream=f, Loader=yaml.FullLoader)
        return value[s]


# 写入yaml文件
def write_yaml(data):
    with open(os.getcwd() + '/extract.yml', encodeing="uf-8", mode='a') as f:
        yaml.dump(data, stream=f, allow_unicode=True)


# 清空yaml文件
def clear_yaml():
    with open(os.getcwd() + '/extract.yml', encodeing="uf-8", mode='w') as f:
        f.truncate()
```

