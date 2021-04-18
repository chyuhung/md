# flask入门

## flask项目创建

克隆github上flask项目文件到本地：

`git clone https://github.com/greyli/helloflask.git`
`cd helloflask`

也可以在github点击该项目fork后，再复制自己项目地址：

`git clone https://github.com/chyuhung/helloflask.git`

## 开发环境搭建

### 需要安装的包

1、安装python，使用3.6及以上版本；

2、安装python包管理工具pip，可以使用`pip --version`命令查看已经安装的版本，安装后如需安装需要的其他包，可以使用命令`pip install [包名]`方便快速的进行安装；

3、安装pipenv，它是基于pip的python包管理工具，用法与pip相似，可以看作pip加强版，它解决了pip+virtualenv+requirements.txt的工作方式弊端，可以说它是pip、Pipfile、Virtualenv的结合体，安装完pip以后，使用命令`pip install pipenv`进行安装，使用命令`pipenv --version`查看安装的版本。

### 创建虚拟环境

1、进入项目目录，使用命令`pipenv install`即可为当前项目创建虚拟环境，它会创建“项目目录名+随机字符”的文件夹，并且安装pip，wheel、setuptools等基本的包，flask示例项目中包含Pipfile文件，文件中列出的包也会一并安装；

2、安装完成后，使用命令`pipenv shell`激活虚拟环境，使用`pipenv run`命令，如：`pipenv run python hello.py`则不需要关心是否激活了虚拟环境；

3、依赖管理：一个程序通常会使用很多的Python包，即依赖(dependency)。而程序不仅仅会在一台电脑上运行，程序部署上线时需要安装到远程服务器上，为了能顺利运行程序，不得不记下所有依赖包，然后使用pip或Pipenv安装，这些重复无用的工作当然应该避免。在以前通常使用pip搭配一个requirements.txt文件来记录依赖。但requirements.txt需要手动维护，在使用上不够灵活。Pipfile的出现就是为了替代难于管理的requirements.txt。在创建虚拟环境时，如果项目根目录下没有Pipfile文件，pipenv install命令还会在项目文件夹根目录下创建Pipfile和Pipfile.lock文件，前者用来记录项目依赖包列表，而后者记录了固定版本的详细依赖包列表。当使用Pipenv安装/删除/更新依赖包时，Pipfile以及Pipfile.lock会自动更新。

### 安装flask

使用命令`pipenv install flask`在项目文件夹下安装flask，pipenv能够自动管理虚拟环境，使用pipenv命令安装的包都在虚拟环境中，而需要在全局环境下安装包等才使用pip命令。

安装Flask会默认同时安装Flask依赖包。

![image-20201218155935922](flask入门.assets/image-20201218155935922.png)

### 开发环境

使用Pycharm打开项目文件夹能够自动识别虚拟环境，再进行简单的配置即可。

![image-20201218160202606](flask入门.assets/image-20201218160202606.png)

## 最小Flask程序

项目程序目录，示例代码为hello小程序。

![image-20201218161047653](flask入门.assets/image-20201218161047653.png)

app.py代码，一般简单程序命令使用app.py，也可以自定义，避免使用flask.py，会与Flask本身冲突。

```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def index():
	return '<h1>Hello Flask!</h1>'
```

传入Flask构造方法的第一个参数是模块或包名称，使用特殊变量`__name__`，python能够根据所处模块自动赋值到`__name__`，对app.py这个程序来说，值为app。

### 注册路由

在这个程序里，app.route()装饰器把根地址/和index()函数绑定起来，当用户访问这个URL时就会触发index()函数。

在一个Web应用里，客户端和服务器上的Flask程序的交互可以简单概括为以下几步：
1）用户在浏览器输入URL访问某个资源。
2）Flask接收用户请求并分析请求的URL。
3）为这个URL找到对应的处理函数。
4）执行函数并生成响应，返回给浏览器。
5）浏览器接收并解析响应，将信息显示在页面中。

### URL

绑定多个URL到视图：

```python
@app. route('/hi')
@app.route('/hello')
def say_hello():
return '<h1>Hello，Flask !</h1>'
```

动态URL

即在URL中添加变量，使用“<变量名>”的形式表示，Flask处理请求时会把变量传入视图函数。

```
#test
@app.route('/hi/<new_name>')
def hi_new_name(new_name):
    response='<h1>hi,%s</h1>'%new_name
    return  response
```

URL中包含变量，则将传入视图函数的字符串称为URL规则，Flask将请求URL和URL规则匹配，如示例代码，/hi/peter，/hi/bob等请求都能够触发这个视图函数。

![image-20201219175818098](flask入门.assets/image-20201219175818098.png)

![image-20201219180219958](flask入门.assets/image-20201219180219958.png)

当URL中包含变量，如果用户访问的URL中没有变量，如/hi，Flask在匹配失败后会返回404错误。

![image-20201219180449961](flask入门.assets/image-20201219180449961.png)

常见的做法时在app.route()装饰器中使用deefaults参数设置URL变量的默认值，该参数接收字典作为输入，存储URL变量和默认值的映射。

```
#test
@app.route('/hi/',defaults={'new_name':'chyuhung'})
def hi_new_name(new_name):
    response='<h1>hi,%s</h1>'%new_name
    return  response
```

设置默认值new_name为“chyuhung”。

![image-20201219181259517](flask入门.assets/image-20201219181259517.png)

### URL与端点

Web程序中，URL无处不在，如果程序中URL都以硬编码方式写出，将会大大降低代码易用性。当修改了某个URL规则，程序对应的URL都要一个个进行修改，更好的方法时使用Flask提供的usr_for()函数获取URL，当路由定义中的URL规则被修改时，这个函数能够返回正确的URL。

```
#test01
@app.route('/test01')
def test01():
    url=url_for('test01')
    response = "hello,test01!url is %s"%url
    return response
```

这个路由的端点即视图函数的名称test01，调用url_for('test01')即可获取对应的URL，即“/test01”。

![image-20201219182451307](flask入门.assets/image-20201219182451307.png)

如果URL含有动态部分，在url_for()函数里也需要传入相应的参数。

```
#test01
@app.route('/test01/<test_num>')
def test01(test_num):
    url=url_for('test01',test_num='01')
    response = "hello,test01!url is %s"%url
    return response
```

![image-20201219182911489](flask入门.assets/image-20201219182911489.png)

使用url_for()函数生成的URL是相对URL(即内部URL)，即URL中的path部分，比如“/hello”，不包含根URL。相对URL只能在程序内部使用。如果想要生成供外部使用的绝对URL，可以在使用url_for()函数时，将_external参数设为True，这会生成完整的URL。

### Flask命令

通过创建任意一个函数，并为其添加app.cli.command()装饰器，就可以注册一个flask命令。

```
#自定义命令
@app.cli.command()
def test():
    click.echo('this is a test')
```

![image-20201219183619753](flask入门.assets/image-20201219183619753.png)

函数的名称即为命令名称，这里注册的命令即test，使用flask test命令来触发函数。作为替代，可以在app.cli.command()装饰器中传入参数来设置命令名称，比如app.cli.command('hello')会把命令名称设置为hello，完整的命令即flask hello。

```
#自定义命令
@app.cli.command('hello')
def test():
    click.echo('this is a test')
```

![image-20201219183829360](flask入门.assets/image-20201219183829360.png)

此外，借助click模块的echo()函数，可以在命令行界面输出字符。

### 模板与静态文件

一个完整的网站当然不能只返回用户一句“Hello,World!”，需要模板(template)和静态文件(static file）来生成更加丰富的网页。模板即包含程序页面的HTML文件，静态文件则是需要在HTML文件中加载的CSS和Java Script文件，以及图片、字体文件等资源文件。默认情况下，模板文件存放在项目根目录中的templates文件夹中，静态文件存放在static文件夹下，这两个文件夹需要和包含程序实例的模块处于同一个目录下，对应的项目结构示例如下所示：

```
hello/
- templates/
- static/
- app.py
```

  

## Flask与http

![image-20201221120651734](flask入门.assets/image-20201221120651734.png)

![image-20201221120711310](flask入门.assets/image-20201221120711310.png)

当用户访问一个URL，浏览器便生成对应的HTTP请求，经由互联网发送到对应的Web服务器。Web服务器接收请求，通过WSGI将HTTP格式的请求数据转换成Flask程序能够使用的Python数据。在程序中，Flask根据请求的URL执行对应的视图函数，获取返回值生成响应。响应依次经过wSGI转换生成HTTP响应，再经由Web服务器传递，最终被发出请求的客户端接收。浏览器渲染响应中包含的HTML和CSS代码，并执行Java Script代码，最终把解析后的页面呈现在用户浏览器的窗口中。

### URL组成

示例URL

```
http://helloflask.com/hello?name=Grey
```

![image-20201221120853513](flask入门.assets/image-20201221120853513.png)

这个URL后面的?name=Grey部分是查询字符串( querystring)。URL中的查询字符串用来向指定的资源传递参数。查询字符串从问号?开始，以键值对的形式写出，多个键值对之间使用&分隔。

浏览器与服务器之间交互的数据被称为报文( message)，请求时浏览器发送的数据被称为请求报文( request message)，而服务器返回的数据被称为响应报文(response message) 。

### 请求类型

HTTP通过方法来区分不同的请求类型。当直接访问一个页面时，请求的方法是GET;当在某个页面填写了表单并提交时，请求方法则通常为POST。

![image-20201221121129097](flask入门.assets/image-20201221121129097.png)

### request对象

Flask的请求对象request，这个请求对象封装了从客户端发来的请求报文，能从它获取请求报文中的所有数据。请求解析和响应封装实际上大部分是由Werkzeug完成的，Flask子类化Werkzeug的请求（Request)和响应(Response)对象并添加了和程序相关的特定功能。



示例URL

```
http://helloflask.com/hello?name=Grey
```

使用request对象属性获取请求URL![image-20201221121428976](flask入门.assets/image-20201221121428976.png)

request对象常用的属性和方法

![image-20201221130310056](flask入门.assets/image-20201221130310056.png)

### Flask处理请求

当请求的URL与某个视图函数的URL规则匹配成功时，对应的视图函数就会被调用。使用flask routes命令可以查看程序中定义的所有路由，这个列表由app.url_map解析得到。

![image-20201221131008727](flask入门.assets/image-20201221131008727.png)

### 设置http监听方法

可以在app.route()装饰器中使用methods参数传入一个包含监听的HTTP方法的可迭代对象。比如，下面的视图函数同时监听GET请求和POST请求:

```
@app.route('/hi',methods=['GET','POST'])
def hi():
    return redirect(url_for('hello'))
```

### URL处理

URL规则中的变量部分有一些特别, <int:year>表示为year变量添加了一个int转换器，Flask在解析这个URL变量时会将其转换为整型。URL中的变量部分默认类型为字符串，但Flask提供了一些转换器可以在URL规则里使用。

![image-20201221131614013](flask入门.assets/image-20201221131614013.png)

```python
#use int URL converter
@app.route('/goto/<int:year>')
def goto(year):
    return 'welcome to %d'%(2020+year)
```



![image-20201221131845292](flask入门.assets/image-20201221131845292.png)

在用法上唯一特别的是any转换器，需要在转换器后添加括号来给出可选值，即"<any(value1, value2，...):变量名>"。

```
@app.route('/colors/<any(blue, white, red):color>')
```

如果想要在any转换器中传入一个预先定义的列表，可以通过格式化字符串的方式（使用%或是format函数）来构建URL规则字符串。

```python
colors=['blue','white','dark','red','green','yellow']
@app.route('/colors/<any(%s):color>'%str(colors)[1:-1])
```

### 请求钩子

有时需要对请求进行预处理( preprocessing)和后处理( postprocessing)，这时可以使用Flask提供的一些请求钩子(Hook)，它们可以用来注册在请求处理的不同阶段执行的处理函数(或称为回调函数，即Callback)。这些请求钩子使用装饰器实现，通过程序实例app调用，用法很简单:以before_request钩子(请求之前)为例，当对一个函数附加了app.before_request装饰器后，就会将这个函数注册为before_request处理函数，每次执行请求前都会触发所有before_request处理函数。

![image-20201221133519896](flask入门.assets/image-20201221133519896.png)

示例代码

```
@app.before_request
def do_something():
    return 'your request got!'
```

![image-20201221133827965](flask入门.assets/image-20201221133827965.png)

处理流程示意图

![image-20201221133921221](flask入门.assets/image-20201221133921221.png)

请求钩子常见应用场景：

before_first_request:在玩具程序中，运行程序前需要进行一些程序的初始化操作，比如创建数据库表，添加管理员用户。这些工作可以放到使用before_first_request装饰器注册的函数中。
before_request:比如网站上要记录用户最后在线的时间，可以通过用户最后发送的请求时间来实现。为了避免在每个视图函数都添加更新在线时间的代码，可以仅在使用before_request钩子注册的函数中调用这段代码。
after_request:经常在视图函数中进行数据库操作，比如更新、插入等，之后需要将更改提交到数据库中。提交更改的代码就可以放到after_request钩子注册的函数中。

另一种常见的应用是建立数据库连接，通常会有多个视图函数需要建立和关闭数据库连接，这些操作基本相同。一个理想的解决方法是在请求之前( before_request)建立连接，在请求之后(teardown_request)关闭连接。通过在使用相应的请求钩子注册的函数中添加代码就可以实现。这很像单元测试中的set Up()方法和tear Down()方法。

重要：after_request钩子和after_this_request钩子必须接收一个响应类对象作为参数，并且返回同一个或更新后的响应对象。

### http响应

响应报文主要由协议版本、状态码( status code)、原因短语( reason phrase) 、响应首部和响应主体组成。

![image-20201221135817689](flask入门.assets/image-20201221135817689.png)

http状态码用来表示请求处理的结果

![image-20201221135858263](flask入门.assets/image-20201221135858263.png)

当关闭调试模式时，即FLASK_ENV使用默认值production，如果程序出错，Flask会自动返回500错误响应;而调试模式下则会显示调试信息和错误堆栈。

### 在Flask中生成响应

响应在Flask中使用response对象表示，响应报文中的大部分内容由服务器处理。Flask会先判断是否可以找到与请求URL相匹配的路由，如果没有则返回404响应。如果找到，则调用对应的视图函数，视图函数的返回值构成了响应报文的主体内容，正确返回时状态码默认为200。Flask会调用make_response()方法将视图函数返回值转换为响应对象。

完整地说，视图函数可以返回最多由三个元素组成的元组:响应主体、状态码、首部字段。其中首部字段可以为字典，或是两元素元组组成的列表。

![image-20201221144648754](flask入门.assets/image-20201221144648754.png)

```
@app.route('/hi',methods=['GET','POST'])
def hi():
    return '',205
```

![image-20201221145541620](flask入门.assets/image-20201221145541620.png)

```
@app.route('/hi')
def hi():
    return '',302,'http://www.example.com'
```



![image-20201221154403114](flask入门.assets/image-20201221154403114.png)

### 重定向

![image-20201221145954677](flask入门.assets/image-20201221145954677.png)

对于重定向这一类特殊响应，Flask提供了一些辅助函数。除了手动生成302响应，可以使用Flask提供的redirect()函数来生成重定向响应，重定向的目标URL作为第一个参数。

在Web程序中，经常需要进行重定向。比如，当某个用户在没有经过认证的情况下访问需要登录后才能访问的资源，程序通常会重定向到登录页面。

```
@app.route('/hi')
def hi():
    return redirect('http://www.example.com')
```

![image-20201221154235592](flask入门.assets/image-20201221154235592.png)

使用redirect()函数时，默认的状态码为302，即临时重定向。如果想修改状态码，可以在redirect()函数中作为第二个参数或使用code关键字传入。

如果要在程序内重定向到其他视图，那么只需在redirect()函数中使用url_for()函数生成目标URL即可。

```
@app.route('/hi')
def hi():
    return redirect(url_for('hello'))#重定向到“hello”
```

### 错误响应

大多数情况下，Flask会自动处理常见的错误响应。HTTP错误对应的异常类在Werkzeug的werkzeug.exceptions模块中定义，抛出这些异常即可返回对应的错误响应。如果想手动返回错误响应，更方便的方法是使用Flask提供的abort()函数。

在abort()函数中传入状态码即可返回对应的错误响应。

```
@app.route('/404')
def not_found():
    abort(404)
```

abort()函数前不需要使用return语句，但一旦abort()函数被调用，abort()函数之后的代码将不会被执行。

#### 响应格式

通常使用HTML格式，不同格式需要不同的MIME类型，在首部Content-Type字段中定义。

![image-20201221155906464](flask入门.assets/image-20201221155906464.png)

MIME类型(又称为media type或content type）是一种用来标识文件类型的机制，它与文件扩展名相对应，可以让客户端区分不同的内容类型，并执行不同的操作。一般的格式为“类型名/子类型名”，其中的子类型名一般为文件扩展名。比如，HTML的MIME类型为“text/html” , png图片的MIME类型为“image/png”。

如果想使用其他MIME类型，可以通过Flask提供的make_response()方法生成响应对象，传入响应的主体作为参数，然后使用响应对象的mimetype属性设置MIME类型。

```
@app.route('/test')
def test():
    response=make_response('this is a test')
    response.mimetype='text/plain'
    return response
```

常用的数据格式有纯文本、HTML、XML和JSON。

#### 纯文本

MIME类型:text/plain

事实上，其他几种格式本质上都是纯文本。比如同样是一行包含HTML标签的文本“<h1>Hello，Flask!</h1>”，当MIME类型设置为纯文本时，浏览器会以文本形式显示“<h1>Hello,Flask!</h1>” ;当MIME类型声明为text/html时，浏览器则会将其作为标题1样式的HTML代码渲染。

```
Note
to: Peter
from: Jane
heading: Reminder
body: Don't forget the party!
```

#### HTML

MIME类型:text/html

HTML ( https://www.w3.org/html/)指Hypertext MarkupLanguage(超文本标记语言)，是最常用的数据格式，也是Flask返回响应的默认数据类型。

```html
<! DOCTYPE html>
        <html>
        <head></head>
        <body>
            <h1>Note</h1>
            <p>to: Peter</p>
            <p>from: Jane</p>
            <p>heading: Reminder</p>
            <p>body: <strong>Don't forget the party! </strong></p>
        </body>
        </html>
```

因为HTML常常包含丰富的信息，可以直接将HTML嵌入页面中，处理起来比较方便。因此，在普通的HTTP请求中使用HTTP作为响应的内容，这也是默认的数据类型。

#### XML

MIME类型: application/xml

XML (https://www.w3.org/XML/)指Extensible MarkupLanguage(可扩展标记语言)，它是一种简单灵活的文本格式，被设计用来存储和交换数据。XML的出现主要就是为了弥补HTML的不足:对于仅仅需要数据的请求来说，HTML提供的信息太过丰富了，而且不易于重用。XML和HTML一样都是标记性语言，使用标签来定义文本，但HTML中的标签用于显示内容，而XML中的标签只用于定义数据。XML一般作为AJAX请求的响应格式，或是WebAPI的响应格式。

```xml
<? xml version="1.0" encoding="UTF-8"? >
        <note>
            <to>Peter</to>
            <from>Jane</from>
            <heading>Reminder</heading>
            <body>Don't forget the party! </body>
        </note>
```

#### JSON

MIME类型: application/json

JSON ( http://json.org/)指Java Script Object Notation ( JavaScript对象表示法)，是一种流行的、轻量的数据交换格式。它的出现又弥补了XML的诸多不足:XML有较高的重用性，但XML相对于其他文档格式来说体积稍大，处理和解析的速度较慢。JSON轻量，简洁，容易阅读和解析，而且能和Web默认的客户端语言JavaScript更好地兼容。JSON的结构基于“键值对的集合”和“有序的值列表”，这两种数据结构类似Python中的字典(dictionary)和列表( list)。正是因为这种通用的数据结构，使得JSON在同样基于这些结构的编程语言之间交换成为可能。

```json
	{
            "note":{
                "to":"Peter",
                "from":"Jane",
                "heading":"Remider",
                "body":"Don't forget the party! "
            }
        }
```

Flask通过引入Python标准库中的json模块（或simplejson，如果可用)为程序提供了JSON支持。可以直接从Flask中导入json对象，然后调用dumps()方法将字典、列表或元组序列化(serialize)为JSON字符串，再使用前面介绍的方法修改MIME类型，即可返回JSON响应。

```
@app.route('/test01')
def test01():
    data={
        'name':'chyuhung',
        'number':'77777'
    }
    response=make_response(json.dumps(data))
    response.mimetype='application/json'
    return response
```

![image-20210106154343595](flask入门.assets/image-20210106154343595.png)

一般并不直接使用json模块的dumps()、load()等方法，因为Flask通过包装这些方法提供了更方便的jsonify()函数。

```
from flask import jsonify
@app.route('/test02')
def test02():
    return jsonify(name='chyuhung',num='7')
```

![image-20210106154639602](flask入门.assets/image-20210106154639602.png)

### Cookie

HTTP是无状态(stateless)协议。也就是说，在一次请求响应结束后，服务器不会留下任何关于对方状态的信息。但是对于某些Web程序来说，客户端的信息必须被记住，比如登录状态，这样才可以根据用户的状态来返回不同的响应。为了解决这类问题，就有了Cookie技术。Cookie技术通过在请求和响应报文中添加Cookie数据来保存客户端的状态信息。

Cookie指Web服务器为了存储某些数据（比如用户信息）而保存在浏览器上的小型文本数据。浏览器会在一定时间内保存它，并在下一次向同一个服务器发送请求时附带这些数据。Cookie通常被用来进行用户会话管理(比如登录状态)，保存用户的个性化信息（比如语言偏好，视频上次播放的位置，网站主题选项等）以及记录和收集用户浏览数据以用来分析用户行为等。

在Flask中，如果想要在响应中添加一个cookie，最方便的方法是使用Response类提供的set_cookie()方法。要使用这个方法，需要先使用make_response()方法手动生成一个响应对象，传入响应主体作为参数。这个响应对象默认实例化内置的Response类。

![image-20210106155244308](flask入门.assets/image-20210106155244308.png)

除上表中的属性和方法以外，Response类拥有和request类相同的get_json()方法、is_json()方法和json属性。

set_cookie()方法支持多个参数设置cookie选项。

![image-20210106155517366](flask入门.assets/image-20210106155517366.png)

```
from flask import Flask,make_response
@app.route('/test03/<name>')
def test03(name):
    response=make_response(redirect(url_for('hello')))
    response.set_cookie('name',name)
    return response
```

浏览器输入URL，重定向到hello程序

![image-20210106174713954](flask入门.assets/image-20210106174713954.png)

![image-20210106174743101](flask入门.assets/image-20210106174743101.png)

查看浏览器cookie

![image-20210106174930821](flask入门.assets/image-20210106174930821.png)

当浏览器保存了服务器端设置的Cookie后，浏览器再次发送到该服务器的请求会自动携带设置的Cookie信息，Cookie的内容存储在请求首部的Cookie字段中。



![image-20210106175124776](flask入门.assets/image-20210106175124776.png)

### 安全的cookie

在编程中，session指用户会话(user session) ，又称为对话(dialogue)，即服务器和客户端/浏览器之间或桌面程序和用户之间建立的交互活动。在Flask中, session对象用来加密Cookie。默认情况下，它会把数据存储在浏览器上一个名为session的cookie里。

session通过密钥对数据进行签名以加密数据，因此，得先设置一个密钥。这里的密钥就是一个具有一定复杂度和随机性的字符串，比如“Drmhze6EPcvOf N_81Bj-n A”。
程序的密钥可以通过Flask.secret_key属性或配置变量SECRET_KEY设置。

```
app.secret_key = 'secret string'
```

更安全的做法是把密钥写进系统环境变量（在命令行中使用export或set命令)，或是保存在.env文件中。

```
SECRET_KEY=secret string
```

### 模拟用户认证

```
from flask import redirect,session,url_for
@app.route('/logintest')
def logintest():
    session['logged_in']=True
    return redirect(url_for('hello'))
```



![image-20210107094046091](flask入门.assets/image-20210107094046091.png)

这个登录视图只是简化的示例，在实际的登录中，需要在页面上提供登录表单，供用户填写账户和密码，然后在登录视图里验证账户和密码的有效性。session对象可以像字典一样操作，向session中添加一个logged-in cookie，将它的值设为True，表示用户已认证。

当支持用户登录后，就可以根据用户的认证状态分别显示不同的内容。

```
from flask import session,request
@app.route('/hellotest')
def hellotest():
    name=request.args.get('name')
    if name is None:
        name=request.cookies.get('name','xixi')
    response='<h1>hello,%s</h1>'%name
    if "logged_in" in session:
        response +='YES'
    else:
        response +='NO'
    return response
    
from flask import redirect,session,url_for
@app.route('/logintest')
def logintest():
    session['logged_in']=True
    return redirect(url_for('hellotest'))
```

![image-20210107110319228](flask入门.assets/image-20210107110319228.png)

程序中的某些资源仅提供给登入的用户，比如管理后台，这时就可以通过判断session是否存在logged-in键来判断用户是否认证。

```
@app.route('/admintest')
def admintest():
    if 'logged_in' not in session:
        abort(403)
    return 'YES'
```

![image-20210107111453347](flask入门.assets/image-20210107111453347.png)

通过判断logged_in是否在session中，可以实现:如果用户已经认证，会返回一行提示文字，否则会返回403错误响应。
登出用户的logout视图也非常简单，登出账户对应的实际操作其实就是把代表用户认证的logged-in cookie删除，这通过session对象的pop方法实现。

```
from flask import session,request
@app.route('/hellotest')
def hellotest():
    name=request.args.get('name')
    if name is None:
        name=request.cookies.get('name','xixi')
    response='<h1>hello,%s</h1>'%name
    if "logged_in" in session:
        response +='YES'
    else:
        response +='NO'
    return response

@app.route('/logouttest')
def logouttest():
    if 'logged_in' in session:
        session.pop('logged_in')
    return redirect(url_for('hellotest'))
```



![image-20210107112917015](flask入门.assets/image-20210107112917015.png)

![image-20210107112944962](flask入门.assets/image-20210107112944962.png)

默认情况下，session cookie会在用户关闭浏览器时删除。通过将session.permanent属性设为True可以将session的有效期延长为Flask. permanent_session_lifetime属性值对应的datetime.timedelta对象，也可通过配置变量PERMANENT_SESSION_LIFETIME设置，默认为31天。

### Flask上下文变量

![image-20210107114435506](flask入门.assets/image-20210107114435506.png)

因为g存储在程序上下文中，而程序上下文会随着每一个请求的进入而激活，随着每一个请求的处理完毕而销毁，所以每次请求都会重设这个值。通常会使用它结合请求钩子来保存每个请求处理前所需要的全局变量，比如当前登入的用户对象，数据库连接等。在前面的示例中，在hello视图中从查询字符串获取name的值，如果每一个视图都需要这个值，那么就要在每个视图重复这行代码。借助g可以将这个操作移动到before_request处理函数中执行，然后保存到g的任意属性上。

```
from flask import g
        @app.before_request
        def get_name():
            g.name = request.args.get('name')
```

### HTTP referer

HTTP referer(起源为referrer在HTTP规范中的错误拼写)是一个用来记录请求发源地址的HTTP首部字段(HTTP_REFERER)，即访问来源。当用户在某个站点单击链接，浏览器向新链接所在的服务器发起请求，**请求的数据中包含的HTTP_REFERER字段记录了用户所在的原站点URL**。
这个值通常会用来追踪用户，比如记录用户进入程序的外部站点，以此来更有针对性地进行营销。在Flask中,referer的值可以通过请求对象的referrer属性获取，即request.referrer(正确拼写形式)。

```
@app.route('/tztest01')
def tztest01():
    response='there is test01,goto tztest03:<a href="%s">tztest03</a>'%url_for('tztest03')
    return response

@app.route('/tztest02')
def tztest02():
    response='there is test02,goto tztest03:<a href="%s">tztest03</a>'%url_for('tztest03')
    return response

@app.route('/tztest03')
def tztest03():
    #do something
    return  redirect(request.referrer)
```

![image-20210107160232519](flask入门.assets/image-20210107160232519.png)

但是在很多种情况下，referrer字段会是空值，比如用户在浏览器的地址栏输入URL，或是用户出于保护隐私的考虑使用了防火墙软件或使用浏览器设置自动清除或修改了referrer字段。需要添加一个备选项。

```
return  redirect(request.referrer or url_for('hellotest'))
```

除了自动从referrer获取，另一种更常见的方式是在URL中手动加入包含当前页面URL的查询参数，这个查询参数一般命名为next。

```
@app.route('/tztest04')
def tztest04():
    response='there is test04,goto tztest05:<a href="%s">tztest05</a>'%url_for('tztest03',next=request.full_path)
    return response

@app.route('/tztest05')
def tztest05():
    #do something
    return redirect(request.args.get('next'))
```



![image-20210107161424707](flask入门.assets/image-20210107161424707.png)

在程序内部只需要使用相对URL，所以这里使用request.full_path获取当前页面的完整路径。在do_something视图中，获取这个next值，然后重定向到对应的路径。

### URL安全验证

安全问题不容小觑，鉴于referer和next容易被篡改的特性，如果不对这些值进行验证，则会形成开放重定向(Open Redirect)漏洞。
以URL中的next参数为例，next变量以查询字符串的方式写在URL里，因此任何人都可以发给某个用户一个包含next变量指向任何站点的链接。

```
http://localhost:5000/do-something?next=http://helloflask.com
```

确保URL安全的关键就是判断URL是否属于程序内部，创建一个URL验证函数is_safe_url()，用来验证next变量值是否属于程序内部URL。

```
def redirect_back(default='hellotest', **kwargs):
    for target in request.args.get('next'), request.referrer:
        if not target:
            continue
        if is_safe_url(target):
            return redirect(target)
    return redirect(url_for(default, **kwargs))
```

### AJAX异步请求

在传统的Web应用中，程序的操作都是基于请求响应循环来实现的。每当页面状态需要变动，或是需要更新数据时，都伴随着一个发向服务器的请求。当服务器返回响应时，整个页面会重载，并渲染新页面。
这种模式会带来一些问题。首先，频繁更新页面会牺牲性能，浪费服务器资源，同时降低用户体验。另外，对于一些操作性很强的程序来说，重载页面会显得很不合理。比如做了一个Web计算器程序，所有的按钮和显示屏幕都很逼真，但当单击“等于”按钮时，要等到页面重新加载后才在显示屏幕上看到结果，这显然会严重影响用户体验。AJAX技术可以完美地解决这些问题。

AJAX指异步Javascript和XML ( Asynchronous Java Script AndXML)，它不是编程语言或通信协议，而是一系列技术的组合体。简单来说， AJAX基于XMLHttp Request( https://xhr.spec.whatwg.org/)可以在不重载页面的情况下和服务器进行数据交换。加上Java Script和DOM (DocumentObject Model，文档对象模型)，就可以在接收到响应数据后局部更新页面。而XML指的则是数据的交互格式，也可以是纯文本( Plain Text) . HTML或JSON。顺便说一句，XMLHttp Request不仅支持HTTP协议，还支持FILE和FTP协议。

### 使用jQuery发送AJAX请求

jQuery是流行的Java Script库，它包装了Java Script，可以通过更简单的方式编写Java Script代码。对于AJAX，它提供了多个相关的方法，使用它可以很方便地实现AJAX操作。更重要的是，jQuery处理了不同浏览器的AJAX兼容问题，只需要编写一套代码，就可以在所有主流的浏览器正常运行。

使用jQuery实现AJAX并不是必须的，可以选择使用原生的XMLHttp Request、其他Java Script框架内置的AJAX接口，或是使用更新的Fetch APl ( https://fetch.spec.whatwg.org/)来发送异步请求。
在示例程序中，将使用全局jQuery函数ajax()发送AJAX请求。ajax()函数是底层函数，有丰富的自定义配置。以下是ajax()函数支持的参数。

![image-20210111141336168](flask入门.assets/image-20210111141336168.png)

### 返回局部数据

#### 纯文本或局部HTML模板

纯文本可以在Java Script用来直接替换页面中的文本值，而局部HTML则可以直接到插入页面中，比如返回评论列表：

```
@app.route('/comments/<int:post_id>')
        def get_comments(post_id):
            ...
            return render_template('comments.html')
```

#### JSON数据

JSON数据可以在Java Script中直接操作：

```
@app.route('/profile/<int:user_id>')
        def get_profile(user_id):
            ...
            return jsonify(username=username, bio=bio)
```

#### 空值

有些时候，程序中的某些接收AJAX请求的视图并不需要返回数据给客户端，比如用来删除文章的视图。这时可以直接返回空值，并将状态码指定为204(表示无内容)，比如:

```
@app.route('/post/delete/<int:post_id>', methods=['DELETE'])
        def delete_post(post_id):
            ...
            return '', 204
```

#### 异步加载文章示例

在示例程序的对应页面中，将显示一篇很长的虚拟文章，文章正文下方有一个“加载更多”按钮，当加载按钮被单击时，会发送一个AJAX请求获取文章的更多内容并直接动态插入到文章下方。

```
@app.route('/posttest')
def posttest():
    post_body=generate_lorem_ipsum(n=2)#生成两段随机文本
    return '''
    <h1>a very long post</h1>
    <div class="body">%s</div>
    <button id="load">load more</button>
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script type="text/javascript">
    $(function(){
        $('#load').click(function(){
            $.ajax({
                url:'/moretest',        //目标URL
                type:'get',             //请求方法
                success:function(data){ //返回2xx响应后触发的回调函数
                $('.body').append(data);//将返回的响应插入页面中
                }
            })
        })
    })
    </script>
    '''%post_body
    
    
@app.route('/moretest')
def moretest():
    return generate_lorem_ipsum(n=1)
```

### http服务端推送

实现服务器端推送的一系列技术被合称为HTTP Server Push (HTTP服务器端推送)，目前常用的推送技术如：

![image-20210113114457698](flask入门.assets/image-20210113114457698.png)

### web安全防范

```
@app.route('/students')
        def bobby_table():
            password = request.args.get('password')
            cur = db.execute("SELECT ＊ FROM students WHERE password='%s'; " % password)
            results = cur.fetchall()
            return results
```

在实际应用中，敏感数据需要通过表单提交的POST请求接收，这里为了便于演示,通过查询参数接收。通过查询字符串获取用户输入的查询参数，并且不经过任何处理就使用字符串格式化的方法拼接到SQL语句中。在这种情况下，如果攻击者输入的password参数值为“" or 1=1--”，即`http://example.com/students?password=' or 1=1--`，那么最终视图函数中被执行的SQL语句将变为:

```
 SELECT ＊ FROM students WHERE password='' or 1=1--; '
```

这时会把students表中的所有记录全部查询并返回，也就意味着所有的记录都被攻击者窃取了。更可怕的是，如果攻击者将password参数的值设为"; drop table users;--”，那么查询语句就会变成：

```
SELECT ＊ FROM students WHERE password=''; drop table students; --;
```

执行这个语句会把students表中的所有记录全部删除掉。

反射型XSS又称为非持久型XSS (Non-Persistent XSS)。当某个站点存在XSS漏洞时，这种攻击会通过URL注入攻击脚本，只有当用户访问这个URL时才会执行攻击脚本。在本章前面介绍查询字符串和cookie时引入的示例就包含反射型XSS漏洞，如：

```
@app.route('/escapetest')
def escapetest():
    name=request.args.get('name')
    return "<h1>hello,%s</h1>"%name
```

这个视图函数接收用户通过查询字符串传入的数据，未做任何处理就把它直接插入到返回的响应主体中，返回给客户端。如果某个用户输入了一段JavaScript代码作为查询参数name的值，如：

```
http://127.0.0.1:5000/escapetest?name=<script>alert('bingo!');</script>
```

![image-20210113142719767](flask入门.assets/image-20210113142719767.png)

能够执行alert()函数就意味着通过这种方式可以执行任意Java Script代码。即攻击者通过Java Script几乎能够做任何事情:窃取用户的cookie和其他敏感数据，重定向到钓鱼网站，发送其他请求，执行诸如转账、发布广告信息、在社交网站关注某个用户等。

### 防范措施

#### html转义

这里的转义和Python中的概念相同，即消除代码执行时的歧义，也就是把变量标记的内容标记为文本，而不是HTML代码。具体来说，这会把变量中与HTML相关的符号转换为安全字符，以避免变量中包含影响页面输出的HTML标签或恶意的Java Script代码。

比如，可以使用Jinja2提供的escape()函数对用户传入的数据进行转义：

```
from jinja2 import escape
@app.route('/escapetest')
def escapetest():
    name=request.args.get('name')
    return "<h1>hello,%s</h1>"%escape(name)
```



![image-20210113143038370](flask入门.assets/image-20210113143038370.png)

以某个程序的用户资料页面为例，来演示一下转义无法完全避免的XSS攻击。程序允许用户输入个人资料中的个人网站地址，通过下面的方式显示在资料页面中：

```
<a href="{{ url }}">Website</a>
```

其中{{ url }}部分表示会被替换为用户输入的url变量值。如果不对URL进行验证，那么用户就可以写入Java Script代码，比如“javascript:alert('Bingo!');”。因为这个值并不包含会被转义的<和>。最终页面上的链接代码会变为：

```
<a href="javascript:alert(' Bingo! '); ">Website</a>
```

当用户单击这个链接时，就会执行被注入的攻击代码。
另外，程序还允许用户自己设置头像图片的URL。这个图片通过下面的方式显示：

```
<img src="{{ url }}">
```

类似的，f url }}部分表示会被替换为用户输入的url变量值。如果不对输入的URL进行验证，那么用户可以将url设为“123" onerror="alert('Bingo! ')”，最终的<img>标签就会变为：

```
<img src="123" onerror="alert(' Bingo! ')">
```

在这里因为src中传入了一个错误的URL，浏览器便会执行onerror属性中设置的JavaScript代码。

## 模板

前面示例中，当用户访问程序的根地址时，视图函数会向客户端返回一行HTML代码。然而，一个完整的HTML页面往往需要几十行甚至上百行代码，如果都写到视图函数里，那可真是个噩梦。这样的代码既不简洁也难于维护，正确的做法是把HTML代码存储在单独的文件中，以便让程序的业务逻辑和表现逻辑分离，即控制器和用户界面的分离。

在动态Web程序中，视图函数返回的HTML数据往往需要根据相应的变量(比如查询参数）动态生成。当HTML代码保存到单独的文件中时，没法再使用字符串格式化或拼接字符串的方式来在HTML代码中插入变量，这时需要使用模板引擎( templateengine)。借助模板引擎，可以在HTML文件中使用特殊的语法来标记出变量，这类包含固定内容和动态部分的可重用文件称为模板(template)。

模板引擎的作用就是读取并执行模板中的特殊语法标记，并根据传入的数据将变量替换为实际值，输出最终的HTML页面，这个过程被称为渲染（rendering)。Flask默认使用的模板引擎是Jinja2，它是一个功能齐全的Python模板引擎，除了设置变量，还允许在模板中添加if判断，执行for迭代，调用函数等，以各种方式控制模板的输出。对于Jinja2来说，模板可以是任何格式的纯文本文件，比如HTML、XML、CSV、La TeX等。在这一章，会学习Jinja2模板引擎的基本用法和一些常用技巧。

### Jinja2常见的定界符

#### 语句

for循环、if判断等：

```
{% ...  %}
```

#### 表达式

字符串、变量、函数等：

```
{{ ... }}
```

#### 注释

```
{# ... #}
```

另外，在模板中，Jinja2支持使用“.”获取变量的属性，比如user字典中的username键值通过“.”获取，即user.username，在效果上等同于user['username']。

### 模板语法

利用Jinja2这样的模板引擎，可以将一部分的程序逻辑放到模板中去。简单地说，可以在模板中使用Python语句和表达式来操作数据的输出。但需要注意的是，Jinja2并不支持所有Python语法。而且出于效率和代码组织等方面的考虑，应该适度使用模板，仅把和输出控制有关的逻辑操作放到模板中。

Jinja2允许在模板中使用大部分Python对象，比如字符串、列表、字典、元组、整型、浮点型、布尔值。它支持基本的运算符号(+、-、*、/等）、比较符号（比如==、!=等）、逻辑符号( and、 or、not和括号）以及in、 is、None和布尔值（True、False)。

Jinja2提供了多种控制结构来控制模板的输出，其中for和if是最常用的两种。在Jinja2里，语句使用{% ...%}标识，尤其需要注意的是，在语句结束的地方，必须添加结束标签。

```
{% if user.bio %}
<i>{{ user.bio }}</i>
{% else %}
<i>this user has not provided a bio</i>
{% endif %}
```

for语句迭代：

```
{% for movie in movies %}
<li>{{ movie.name }} - {{ movie.year }}</li>
{% endfor %}
```

常用的for循环变量

![image-20210128091607292](flask入门.assets/image-20210128091607292.png)

### 渲染模板

渲染一个模板，就是执行模板中的代码，并传入所有在模板中使用的变量，渲染后的结果就是要返回给客户端的HTML响应。在视图函数中渲染模板时，并不直接使用Jinja2提供的函数，而是使用Flask提供的渲染函数render_template()。

```
@app.route('/watchlist')
def watchlist():
    return render_template('watchlist.html',user=user,movies=movies)
```

在render_template()函数中，首先传入模板的文件名作为参数。Flask会在程序根目录下的templates文件夹里寻找模板文件，所以这里传入的文件路径是相对于templates根目录的。除了模板文件路径，还以关键字参数的形式传入了模板中使用的变量值，以user为例:左边的user表示传入模板的变量名称，右边的user则是要传入的对象。

除了render_template()函数，Flask还提供了一个render_template_string()函数用来渲染模板字符串。

如果想传入函数在模板中调用，那么需要传入函数对象本身，而不是函数调用(函数的返回值)，所以仅写出函数名称即可。当把函数传入模板后，可以像在Python脚本中一样通过添加括号的方式调用，而且也可以在括号中传入参数。

### 模板辅助工具

1、模板中定义变量，使用set标签

```
{% set navigation = [('/', 'Home'), ('/about', 'About')] %}
```

也可以将一部分模板数据定义为变量，使用set和endset标签声明开始和结束:

```
{% set navigation %}
            <li><a href="/">Home</a>
            <li><a href="/about">About</a>
{% endset %}
```

2、内置上下文，Flask提供了一些内置变量，可以直接使用

![image-20210128105925871](flask入门.assets/image-20210128105925871.png)

如果多个模板都需要使用同一变量，那么比起在多个视图函数中重复传入，更好的方法是能够设置一个模板全局变量。Flask提供了一个app.context_processor装饰器，可以用来注册模板上下文处理函数，它可以完成统一传入变量的工作。模板上下文处理函数需要返回一个包含变量键值对的字典。

```
@app.context_processor
        def inject_foo():
            foo = 'I am foo.'
            return dict(foo=foo)  # 等同于return {'foo': foo}
```

当调用render_template()函数渲染任意一个模板时，所有使用app.context_processor装饰器注册的模板上下文处理函数（包括Flask内置的上下文处理函数）都会被执行，这些函数的返回值会被添加到模板中，因此可以在模板中直接使用foo变量。

3、全局变量

Jinja2默认的全局函数

![image-20210128112544688](flask入门.assets/image-20210128112544688.png)

Flask全局函数

![image-20210128112631351](flask入门.assets/image-20210128112631351.png)

Flask除了把g、session、config、 request对象注册为上下文变量，也将它们设为全局变量，因此可以全局使用。

4、自定义全局函数

除了使用app.context_processor注册模板上下文处理函数来传入函数，也可以使用app. template_global装饰器直接将函数注册为模板全局函数。比如把bar()函数注册为模板全局函数。

```
@app.template_global
def bar():
    return "I am bar"
```

5、过滤器

在Jinja2中，过滤器（(filter）是一些可以用来修改和过滤变量值的特殊函数，过滤器和变量用一个竖线（管道符号）隔开，需要参数的过滤器可以像函数一样使用括号传递。

```
<h3>{{ user.username }}'s movies list({{ movies|length }}):</h3>
```

该方法类似于在python中调用len(movies)。

另一种用法是将过滤器作用于一部分模板数据，使用filter标签和endfilter标签声明开始和结束。下面使用upper过滤器将一段文字转换为大写:

```
{% filter upper %}
            This text becomes uppercase.
{% endfilter %}
```

6、内置过滤器

Jinja2提供的内置过滤器

![image-20210128113405932](flask入门.assets/image-20210128113405932.png)

另外，过滤器可以叠加使用，下面的示例为name变量设置默认值，并将其标题化:

```
<h1>Hello, {{ name|default(’陌生人’)|title }}! </h1>
```

在确保变量值安全的情况下，这通常意味着已经对用户输入的内容进行了“消毒”处理。这时如果想避免转义，将变量作为HTML解析，可以对变量使用safe过滤器:

```
{{ sanitized_text|safe }}
```

另一种将文本标记为安全的方法是在渲染前将变量转换为Markup对象:

7、自定义过滤器

使用app.template_filter()装饰器可以注册自定义过滤器。

```
from flask import Markup
@app.template_filter()
def musical(s):
	return s + Markup(' &#9835; ')

#或
app.add_template_filter(musical)
```

创建的musical过滤器会在被过滤的变量字符后面添加一个音符(single bar note）图标，因为音符通过HTML实体\&#9835;表示，使用Markup类将它标记为安全字符。在使用时和其他过滤器用法相同:

```
{{ name|musical }}
```

8、测试器

在Jinja2中，测试器(Test)是一些用来测试变量或表达式，返回布尔值(True或False）的特殊函数。比如，number测试器用来判断一个变量或表达式是否是数字，使用is连接变量和测试器:

```
{% if age is number %}
	{{ age ＊ 365 }}
{% else %}
无效的数字。
{% endif %}
```

9、常用的内置测试器

![image-20210129150607919](flask入门.assets/image-20210129150607919.png)

在使用测试器时，is的左侧是测试器函数的第一个参数(value) ,其他参数可以添加括号传入，也可以在右侧使用空格连接，以sameas为例:

```
 {% if foo is sameas(bar) %}
 #等同于：
 {% if foo is sameas bar %}
```

10、自定义测试器

```
@app.template_test()
def baz(n):
    if n == 'baz':
        return True
    return False
```

### 模板环境对象

在Jinja2中，渲染行为由jinja2.Enviroment类控制，所有的配置选项、上下文变量、全局函数、过滤器和测试器都存储在Enviroment实例上。当与Flask结合后，并不单独创建Enviroment对象，而是使用Flask创建的Enviroment对象，它存储在appjinja_env属性上。
在程序中，可以使用app.jinja_env更改Jinja2设置。比如，可以自定义所有的定界符。下面使用variable_start_string和variable_end_string分别自定义变量定界符的开始和结束符号:

```
app = Flask(__name__)
app.jinja_env.variable_start_string = '[['
app.jinja_env.variable_end_string = ']]'
```

模板环境中的全局函数、过滤器和测试器分别存储在Enviroment对象的globals、filters和tests属性中，这三个属性都是字典对象。除了使用Flask提供的装饰器和方法注册自定义函数，也可以直接操作这三个字典来添加相应的函数或变量，这通过向对应的字典属性中添加一个键值对实现，传入模板的名称作为键，对应的函数对象或变量作为值。

1、添加自定义全局对象

和app.template_global()装饰器不同，直接操作globals字典允许传入任意Python对象，而不仅仅是函数，类似于上下文处理函数的作用。下面的代码使用app.jinja_env.globals分别向模板中添加全局函数bar和全局变量foo:

```
def bar():
    return "I am bar"
foo="I am foo"

app.jinja_env.globals['bar']=bar
app.jinja_env.globals['foo']=foo
```

全局变量或函数的使用：

```
{{ bar() }} and {{ foo }}
```

2、添加自定义过滤器

```
def musical(s):
    return s +Markup('&#9835;')
app.jinja_env.filters['musical']=musical
```

3、添加自定义测试器

```
def baz(n):
    if n == 'baz':
        return True
    else:
        return  False
app.jinja_env.tests['baz']=baz
```

### 模板结构组织

1、局部模板

web程序中，为每一类页面独立编写一个模板，比如主页模板、用户资料模板。模板可以直接在视图函数中渲染并作为HTML响应主体。除了这类模板，还会用到另一类非独立模板，这类模板通常被称为局部模板或次模板，因为它们仅包含部分代码，所以不会在视图函数中直接渲染它，而是插入到其他独立模板中。

当程序中的某个视图用来处理AJAX请求时，返回的数据不需要包含完整的HTML结构，这时就可以返回渲染后的局部模板。

当多个独立模板中都会使用同一块HTML代码时，可以把这部分代码抽离出来，存储到局部模板中。这样一方面可以避免重复，另一方面也可以方便统一管理。比如，多个页面中都要在页面顶部显示一个提示条，这个横幅可以定义在局部模板\_banner.html中。

使用include标签来插入一个局部模板，这会把局部模板的全部内容插在使用include标签的位置。比如，在其他模板中，可以在任意位置使用下面的代码插入_banner.html的内容:

```
{% include '_banner.html' %}
```

**为了和普通模板区分开，局部模板的命名通常以一个下划线开始。**

2、宏

宏(macro)是Jinja2提供的一个非常有用的特性，它类似Python中的函数。使用宏可以把一部分模板代码封装到宏里，使用传递的参数来构建内容，最后返回构建后的内容。在功能上，它和局部模板类似，都是为了方便代码块的重用。
为了便于管理，可以把宏存储在单独的文件中，这个文件通常命名为macros.html或_macors.html。在创建宏时，使用macro和endmacro标签声明宏的开始和结束。在开始标签中定义宏的名称和接收的参数。

```
{% macro qux(amount=1) %}
            {% if amount == 1 %}
                I am qux.
            {% elif amount > 1 %}
                We are quxs.
            {% endif %}
        {% endmacro %}
```

使用时，需要像从Python模块中导入函数一样使用import语句导入它，然后作为函数调用，传入必要的参数。

```
{% from 'macros.html' import qux %}
          .
        {{ qux(amount=5) }}
```

另外，在使用宏时需要注意上下文问题。在Jinja2中，出于性能的考虑，并且为了让这一切保持显式，默认情况下包含（include)一个局部模板会传递当前上下文到局部模板中，但导入(import)却不会。具体来说，当使用render_template()函数渲染一个foo.html模板时，这个foo.html的模板上下文中包含下列对象:

```
1、Flask使用内置的模板上下文处理函数提供的g、session.config、request。
2、扩展使用内置的模板上下文处理函数提供的变量。
3、自定义模板上下文处理器传入的变量。
4、使用render_template()函数传入的变量。
5、Jinja2和Flask内置及自定义全局对象。
6、Jinja2内置及自定义过滤器。
7、Jinja2内置及自定义测试器。
```

使用include标签插入的局部模板(比如_banner.html)同样可以使用上述上下文中的变量和函数。而导入另一个并非被直接渲染的模板（比如macros.html)时，这个模板仅包含下列这些对象:

```
1、Jinja2和Flask内置的全局函数和自定义全局函数。
2、Jinja2内置及自定义过滤器。
3、Jinja2内置及自定义测试器。
```

### 模板继承

1、基模板

基模板存储了程序页面的固定部分，通常被命名为base.html或layout.html。示例程序中的基模板base.html中包含了一个基本的HTML结构，还添加了一个简单的导航条和页脚：

```
<!DOCTYPE html>
<html>
<head>
    {% block head %}
        <meta charset="utf-8">
        <title>{% block title %}Template - HelloFlask{% endblock %}</title>
        <link rel="icon" type="image/x-icon" href="{{ url_for('static', filename='favicon.ico') }}">
        {% block styles %}
            <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css' ) }}">
        {% endblock %}
    {% endblock %}
</head>
<body>
<nav>
    <ul><li><a href="{{ url_for('index') }}">Home</a></li></ul>
</nav>

<main>
    {% for message in get_flashed_messages() %}
        <div class="alert">{{ message }}</div>
    {% endfor %}
    {% block content %}{% endblock %}
</main>
<footer>
    {% block footer %}
        <small> &copy; 2021 <a href="http://www.baidu.com" title="Written by chyuhung">chyuhung</a> /
            <a href="https://github.com/chyuhung/helloflask" title="Fork me on GitHub">GitHub</a> /
            <a href="http://helloflask.com" title="A HelloFlask project">HelloFlask</a>
        </small>
    {% endblock %}
</footer>
{% block scripts %}{% endblock %}
</body>
</html>

```

当子模板继承基模板后，子模板会自动包含基模板的内容和结构。为了能够让子模板方便地覆盖或插入内容到基模板中，需要在基模板中定义块(block)，在子模板中可以通过定义同名的块来执行继承操作。

块的开始和结束分别使用block和endblock标签声明，而且块之间可以嵌套。在这个基模板中，创建了六个块: head、 title、styles、content、footer和scripts，分别用来划分不同的代码。其中,head块表示<head>标签的内容，title表示<title>标签的内容，content块表示页面主体内容，footer表示页脚部分，styles块和scripts块，则分别用来包含CSS文件和Java Script文件引用链接或页内的CSS和Java Script代码。

块名称可以随意指定，而且并不是必须的。可以按照需要设置块，如果只需要让子模板添加主体内容，那么仅定义一个content块就足够了。
以content块为例，模板继承示意图如图3-2所示。

![image-20210129163338166](flask入门.assets/image-20210129163338166.png)

为了避免块的混乱，块的结束标签可以指明块名，同时要确保前后名称一致。比如:

```
{% block body %}
          .
{% endblock body %}
```

2、编写子模板

因为基模板中定义了HTML的基本结构，而且包含了页脚等固定信息，在子模板中不再需要定义这些内容，只需要对特定的块进行修改。这时可以修改前面创建的电影清单模板watchlist.html和主页模板index.html，将这些子模板的通用部分合并到基模板中，并在子模板中定义块来组织内容，以便在渲染时将块中的内容插入到基模板的对应位置。以index.html为例:

**使用extends标签声明扩展基模板，它告诉模板引擎当前模板派生自base.html。extends必须是子模板的第一个标签。**

```
{% extends 'base.html' %}
{% from 'macros.html' import qux %}

{% block content %}
{% set name='baz' %}
<h1>Template</h1>
<ul>
    <li><a href="{{ url_for('watchlist') }}">Watchlist</a></li>
    <li>Filter: {{ foo|musical }}</li>
    <li>Global: {{ bar() }}</li>
    <li>Test: {% if name is baz %}I am baz.{% endif %}</li>
    <li>Macro: {{ qux(amount=1) }}</li>
</ul>
{% endblock %}
```

### 子模板对父模板的操作

1、覆盖内容

当在子模板里创建同名的块时，会使用子块的内容覆盖父块的内容。比如在子模板index.html中定义了title块，内容为Home，这会把块中的内容填充到基模板里的title块的位置，最终渲染为<title>Home</title>, content块的效果同理。

2、追加内容

如果想要向基模板中的块追加内容，需要使用Jinja2提供的super()函数进行声明，这会向父块添加内容。比如，下面的示例向基模板中的styles块追加了一行\<style\>样式定义:

```
 {% block styles %}
 {{ super() }}
 <style>
     .foo {
     	color: red;
     }
 </style>
 {% endblock %}
```

当子模板被渲染时，它会继承基模板的所有内容，然后根据定义的块进行覆盖或追加操作。

3、空白控制

在实际输出的HTML文件中，模板中的Jinja2语句、表达式和注释会保留移除后的空行，如果想在渲染时自动去掉这些空行，可以在定界符内侧添加减号。比如，{%-endfor %}会移除该语句前的空白，同理，在右边的定界符内侧添加减号将移除该语句后的空白:

```
{% if user.bio -%}
	<i>{{ user.bio }}</i>
{% else -%}
	<i>This user has not provided a bio.</i>
{%- endif %}
```

除了在模板中使用减号来控制空白外，也可以使用模板环境对象提供的trim_blocks和lstrip_blocks属性设置，前者用来删除Jinja2语句后的第一个空行，后者则用来删除Jinja2语句所在行之前的空格和制表符(tabs) :

```
app.jinja_env.trim_blocks = True
app.jinja_env.lstrip_blocks = True
```

4、加载静态文件

一个Web项目不仅需要HTML模板，还需要许多静态文件，比如CSS、Java Script文件、图片以及音频等。在Flask程序中，默认需要将静态文件存储在与主脚本（包含程序实例的脚本)同级目录的static文件夹中。
为了在HTML文件中引用静态文件，需要使用url_for()函数获取静态文件的URL。Flask内置了用于获取静态文件的视图函数，端点值为static，它的默认URL规则为/static/\<path:filename\>,URL变量filename是相对于static文件夹根目录的文件路径。

在示例程序的static目录下保存了一个头像图片avatar.jpg，可以通过url_for('static', filename='avatar.jpg')获取这个文件的URL，这个函数调用生成的URL为/static/avatar.jpg，在浏览器中输入http://localhost:5000/static/avatar.jpg即可访问图片
。在模板watchlist2.html里，在用户名的左侧添加了这个图片，使用url_for()函数生成图片src属性所需的图片URL，如下所示:

```
<img src=" {{ url_for('static',filename='avatar.jpg') }}",width="50">
```

另外，还创建了一个存储CSS规则的styles.css文件，使用下面的方式在模板中加载这个文件：

```
<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='style.css') }}">
```

5、添加Favicon

favicon.ico文件指的是Favicon ( favorite icon，收藏夹头像/网站头像)，又称为shortcut icon、tab icon、website icon或是bookmark icon。
顾名思义，这是一个在浏览器标签页、地址栏和书签收藏夹等处显示的小图标，作为网站的特殊标记。浏览器在发起请求时，会自动向根目录请求这个文件，在前面的示例程序中，没有提供这个文件，所以才会产生404记录。

要想为Web项目添加Favicon，要先有一个Favicon文件，并放置到static目录下。它通常是一个宽高相同的ICO格式文件，命名为favicon.ico。

Flask中静态文件的默认路径为/static/filename，为了正确返回Favicon，可以显式地在HTML页面中声明Favicon的路径。首先可以在\<head\>部分添加一个\<link\>元素，然后将rel属性设置为icon，如下所示：

```
<link rel="icon" type="image/x-icon" href="{{ url_for('static',filename='favicon.ico') }}">
```

### CSS框架

在编写Web程序时，手动编写CSS比较麻烦，更常见的做法是使用CSS框架来为程序添加样式。CSS框架内置了大量可以直接使用的CSS样式类和Java Script函数，使用它们可以非常快速地让程序页面变得美观和易用，同时也可以定义自己的CSS文件来进行补充和调整。以Bootstrap ( http://getbootstrap.com/)）为例，需要访问Bootstrap的下载页面( http://getbootstrap.com/docs/4.0/getting-started/download/)下载相应的资源文件，然后分类别放到static目录下。

Bootstrap是最流行的开源前端框架之一，它有浏览器支持广泛、响应式设计等特点。使用它可以快速搭建美观、现代的网页。Bootstrap的官方文档(http://getbootstrap.com/docs/）提供了很多简单易懂的示例代码。
通常情况下，CSS和JavaScript的资源引用会在基模板中定义，具体方式和加载自定义的styles.css文件相同：

```
       ...
        {% block styles %}
            <link rel="stylesheet" href="{{ url_for('static', filename='css/bootstrap.min.css') }}">
        {% endblock %}
          .
        {% block scripts %}
            <script src="{{ url_for('static', filename='js/jquery.min.js') }}"></script>
            <script src="{{ url_for('static', filename='js/popper.min.js') }}"></script>
            <script src="{{ url_for('static', filename='js/bootstrap.min.js') }}"></script>
        {% endblock %}
          ...
```

如果不使用Bootstrap提供的Java Script功能，那么也可以不加载。另外，Bootstrap所―依赖的j Query (https://jquery.com/)和Popper.js(https://popper.js.org/）需要单独下载，这三个Java Script文件在引入时要按照j Query→Popper.js→Boostrap的顺序引入。

虽然建议在开发时统一管理静态资源，如果想简化开发过程，那么从CDN加载是更方便的做法。从CND加载时，只需要将相应的URL替换为CDN提供的资源URL，比如：

```
		...
        {% block styles %}
            <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/
                css/bootstrap.min.css">
        {% endblock %}
          ...
        {% block scripts %}
            <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js"></script>
            <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/
                popper.min.js"></script>
            <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
        {% endblock %}
          ...
```

### 使用宏加载静态资源

为了方便加载静态资源，可以创建一个专门用于加载静态资源的宏，如：

```
{% macro static_file(type, filename_or_url, local=True) %}
            {% if local %}
                {% set filename_or_url = url_for('static', filename=filename_or_url) %}
            {% endif %}
            {% if type == 'css' %}
                <link rel="stylesheet" href="{{ filename_or_url }}" type="text/css">
            {% elif type == 'js' %}
                <script type="text/javascript" src="{{ filename_or_url }}"></script>
            {% elif type == 'icon' %}
                <link rel="icon" href="{{ filename_or_url }}">
            {% endif %}
        {% endmacro %}
```

在模板中导入宏后，只需在调用时传入静态资源的类别和文件路径就会获得完整的资源加载语句。使用它加载CSS文件的示例如下：

```
static_file('css', 'css/bootstrap.min.css')
```

使用它也可以从CDN加载资源，只需要将关键字参数local设为False，然后传入资源的URL即可：

```
static_file('css', 'https://maxcdn.../css/bootstrap.min.css', local=False)
```

1、消息闪现

Flask提供了一个非常有用的flash()函数，它可以用来“闪现”需要显示给用户的消息，比如当用户登录成功后显示“欢迎回来!”。在视图函数调用flash()函数，传入消息内容即可“闪现”一条消息。当然，它并不是想象的，能够立刻在用户的浏览器弹出一条消息。实际上，使用功能flash()函数发送的消息会存储在session中，需要在模板中使用全局函数get_flashed_messages()获取消息并将其显示出来。

可以在任意视图函数中调用flash()函数发送消息。为了测试消息闪现，添加了一个just_flash视图，在函数中发送了一条消息，最后重定向到index视图，如：

```
@app.route('/flash')
def just_flash():
    flash("welcome!")
    return redirect(url_for('watchlist'))
```

Flask提供了get_ flashed_message()函数用来在模板里获取消息，因为程序的每一个页面都有可能需要显示消息，把获取并显示消息的代码放在基模板中content块的上面，这样就可以在页面主体内容的上面显示消息。

```
<main>
    {% for message in get_flashed_messages() %}
        <div class="alert">{{ message }}</div>
    {% endfor %}
    {% block content %}{% endblock %}
</main>
```

当get_flashed_message()函数被调用时，session中存储的所有消息都会被移除。如果这时刷新页面，会发现重载后的页面不再出现这条消息。

Jinja2内部使用Unicode，所以需要向模板传递Unicode对象或只包含ASCII字符的字符串。在Python 2.x中，如果字符串包含中文（或任何非ASCII字符），那么需要在字符串前添加u前缀（如：u'大家好，这是测试消息），这会告诉Python把这个字符串编码成Unicode字符串，另外还需要在Python文件的首行添加编码声明，这会让Python使用UTF-8来解码字符串，后面不再提示。

Flask、Jinja2和Werkzeug等相关依赖均将文本的类型设为Unicode，所以在编写程序和它们交互时应该遵循同样的约定。比如，在Python脚本中添加编码声明;在Python2中为非ASCII字符添加u前缀;将编辑器的默认编码设为UTF-8;在HTML文件的head标签中添加编码声明，即\<meta charset="utf-8"\>;当需要读取文件传入模板时，手动使用decode()函数解码。

2、自定义错误页面

当程序返回错误响应时，会渲染一个默认的错误页面，默认的错误页面太简单了，而且和其他页面的风格不符，导致用户看到这样的页面时往往会不知所措。可以注册错误处理函数来自定义错误页面。
错误处理函数和视图函数很相似，返回值将会作为响应的主体，因此首先要创建错误页面的模板文件。为了和普通模板区分开来，在模板文件夹templates里为错误页面创建了一个errors子文件夹，并在其中为最常见的404和500错误创建了模板文件。

```
{% extends 'base.html' %}
{% block title %}404 - Page Not Found{% endblock %}
{% block content %}
<h1>Page Not Found</h1>
<p>You are lost...</p>
{% endblock %}
```

错误处理函数需要附加app.errorhandler()装饰器，并传入错误状态码作为参数。错误处理函数本身则需要接收异常类作为参数，并在返回值中注明对应的HTTP状态码。当发生错误时，对应的错误处理函数会被调用，它的返回值会作为错误响应的主体。

```
@app.errorhandler(500)
def internal_server_error(e):
    return render_template('errors/500.html'), 500
@app.errorhandler(404)
def page_not_found(e):
    return render_template('errors/404.html'), 404
```

![image-20210208144333361](flask入门.assets/image-20210208144333361.png)

错误处理函数接收异常对象作为参数，内置的异常对象提供了下列常用属性：

![image-20210208144544159](flask入门.assets/image-20210208144544159.png)

### Java Script和CSS中的Jinja2

当程序逐渐变大时，很多时候会需要在Java Script和CSS代码中使用Jinja2提供的变量值，甚至是控制语句。比如，通过传入模板的theme_color变量来为页面设置主题色彩，或是根据用户是否登录来决定是否执行某个Java Script函数。
首先要明白的是，只有使用render_template()传入的模板文件才会被渲染，如果把Jinja2代码写在单独的Java Script或是CSS文件中，尽管在HTML中引入了它们，但它们包含的Jinja2代码永远也不会被执行。

1、行内/嵌入式Java Script/CSS

如果要在Java Script和CSS文件中使用Jinja2代码，那么就在HTML中使用\<style\>和\<script\>标签定义这部分CSS和Java Script代码。
在这部分CSS和Java Script代码中加入Jinja2时，不用考虑编写时的语法错误，比如引号错误，因为Jinja2会在渲染后被替换掉，所以只需要确保渲染后的代码正确即可。
不过并不推荐使用这种方式，尤其是行内Java Script/CSS会让维护变得困难。避免把大量Java Script代码留在HTML中的办法就是尽量将要使用的Jinja2变量值在HTML模板中定义为Java Script变量。

2、定义为Java Script/CSS变量

对于想要在Java Script中获取的数据，如果是元素特定的数据，比如某个文章条目对应的id值，可以通过HTML元素的data-*属性存储。可以自定义横线后的名称，作为元素上的自定义数据变量，比如data-id, data-username等，比如：

```
<span  data-id="{{  user.id  }}"  data-username="{{  user.username  }}">{{  user.username }}</span>
```

在Java Script中，可以使用DOM元素的dataset属性获取data-*属性值，比如element. dataset.username，或是使用get Attribute()方法，比如element.getAttribute('data-username');使用j Query时，可以直接对j Query对象调用data方法获取，比如$element.data('username')。

在HTML中,“data-*”被称为自定义数据属性(custom data attribute)，可以用它来存储自定义的数据供Java Script获取。
对于需要全局使用的数据，则可以在页面中使用嵌入式Java Script定义变量，如果没法定义为Java Script变量，那就考虑定义为函数。

```
<script type="text/javascript">
	var foo = '{{ foo_variable }}';
</script>
```

CSS同理，有些时候会需要将Jinja2变量值传入CSS文件，比如希望将用户设置的主题颜色设置到对应的CSS规则中，或是需要将static目录下某个图片的URL传入CSS来设置为背景图片，除了将这部分CSS定义直接写到HTML中外，可以将这些值定义为CSS变量。

```
<style>
:root {
	--theme-color: {{ theme_color }};
	--background-url: {{ url_for('static', filename='background.jpg') }}
}
</style>
```

在CSS文件中，使用var()函数并传入变量名即可获取对应的变量值：

```
#foo {
	color: var(--theme-color);
}
#bar {
	background: var(--background-url);
}
```

## 表单

在Web程序中，表单是和用户交互最常见的方式之一。用户注册、登录、撰写文章、编辑设置，无一不用到表单。不过，表单的处理却并不简单。不仅要创建表单，验证用户输入的内容，向用户显示错误提示，还要获取并保存数据。幸运的是，强大的WTForms可以解决这些问题。WTForms是一个使用Python编写的表单库，它使得表单的定义、验证（服务器端）和处理变得非常轻松。

### html表单

在HTML中，表单通过\<form\>标签创建，表单中的字段使用\<input\>标签定义。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>This is a test</title>
</head>
<body>
<form method="post">
<label for="username">Username</label><br>
    <input type="text" name="username" placeholder="Your username"><br>
    <label for="password">Password</label><br>
    <input type="password" name="password" placeholder="Your password"><br>
    <input id="remember" name="remember" type="checkbox" checked>
    <label for="remember"><small>Remember me</small></label><br>
    <input type="submit" name="submit" value="Login">
</form>
</body>
</html>
```

```
@app.route("/test",methods=['GET','POST'])
def test():
    return render_template("test.html")
```

运行效果：

![image-20210220102702302](flask入门.assets/image-20210220102702302.png)

WTForms支持在Python中使用大考的更易于重用。因此，除非是非常简单HTML代码，这种方式更加方便，而且使表单更易于重用。因此，除非是非常简单的程序，或者是想让表单的定义更加灵活，否则一般不会在模板中直接使用HTML编写表单。

### 使用Flask-WTF处理表单

扩展Flask-WTF集成了WTForms，使用它可以在Flask中更方便地使用WTForms。Flask-WTF将表单数据解析、CSRF保护、文件上传等功能与Flask集成，另外还附加了re CAPTCHA支持。

Flask-WTF默认为每个表单启用CSRF保护，它会为自动生成和验证CSRF令牌。默认情况下，Flask-WTF使用程序密钥来对CSRF令牌进行签名，所以需要为程序设置密钥:

```
 app.secret_key = 'secret string'
```

### 定义WTForms表单类

当使用WTForms创建表单时，表单由Python类表示，这个类继承从WTForms导入的Form基类。一个表单由若干个输入字段组成，这些字段分别用表单类的类属性来表示(字段即Field，可以简单理解为表单内的输入框、按钮等部件)。下面定义了一个Login Form类，最终会生成在前面定义的HTML表单:

```
from wtforms import Form, StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import DataRequired, Length
class LoginForm(Form):
	username = StringField('Username', validators=[DataRequired()])
	password = PasswordField('Password', validators=[DataRequired(), Length(8, 128)])
	remember = BooleanField('Remember me')
	submit = SubmitField('Log in')
```

*每个字段属性通过实例化WTForms提供的字段类表示。字段属性的名称将作为对应HTML\<input\>元素的name属性及id属性值。*

*字段属性名称大小写敏感，不能以下划线或validate开头。*
这里的Login Form表单类中定义了四个字段:文本字段String Field、密码字段Password-Field、勾选框字段Boolean Field和提交按钮字段Submit Field。字段类从wtforms包导入，常用的WTForms字段如:

![image-20210220112429284](flask入门.assets/image-20210220112429284.png)

*有些字段最终生成的HTML代码相同，不过WTForms会在表单提交后根据表单类中字段的类型对数据进行处理，转换成对应的Python类型，以便在Python脚本中对数据进行处理。*
通过实例化字段类时传入的参数，可以对字段进行设置，字段类构造方法接收的常用参数如:

![image-20210220112542018](flask入门.assets/image-20210220112542018.png)

在WTForms中，验证器(validator）是一系列用于验证字段数据的类，在实例化字段类时使用validators关键字来指定附加的验证器列表。验证器从wtforms.validators模块中导入，常用的验证器如:

![image-20210220141504545](flask入门.assets/image-20210220141504545.png)

在实例化验证类时，message参数用来传入自定义错误消息，如果没有设置则使用内置的英文错误消息，后面了解如何使用内置的中文错误消息。

*validators参数接收一个传入可调用对象组成的列表。内置的验证器通过实现了\_call_()方法的类表示，所以需要在验证器后添加括号。*

在name和password字段里，都使用了Data Required验证器，用来验证输入的数据是否有效。另外，password字段里还添加了一个Length验证器，用来验证输入的数据长度是否在给定的范围内。验证器的第一个参数一般为错误提示消息，可以使用message关键字传递参数，通过传入自定义错误信息来覆盖内置消息，比如:

```
name = String Field('Your Name', validators=[Data Required(message=u'名字不能为空！')])
```

当使用Flask-WTF定义表单时，仍然使用WTForms提供的字段类和验证器，创建的方式也完全相同，只不过表单类要继承Flask-WTF提供的Flask Form类。Flask Form类继承自Form类，进行了一些设置，并附加了一些辅助方法，以便与Flask集成。因为本章的示例程序中包含多个表单类，为了便于组织，创建了一个forms.py脚本，用来存储所有的表单类。

*配置键WTF_CSRF_ENABLED用来设置是否开启CSRF保护，默认为True。Flask-WTF会自动在实例化表单类时添加一个包含CSRF令牌值的隐藏字段，字段名为csrf_token。*

### 输出html代码

以使用WTForms创建的Login Form为例，实例化表单类，然后将实例属性转换成字符串或直接调用就可以获取表单字段对应的HTML代码:

```
>>> form = LoginForm()
>>> form.username()
	u'<input id="username" name="username" type="text" value="">'
>>> form.submit()
	u'<input id="submit" name="submit" type="submit" value="Submit">'
```

字段的\<label>元素的HTML代码则可以通过“form.字段名.label”的形式获取:

```
 >>> form.username.label()
	u'<label for="username">Username</label>'
>>> form.submit.label()
	u'<label for="submit">Submit</label>'
```

在创建HTML表单时，经常会需要使用HTML\<input>元素的其他属性来对字段进行设置。比如，添加class属性设置对应的CSS类为字段添加样式;添加placeholder属性设置占位文本。默认情况下，WTForms输出的字段HTML代码只会包含id和name属性，属性值均为表单类中对应的字段属性名称。如果要添加额外的属性，通常有两种方法。

### 使用render_kw属性

```
username = StringField('Username', render_kw={'placeholder': 'Your Username'})
```

这个字段被调用后输出的HTML代码如下所示:

```
<input type="text" id="username" name="username" placeholder="Your Username">
```

### 在调用字段时传入

在调用字段属性时，通过添加括号使用关键字参数的形式也可以传入字段额外的HTML属性:

```
>>> form.username(style='width: 200px; ', class_='bar')
	u'<input class="bar" id="username" name="username" style="width: 200px; " type="text">'
```

class是 Python的保留关键字，在这里使用class_来代替class，渲染后的\<input\>会获得正确的class属性，在模板中调用时则可以直接使用class。
*通过上面的方法也可以修改id和name属性，但表单被提交后，WTForms需要通过name属性来获取对应的数据，所以不能修改name属性值。*

### 在模板中渲染表单

为了能够在模板中渲染表单，需要把表单类实例传入模板。首先在视图函数里实例化表单类Login Form，然后在render_template()函数中使用关键字参数form将表单实例传入模板:

```
class TestForm(FlaskForm):
    username=StringField('username',validators=[DataRequired()])
    password=PasswordField('password',validators=[DataRequired(),Length(8,128)])
    remember = BooleanField('remember?')
    submit=SubmitField('Log in')
```

```
from forms import TestForm
@app.route("/test01",methods=['GET','POST'])
def test01():
    form=TestForm()
    return render_template("test01.html",form=form)
```

在模板中，只需要调用表单类的属性即可获取字段对应的HTML代码
，如果需要传入参数，也可以添加括号：

```
<form method="post">
    {{ form.csrf_token }} <! -- 渲染CSRF令牌隐藏字段 -->
    {{ form.username.label }}{{ form.username }}<br>
    {{ form.password.label }}{{ form.password }}<br>
    {{ form.remember }}{{ form.remember.label }}<br>
    {{ form.submit }}<br>
</form>
```

需要注意的是，在上面的代码中，除了渲染各个字段的标签和字段本身，还调用了form.csrf_token属性渲染Flask-WTF为表单类自动创建的CSRF令牌字段。form.csrf_token字段包含了自动生成的CSRF令牌值，在提交表单后会自动被验证，为了确保表单通过验证，必须在表单中手动渲染这个字段。

![image-20210222115805022](flask入门.assets/image-20210222115805022.png)

Flask-WTF为表单类实例提供了一个form.hidden_tag()方法，这个方法会依次渲染表单中所有的隐藏字段。因为csrf_token字段也是隐藏字段，所以当这个方法被调用时也会渲染csrf_token字段。

使用render_kw字典或是在调用字段时传入参数来定义字段的额外HTML属性，通过这种方式添加CSS类，可以编写一个Bootstrap风格的表单:

```
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">


{% block styles %}
    <link rel="stylesheet" href="{{ url_for('static',filename='css/bootstrap.min.css') }}">
{% endblock %}

{% block slot %}
<div class="container">
    <br>
    <a href="{{ url_for("index") }}">Return</a>
    <br>
    <h1 class="display-4">This Is Test01</h1>
    <hr>
    <form method="post">
    {{ form.csrf_token }} <! -- 渲染CSRF令牌隐藏字段 -->
        <div class="form-group">
            {{ form.username.label }}<br>{{ form.username(class='form-control') }}<br>
            <div class="form-group">
                {{ form.password.label }}<br>{{ form.password(class='form-control') }}<br>
            </div>
            <div class="form-check">
                {{ form.remember(class='form-check-input') }}{{ form.remember.label }}<br>
            </div>
            {{ form.submit(class='btn btn-primary') }}<br>
        </div>
{% endblock %}
</form>
</div>
```

![image-20210223102509723](flask入门.assets/image-20210223102509723.png)

如果想手动编写HTML表单的代码，要注意表单字段的name属性值必须和表单类的字段名称相同，这样在提交表单时WTForms才能正确地获取数据并进行验证。

### 处理表单数据

表单数据的处理涉及很多内容，除去表单提交不说，从获取数据到保存数据大致会经历以下步骤:
1)解析请求，获取表单数据。
2)对数据进行必要的转换，比如将勾选框的值转换成Python的布尔值。
3)验证数据是否符合要求，同时验证CSRF令牌。
4)如果验证未通过则需要生成错误消息，并在模板中显示错误消息。
5)如果通过验证，就把数据保存到数据库或做进一步处理。
除非是简单的程序，否则手动处理不太现实，使用Flask-WTF和WTForms可以极大地简化这些步骤。

### 提交表单

在HTML中，当\<form\>标签声明的表单中类型为submit的提交字段被单击时，就会创建一个提交表单的HTTP请求，请求中包含表单各个字段的数据。表单的提交行为主要由三个属性控制：

![image-20210223103635348](flask入门.assets/image-20210223103635348.png)

form标签的action属性用来指定表单被提交的目标URL，默认为当前URL，也就是渲染该模板的路由所在的URL。如果要把表单数据发送到其他URL，可以自定义这个属性值。
当使用GET方法提交表单数据时，表单的数据会以查询字符串的形式附加在请求的URL里，比如:

```
http://localhost:5000/basic?username=cyh&password=12345
```

GET方式仅适用于长度不超过3000个字符，且不包含敏感信息的表单。因为这种方式会直接将用户提交的表单数据暴露在URL中，容易被攻击者截获，示例中的情况明显是危险的。因此，出于安全的考虑，一般使用POST方法提交表单。使用POST方法时，按照默认的编码类型，表单数据会被存储在请求主体中，比如:

```
POST /basic HTTP/1.0
...
Content-Type: application/x-www-form-urlencoded
Content-Length: 30

username=cyh&password=12345
```

Flask为路由设置默认监听的HTTP方法为GET。为了支持接收表单提交发送的POST请求，必须在app.route()装饰器里使用methods关键字为路由指定HTTP方法:

```
@app.route("/test01",methods=['GET','POST'])
def test01():
    form=TestForm()
    return render_template("test01.html",form=form)
```

### 验证表单数据

#### 客户端验证和服务端验证

1、客户端验证

客户端验证(client side validation）是指在客户端（比如Web浏览器）对用户的
输入值进行验证。比如，使用HTML5内置的验证属性即可实现基本的客尸端验让(type、required、min、max、accept等)。比如，下面的username字段添加了required标志:

```
username=StringField('用户名',validators=[DataRequired()])
```

```
<input class="form-control" id="username" name="username" required="" type="text" value="">
```

和其他附加HTML属性相同，可以在定义表单时通过render_kw传入这些属性，或是在渲染表单时传入。像required这类布尔值属性，值可以为空或是任意ASCII字符，比如:

```
{{ form.username(required='') }}
```

除了使用HTML5提供的属性实现基本的客户端验证，通常会使用Java Script实现完善的验证机制。如果不想手动编写Java Script代码实现客户端验证，可以考虑使用各种Java Script表单验证库，比如j Query Validation Plugin(https://jqueryvalidation.org/) . Parsley. js ( http://parsleyjs.org/)以及可与Bootstrap集成的Bootstrap Validator ( http://1000hz.github.io/bootstrap-validator/，目前仅支持Bootstrap3版本)等。

客户端方式可以实时动态提示用户输入是否正确，只有用户输入正确后才会将表单数据发送到服务器。客户端验证可以增强用户体验，降低服务器负载。

2、服务端验证

服务器端验证(server side validation）是指用户把输入的数据提交到服务器端，在服务器端对数据进行验证。如果验证出错，就在返回的响应中加入错误信息。用户修改后再次提交表单，直到通过验证。在Flask程序中使用WTForms实现的就是服务器端验证。
在这里不必纠结使用何种形式，因为无论是否使用客户端验证，服务器端验证都是必不可少的，因为用户可以通过各种方式绕过客户端验证，比如在客户端设置禁用Java Script。对于玩具程序来说，可以不用考虑那么多，但对于真实项目来说，绝对不能忽视任何安全问题。

### WTForms验证机制

WTForms验证表单字段的方式是在实例化表单类时传入表单数据，然后对表单实例调用validate()方法。这会逐个对字段调用字段实例化时定义的验证器，返回表示验证结果的布尔值。如果验证失败，就把错误消息存储到表单实例的errors属性对应的字典中。

因为表单使用POST方法提交，如果单纯使用WTForms，在实例化表单类时需要首先把request.form传入表单类，而使用Flask-WTF时，表单类继承的Flask Form基类默认会从request.form获取表单数据，所以不需要手动传入。

使用POST方法提交的表单，其数据会被Flask解析为一个字典，可以通过请求对象的form属性获取(request.form);使用GET方法提交的表单的数据同样会被解析为字典，不过要通过请求对象的args属性获取(request.args)。

### 在视图函数中验证表单

因为现在的basic_form视图同时接收两种类型的请求:GET请求和POST请求。所以要根据请求方法的不同执行不同的代码。具体来说:首先是实例化表单，如果是GET请求，那么就渲染模板;如果是POST请求，就调用validate()方法验证表单数据。
请求的HTTP方法可以通过request.method属性获取，可以使用下面的方式来组织视图函数:

```
@app.route("/test01",methods=['GET','POST'])
def test01():
    form=TestForm()
    if request.method=='POST' and form.validate():
        username=request.form.get('username')
        flash('welcome home,%s!'%username)
        return redirect(url_for('index'))
    return render_template("test01.html",form=form)
```

当请求方法是GET时，会跳过这个if语句，渲染basic.html模板;当请求的方法是POST时(说明用户提交了表单），则验证表单数据。这会逐个字段(包括CSRF令牌字段)调用附加的验证器进行验证。

因为WTForms会自动对CSRF令牌字段进行验证，如果没有渲染该字段会导致验证出错，错误消息为“CSRF token is missing"。Flask-WTF提供的validate_on_submit()方法合并了这两个操作，因此代码可以简化为:

```
if form.validate_on_submit():
```

除了POST方法，如果请求的方法是PUT、PATCH和 DELETE方法，form.validate_on_submit()也会验证表单数据。如果form.validate_on_submit()返回True，则表示用户提交了表单，且表单通过验证，那么就可以在这个if语句内获取表单数据:

```
@app.route("/test01",methods=['GET','POST'])
def test01():
    form=TestForm()
    if form.validate_on_submit():
        username=form.username.data
        flash('welcome home,%s!'%username)
        return redirect(url_for('index'))
    return render_template("test01.html",form=form)
```

表单类的data属性是一个匹配所有字段与对应数据的字典，一般直接通过"form.字段属性名.data”的形式来获取对应字段的数据。例如，form.username.data返回username字段的值。如上，当表单验证成功后，获取了username字段的数据，然后用来发送一条flash消息，最后将程序重定向到index视图。

在这个if语句内，如果不使用重定向的话，当if语句执行完毕后会继续执行最后的render_template()函数渲染模板，最后像往常一样返回一个常规的200响应，但这会造成一个问题:
在浏览器中，当单击F5刷新/重载时的默认行为是发送上一个请求。如果上一个请求是POST请求，那么就会弹出一个确认窗口，询问用户是否再次提交表单。*为了避免出现这个容易让人产生困惑的提示，尽量不要让提交表单的POST请求作为最后一个请求。这就是为什么在处理表单后返回一个重定向响应，这会让浏览器重新发送一个新的GET请求到重定向的目标URL。最终，最后一个请求就变成了GET请求。这种用来防止重复提交表单的技术称为PRG ( Post/Redirect/Get)模式，即通过对提交表单的POST请求返回重定向响应将最后一个请求转换为GET请求。*

### 在模板中渲染错误消息

如果form.validate_on_submit()返回False，那么说明验证没有通过。对于验证未通过的字段，WTForms会把错误消息添加到表单类的errors属性中，这是一个匹配作为表单字段的类属性到对应的错误消息列表的字典。一般会直接通过字段名来获取对应字段的错误消息列表，即“form.字段名.errors”。比如，form.name.errors返回name字段的错误消息列表。
像渲染flash()消息一样，可以在模板里使用for循环迭代错误消息列表：

```
{% for message in form.password.errors %}
<small class="error">{{ message }}</small>
{% endfor %}
```

在使用Data Required和Input Required验证器时，WTForms会在字段输出的HTML代码中添加required属性，所以会弹出浏览器内置的错误提示。同时，WTForms也会在表单字段的flags属性添加required标志(比如form.username.flags.required)，所以可以在模板中通过这个标志值来判断是否在字段文本中添加一个*号或文字标注，以表示必填项。
如果在用户名字段输入空格，在密码字段输入的数值长度小于6，返回响应后会看到对应的错误消息显示在字段下方：

![image-20210223154009980](flask入门.assets/image-20210223154009980.png)

lnput Required验证器和Data Required很相似，但Input Required仅验证用户是否有输入，而不管输入的值是否有效。例如，由空格组成的数据也会通过验证。当使用Data Required时，如果用户输入的数据不符合字段要求，比如在IntegerField输入非数字时会视为未输入，而不是类型错误。
至此，已经介绍了在Python中处理HTML表单的所有基本内容。完整的表单处理过程的流程图如：

![img](flask入门.assets/epub_22987517_236)

## 表单实践

### 设置错误消息语言

WTForms内置了多种语言的错误消息，如果想改变内置错误消息的默认语言，可以通过自定义表单基类实现(Flask-WTF版本>0.14.2)。
实现这个功能需要确保Flask-WTF版本>0.14.2或单独使用WTForms。
示例程序创建了一个My Base Form基类，所有继承这个基类的表单类的内置错误消息语言都会设为简体中文。

```
from flask_wtf import FlaskForm
from wtforms import StringField,SubmitField
from wtforms.validators import DataRequired

class TestForm02(FlaskForm):
    class Meta:
        locals=['zh']
class TestForm03(TestForm02):
    name=StringField('name',validators=[DataRequired()])
    submit=SubmitField()

app = Flask(__name__)
app.config['WTF_I18N_ENABLED'] = False
app.config["SECRET_KEY"] = "12345678"

@app.route('/test03',methods=['GET','POST'])
def test03():
    form=TestForm03()
    if form.validate_on_submit():
        name=form.name
        flash('hello %s'%name)
        return redirect(url_for('index'))
    return render_template('TestForm03.html',form=form)
```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>This is a test!</title>
</head>
<body>
<form method="post">
{{ form.csrf_token }}
    <div>
        <h1>This is a test!</h1><br>
    </div>
{{ form.name }}
{{ form.submit }}
</form>
</body>
</html>
```

首先，需要将配置变量WTF_I18N_ENABLED设为False，这会让Flask-WTF使用WTForms内置的错误消息翻译。然后需要在自定义基类中定义Meta类，并在locales列表中加入简体中文的地区字符串。在创建表单时，继承这个My BaseForm即可将错误消息语言设为中文，比如上面定义的TestForm03。另外，也可以在实例化表单类时通过meta关键字传入locales值，比如:

```
form = MyForm(meta={'locales': ['en_US', 'en']})
```

locales属性是一个根据优先级排列的地区字符串列表。在WTForms中，简体中文和繁体中文的地区字符串分别为zh和zh_TW。

### 使用宏渲染表单

在模板中渲染表单时，有大量的工作要做:
1、调用字段属性，获取\<input\>定义。
2、调用对应的label属性，获取\<label\>定义。
3、渲染错误消息。

为了避免为每一个字段重复这些代码，可以创建一个宏来渲染表单字段：

```
{% macro form_field(field) %}
	{{ field.label }}<br>
	{{ field(＊＊kwargs) }}<br>
	{% if field.errors %}
		{% for error in field.errors %}
			<small class="error">{{ error }}</small><br>
		{% endfor %}
	{% endif %}
{% endmacro %}
```

这个form_field()宏接收表单类实例的字段属性和附加的关键字参数作为输入，返回包含\<label\>标签、表单字段、错误消息列表的HTML表单字段代码。使用这个宏渲染表单的示例如下所示:

```
{% from 'macros.html' import form_field %}
.
<form method="post">
    {{ form.csrf_token }}
    {{ form_field(form.username)}}<br>
    {{ form_field(form.password) }}<br>
	.
</form>
```

在上面的代码中，调用form_field()宏逐个渲染表单中的字段，只要把每一个类属性传入form_field()宏，即可完成渲染。

### 自定义验证器

在WTForms中，验证器是指在定义字段时传入validators参数列表的可调用对象。

1、行内验证器

除了使用WTForms提供的验证器来验证表单字段，还可以在表单类中定义方法来验证特定字段：

```
class MustBe07(FlaskForm):
    answer=IntegerField('The number')
    submit=SubmitField('Enter')
    def validate_answer(form,field):
        if field.data!=7:
            raise ValidationError("Must be 7")
```

当表单类中包含以“validate_字段属性名”形式命名的方法时，在验证字段数据时会同时调用这个方法来验证对应的字段，这也是为什么表单类的字段属性名不能以validate开头。验证方法接收两个位置参数，依次为form和field，前者为表单类实例，后者是字段对象，可以通过field.data获取字段数据，这两个参数将在验证表单时被调用传入。验证出错时抛出从wtforms.validators模块导入的Validation Error异常，传入错误消息作为参数。因为这种方法仅用来验证特定的表单类字段，所以又称为行内验证器(in-line validator)。

```
from forms import MustBe07
@app.route("/must",methods=['GET',"POST"])
def mustbe07():
    form=MustBe07()
    if form.validate_on_submit():
        return redirect(url_for('index'))
    return render_template("mustbe07.html",form=form)
```

```
<!DOCTYPE html>
<html lang="en">
{% from 'macros.html' import form_field %}
<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css' ) }}">
    <title>MustBe07</title>
    <h1>MustBe07</h1>

</head>
<body>
    <form method="post">
        {{ form.answer.label }}<br>
        {{ form.answer}}<br>
        {% if form.answer.errors %}
            {% for message in form.answer.errors %}
                <small class="error">{{ message }}</small><br>
            {% endfor %}
        {% endif %}
        <br>{{ form.submit}}
    </form>

</body>
</html>
```

运行效果：

![image-20210225113452577](flask入门.assets/image-20210225113452577.png)

### 全局验证器

如果想要创建一个可重用的通用验证器，可以通过定义一个函数实现。如果不需要传入参数定义验证器，那么一个和表单类中定义的验证方法完全相同的函数就足够了。

```
def is_10(form,field):
    if field.data!=10:
        raise ValidationError("is not 10!")
class MustBe07(FlaskForm):
    answer=IntegerField('The number',validators=[is_10])
    submit=SubmitField('Enter')
    def validate_answer(form,field):
        if field.data!=7:
            raise ValidationError("Must be 7")
```

运行效果：

![image-20210225114050474](flask入门.assets/image-20210225114050474.png)

当使用函数定义全局的验证器时，需要在定义字段时在validators列表里传入这个验证器。因为在validators列表中传入的验证器必须是可调用对象，所以这里传入了函数对象，而不是函数调用。
这仅仅是一个简单的示例，在现实中，通常需要让验证器支持传入参数来对验证过程进行设置。至少，应该支持message参数来设置自定义错误消息。这时验证函数应该实现成工厂函数，即返回一个可调用对象的函数：

```
from wtforms.validators import ValidationError

        def is_42(message=None):
            if message is None:
                message = 'Must be 42.'

            def _is_42(form, field):
                if field.data ! = 42:
                    raise ValidationError(message)

return _is_42

        class FortyTwoForm(FlaskForm):
            answer = IntegerField('The Number', validators=[is_42()])
            submit = SubmitField()
```

在现在的is_42()函数中，创建了另一个_is_42()函数，这个函数会被作为可调用对象返回。is_42()函数接收的message参数用来传入自定义错误消息，默认为None，如果没有设置就使用内置消息。在validators列表中，这时需要传入的是对工厂函数is_42()的调用。

## 文件上传

在HTML中，渲染一个文件上传字段只需要将<input>标签的type属性设为file,即<input type="file">。这会在浏览器中渲染成一个文件上传字段，单击文件选择按钮会打开文件选择窗口，选择对应的文件后，被选择的文件名会显示在文件选择按钮旁边。
在服务器端，可以和普通数据一样获取上传文件数据并保存。不过需要考虑安全问题，文件上传漏洞也是比较流行的攻击方式。除了常规的CSRF防范，还需要重点注意下面的问题:
1、验证文件类型。
2、验证文件大小。
3、过滤文件名。

### 定义上传表单

在Python表单类中创建文件上传字段时，使用扩展Flask-WTF提供的FileField类，它继承WTForms提供的上传字段File Field，添加了对Flask的集成。

```
class UploadPhoto(FlaskForm):
    photo=FileField("Upload Photo",validators=[FileRequired(),FileAllowed(['jpg','png','jpeg','gif'])])
    submit=SubmitField("Upload")
```

为了便于测试，创建一个用来上传图片的photo字段。和其他字段类似，也需要对文件上传字段进行验证。Flask-WTF在flask_wtf.file模块下提供了两个文件相关的验证器：

![image-20210303174152204](flask入门.assets/image-20210303174152204.png)

使用File Required确保提交的表单字段中包含文件数据。出于安全考虑，必须对上传的文件类型进行限制。如果用户可以上传HTML文件，而且同时提供了视图函数获取上传后的文件，那么很容易导致XSS攻击。使用FileAllowed设置允许的文件类型，传入一个包含允许文件类型的后缀名列表。
Flask-WTF提供的File Allowed是在服务器端验证上传文件，使用HTML5中的accept属性也可以在客户端实现简单的类型过滤。这个属性接收MIME类型字符串或文件格式后缀，多个值之间使用逗号分隔，比如:

```
<input type="file" id="profile_pic" name="profile_pic" accept=".jpg, .jpeg, .png, .gif">
```

当用户单击文件选择按钮后，打开的文件选择窗口会默认将accept属性值之外的文件过滤掉。尽管如此，用户还是可以选择设定之外的文件，所以仍然需要进行服务器端验证。

扩展Flask-Uploads ( https://github.com/maxcountryman/flask-uploads)内置了在Flask中实现文件上传的便利功能。Flask-WTF提供的File Allowed()也支持传入Flask-Uploads中的上传集对象（Upload Set)作为upload_se
t参数的值。另外，同类的扩展还有Flask-Transfer ( https://github.com/justanr/Flask-Transfer)。

除了验证文件的类型，通常还需要对文件大小进行验证，肯定不想让用户上传超大的文件来拖垮服务器。通过设置Flask内置的配置变量MAX_CONTENT_LENGTH，可以限制请求报文的最大长度，单位为字节(byte)。比如，下面将最大长度限制为3M:

```
app.config['MAX_CONTENT_LENGTH'] = 3 ＊ 1024 ＊ 1024
```

当请求数据(上传文件大小）超过这个限制后，会返回413错误响应（RequestEntity Too Large).

可以创建对应的错误处理函数来返回自定义的413错误响应。需要注意，Flask内置的开发服务器在抛出对应的异常时不会返回413响应，而是中断连接。不过不用担心这个问题，当使用生产环境下的服务器时，会正确返回413错误响应。

### 渲染上传表单

在新创建的upload视图里，实例化表单类UploadForm，然后传入模板:

```
from forms import UploadPhoto

@app.route("/uploadphoto",methods=['GET','POST'])
def uploadphoto():
    form=UploadPhoto()
	...
    return render_template('uploadphoto.html',form=form)
```

渲染表单：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form method="post" enctype="multipart/form-data">
        {{ form.csrf_token }}
        {{ form.photo }}
        {{ form.submit }}
    </form>
        {% for message in get_flashed_messages() %}
        <div class="alert">
            {{ message }}
        </div>
    {% endfor %}
</body>
</html>
```

**唯一需要注意的是，当表单中包含文件上传字段时（即type属性为file的input标签)，需要将表单的enctype属性设为"multipart/form-data"，这会告诉浏览器将上传数据发送到服务器，否则仅会把文件名作为表单数据提交。**

### 处理上传文件

和普通的表单数据不同，当包含上传文件字段的表单提交后，上传的文件需要在请求对象的files属性( request.files)中获取。这个属性是Werkzeug提供的Immutable Multi Dict字典对象，存储字段的name键值和文件对象的映射，比如:

```
ImmutableMultiDict([('photo', <FileStorage: u'0f913b0ff95.JPG' ('image/jpeg')>)])
```

上传的文件会被Flask解析为Werkzeug中的FileStorage对象(werkzeug.datastructures. FileStorage)。当手动处理时，需要使用文件上传字段的name属性值作为键获取对应的文件对象。比如:

```
request.files.get('photo')
```

当使用Flask-WTF时，它会自动获取对应的文件对象，这里仍然使用表单类属性的data属性获取上传文件。

```
from forms import UploadPhoto
import os
app.config['UPLOAD_PATH']=os.path.join(app.root_path,'uploads')

@app.route("/uploadphoto",methods=['GET','POST'])
def uploadphoto():
    form=UploadPhoto()
    if form.validate_on_submit():
        photo=form.photo.data
        # photoname=random_filename(photo.filename)
        photoname=photo.filename
        photo.save(os.path.join(app.config['UPLOAD_PATH'],photoname))
        flash('Upload success.')
        session['photonames']=[photoname]
        return redirect(url_for('showphoto'))
    return render_template('uploadphoto.html',form=form)
```

当表单通过验证后，通过form.photo.data获取存储上传文件的File Storage对象。接下来，需要处理文件名，通常有三种处理方式:
(1）使用原文件名
如果能够确定文件的来源安全，可以直接使用原文件名，通过File Storage对象的filename属性获取:

```
photoname = photo.filename
```

(2)使用过滤后的文件名
如果要支持用户上传文件，必须对文件名进行处理，因为攻击者可能会在文件名中加入恶意路径。比如，如果恶意用户在文件名中加入表示上级目录的.. (比如../.../../home/username/.bashrc或... ../../etc/passwd)，那么当保存文件时，如果这里表示上级目录的..数量正确，就会导致服务器上的系统文件被覆盖或篡改，还有可能执行恶意脚本。可以使用Werkzeug提供的secure_filename()函数对文件名进行过滤，传递文件名作为参数，它会过滤掉所有危险字符，返回“安全的文件名”，如下所示:

```
>>> from werkzeug.utils import secure_filename
>>> secure_filename('avatar! @#//#\\%$^&.jpg')
'avatar.jpg'
>>> secure_filename('avatar头像．jpg')
'avatar.jpg'
```

(3）统一重命名
secure_filename()函数非常方便，它会过滤掉文件名中的非ASCII字符。但如果文件名完全由非ASCII字符组成，那么会得到一个空文件名:

```
>>> secure_filename(’头像．jpg')
        'jpg'
```

为了避免出现这种情况，更好的做法是使用统一的处理方式对所有上传的文件重新命名。随机文件名有很多种方式可以生成，下面是一个使用Python内置的uuid模块生成随机文件名的random_filename()函数:

```
def random_filename(filename):
    ext = os.path.splitext(filename)[1]
    new_filename = uuid.uuid4().hex + ext
    return new_filename
```

UUID (Universally Unique ldentifier，通用唯一识别码）是用来标识信息的128位数字，比如用作数据库表的主键。使用标准方法生成的UUID出现重复的可能性接近0。在UUID的标准中，UUID分为5个版本，每个版本使用不同的生成方法并且适用于不同的场景。使用的uuid4()方法对应的是第4个版本:不接收参数而生成随机UUID。

在uploadphoto视图中，调用这个函数来获取随机文件名，传入原文件名作为参数:

```
photoname=random_filename(photo.filename)
```

处理完文件名后，是时候将文件保存到文件系统中了。在form目录下创建了一个uploads文件夹，用于保存上传后的文件。指向这个文件夹的绝对路径存储在自定义配置变量UPLOAD_PATH中:

```
app.config['UPLOAD_PATH']=os.path.join(app.root_path,'uploads')
```

这里的路径通过app.root \_path属性构造，它存储了程序实例所在脚本的绝对路径，相当于os.path.abspath(os.path.dirname(\_file\_))。为了保存文件，需要提前手动创建这个文件夹。
对File Storage对象调用save()方法即可保存，传入包含目标文件夹绝对路径和文件名在内的完整保存路径:

文件保存后，希望能够显示上传后的图片。为了让上传后的文件能够通过URL获取，还需要创建一个视图函数来返回上传后的文件:

```
@app.route("/getfile/<path:filename>")
def getfile(filename):
    return send_from_directory(app.config["UPLOAD_PATH"],filename)
```

下载测试文件：

![image-20210304160930661](flask入门.assets/image-20210304160930661.png)

![image-20210304161028389](flask入门.assets/image-20210304161028389.png)

这个视图的作用与Flask内置的static视图类似，通过传入的文件路径返回对应的静态文件。在这个uploads视图中，使用Flask提供的send_from_directory()函数来获取文件，传入文件的路径和文件名作为参数。

在getfile视图的URL规则中，filename变量使用了path转换器以支持传入包含斜线的路径字符串。
在upload视图里保存文件后，使用flash()发送一个提示，将文件名保存到session中，最后重定向到show_images视图。show_images视图返回的uploaded.html模板中将从session获取文件名，渲染出上传后的图片。

这里将filename作为列表传入session只是为了兼容下面的多文件上传示例，这两个视图使用同一个模板，使用session可以在模板中统一从session获取文件名列表。在uploaded.html模板里，将传入的文件名作为URL变量，通过上面的getfile视图获取文件URL，作为\<img\>标签的src属性值:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{% if session.photonames %}
{% for photoname in session.photonames %}
<a href="{{ url_for("getfile",filename=photoname) }}" target="_blank">
    <img src="{{ url_for("getfile",filename=photoname) }}">
</a>
{% endfor %}
{% endif %}

{% for message in get_flashed_messages() %}
<div class="alert">
    {{ message }}
</div>
{% endfor %}

</body>
</html>
```

访问http://localhost:5000/uploadphoto打开文件上传示例，选择文件并提交后即可看到上传后的图片。另外，会在示例程序文件夹中的uploads目录下发现上传的文件。

![image-20210304194534364](flask入门.assets/image-20210304194534364.png)

### 多文件上传

因为Flask-WTF当前版本（0.14.2)中并未添加对多文件上传的渲染和验证支持，因此需要在视图函数中手动获取文件并进行验证。这种手动处理方式和在上一节介绍的方式效果基本相同。
在客户端，通过在文件上传字段(type=file)加入multiple属性，就可以开启多选:

```
<input type="file" id="file" name="file" multiple>
```

创建表单类时，可以直接使用WTForms提供的Multiple File Field字段实现，添加一个Data Required验证器来确保包含文件:

```
from wtforms import  MultipleFileField
class MultiUploadPhoto(FlaskForm):
    photo=MultipleFileField("Upload Photo",validators=[DataRequired()])
    submit=SubmitField("Start Upload")
```

表单提交时，在服务器端的程序中，对request.files属性调用getlist()方法并传入字段的name属性值会返回包含所有上传文件对象的列表。在multi_upload视图中，迭代这个列表，然后逐一对文件进行处理：

```
from forms import MultiUploadPhoto
@app.route("/multi",methods=['POST','GET'])
def multi():
    form=MultiUploadPhoto()
    if request.method=="POST":
        filenames=[]
        #验证CSRF令牌
        try:
            validate_csrf(form.csrf_token.data)
        except:
            flash('CSRF token error')
            return redirect(url_for('multi'))
        #检查文件是否存在
        if 'photo' not in request.files:
            flash("this field is required")
            redirect(url_for('multi'))

        for f in request.files.getlist('photo'):
            # 检查文件类型
            if f and allowed_file(f.filename):
                filename=random_filename(f.filename)
                f.save(os.path.join(app.config['UPLOAD_PATH'],filename))
                filenames.append(filename)
            else:
                flash("Invalid file type")
                return redirect(url_for('multi'))
        flash("Upload success")
        flash(filenames)
        session['photonames']=filenames
        flash(session["photonames"])
        flash("show start")
        return redirect(url_for('showphoto'))
    return render_template('uploadphoto.html',form=form)
```

在请求方法为POST时，对上传数据进行手动验证，主要包含下面几步:
1）手动调用flask_wtf.csrf.validate_csrf验证CSRF令牌，传入表单中csrf_token隐藏字段的值。如果抛出wtforms.Validation Error异常则表明验证未通过。
2）其中if 'photo' not in request.files用来确保字段中包含文件数据（相当于FileRequired验证器），如果用户没有选择文件就提交表单则request.files将为空。
3) if f用来确保文件对象存在，这里也可以检查f是否是File Storage实例。
4) allowed_file(f.filename)调用了allowed_file()函数，传入文件名。这个函数相当于File Allowed验证器，用来验证文件类型，返回布尔值：

```
app.config['ALLOWED_EXTENSIONS'] = ['png', 'jpg', 'jpeg', 'gif']
def allowed_file(filename):
    return '.' in filename and \
           filename.rsplit('.', 1)[1].lower() in app.config['ALLOWED_EXTENSIONS']
```

在上面的几个验证语句里，如果没有通过验证，使用flash()函数显示错误消息，然后重定向到multi_upload视图。
为了方便测试，还创建了一个临时的filenames列表，保存上传后的文件名到session中。访问http://localhost:5000/multi打开多文件上传示例，单击按钮后可以选择多个文件，当上传的文件通过验证时，程序会重定向到show_images视图，这个视图返回的uploaded.html模板中将从session获取所有文件名，渲染出所有上传后的图片。

在新版本的Flask-WTF发布后，可以使用和单文件上传相同的方式处理表单。比如，可以使用Flask-WTF提供的Multiple File Field来创建提供Flask支持的多文件上传字段，使用相应的验证器对文件进行验证。在视图函数中，则可以继续使用form.validate_on_submit()来验证表单，并通过form.photo.data来获取字段的数据——包含所有上传文件对象(werkzeug.datastructures.File Storage)的列表。

## 使用Flask-CKEditor集成富文本编辑器

富文本编辑器即WYSIWYG (What You See Is What You Get，所见即所得）编辑器，类似于经常使用的文本编辑软件。它提供一系列按钮和下拉列表来为文本设置格式，编辑状态的文本样式即最终呈现出来的样式。在Web程序中，这种编辑器也称为HTML富文本编辑器，因为它使用HTML标签来为文本定义样式。
CKEditor (http://ckeditor.com/）是一个开源的富文本编辑器，它包含丰富的配置选项，而且有大量第三方插件支持。扩展Flask-CKEditor简化了在Flask程序中使用CKEditor的过程，将使用它来集成CKEditor。首先使用Pipenv安装:

```
pipenv install flask-ckeditor
```

### 配置编辑器

flask-ckeditor常用配置：

![image-20210324104810004](flask入门.assets/image-20210324104810004.png)

为方便开发，使用内置的本地资源：

```
app.config['CKEDITOR_SERVE_LOCAL'] = True
```

### 渲染编辑器

富文本编辑器在HTML中通过文本区域字段表示，即\<textarea\>\</textarea\>。Flask-CKEditor通过包装WTForms提供的Text Area Field字段类型实现了一个CKEditor Field字段类，使用它来构建富文本编辑框字段。

```
from flask_wtf import FlaskForm
from wtforms import StringField,SubmitField
from wtforms.validators import DataRequired,Length
from flask_ckeditor import CKEditorField

class RichTextForm(FlaskForm):
    title=StringField('Title',validators=[DataRequired(),Length(1,50)])
    body=CKEditorField('Body',validators=[DataRequired()])
    submit=SubmitField('Publish')
```

文章正文字段(body)使用的CKEditor Field字段类型从Flask-CKEditor导入。可以像其他字段一样定义标签、验证器和默认值。在使用上，这个字段和WTForms内置的其他字段完全相同。比如，在提交表单时，同样使用data属性获取数据。
在模板中，渲染这个body字段的方式和其他字段也完全相同:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Rich Text Form</title>
</head>
<body>
<h1>Rich Text Form</h1>
<form method="post">
    {{ form.csrf_token }}
    {{ form.title.label }}
    {{ form.title}}
    <br><br>
    {{ form.body }}
    <br><br>
    {{ form.submit }}
</form>
</body>
{{ ckeditor.load() }}
</html>
```

渲染CKEditor编辑器需要加载相应的Java Script脚本。在开发时，为了方便开发，可以使用Flask-CKEditor在模板中提供的ckeditor.load()方法加载资源，它默认从CDN加载资源，将CKEDITOR_SERVE_LOCAL设为Ture会使用扩展内置的本地资源，内置的本地资源包含了几个常用的插件和语言包。ckeditor.load()方法支持通过pkg_type参数传入包类型，这会覆盖配置CKEDITOR_PKG_TYPE的值，额外的version参数可以设置从CDN加载的CKEditor版本。

作为替代，可以访问CKEditor官网提供的构建工具( https://ckeditor.com/cke4/builder)构建自己的CKEditor包，下载后放到static目录下，然后在需要显示文本编辑器的模板中加载包目录下的ckeditor.js文件，替换掉ckeditor.load()调用。
如果使用配置变量设置了编辑器的高度、宽度和语言或是其他插件配置，需要使用ckeditor.config()方法加载配置，传入对应表单字段的name属性值，即对应表单类属性名。这个方法需要在加载CKEditor资源后调用:

```
 {{ ckeditor.config(name='body') }}
```

### 单个表单多个提交按钮

在某些情况下，可能需要为一个表单添加多个提交按钮。比如在创建文章的表单中添加发布新文章和保存草稿的按钮。当用户提交表单时，需要在视图函数中根据按下的按钮来做出不同的处理。以下代码建了一个这样的表单，其中save表示保存草稿按钮, publish表示发布按钮，正文字段使用Text Area Field字段：

```
from flask_wtf import FlaskForm
from wtforms import StringField,SubmitField,TextAreaField
from wtforms.validators import DataRequired,Length

class RichTextForm(FlaskForm):
    title=StringField('Title',validators=[DataRequired(),Length(1,50)])
    body=TextAreaField('Body',validators=[DataRequired()])
    publish=SubmitField('Publish')
    save=SubmitField('Save')
```

当表单数据通过POST请求提交时，Flask会把表单数据解析到request.form字典。如果表单中有两个提交字段，那么只有被单击的提交字段才会出现在这个字典中。当对表单类实例或特定的字段属性调用data属性时，WTForms会对数据做进一步处理。对于提交字段的值，它会将其转换为布尔值:被单击的提交字段的值将是True，未被单击的值则是False。
基于这个机制，可以通过提交按钮字段的值来判断当前被单击的按钮：

```
from flask import Flask,render_template,url_for,flash,redirect
from flask_ckeditor import CKEditor
from forms import RichTextForm


app=Flask(__name__)
app.config['CKEDITOR_SERVE_LOCAL'] = True
app.secret_key='abcdefg'
ckeditor=CKEditor(app)

@app.route("/ckeditor",methods=['POST','GET'])
def ckeditor():
    form=RichTextForm()
    if form.validate_on_submit():
        if form.save.data:
            flash("Save successful!")
        elif form.publish.data:
            flash("Publish successful!")
        return redirect(url_for('index'))
    return render_template('richtextform.html',form=form)

@app.route('/',methods=['POST','GET'])
def index():
    return render_template('index.html')
```

### 单个页面多个表单

除了在单个表单上实现多个提交按钮，有时还需要在单个页面上创建多个表单。比如，在程序的主页上同时添加登录和注册表单。当在同一个页面上添加多个表单时，要解决的一个问题就是在视图函数中判断当前被提交的是哪个表单。

#### 单视图处理

创建两个表单，并在模板中分别渲染并不是难事，但是当提交某个表单时，就会遇到问题。Flask-WTF根据请求方法判断表单是否提交，但并不判断是哪个表单被提交，所以需要手动判断。基于上一节介绍的内容，知道被单击的提交字段最终的data属性值是布尔值，即True或False。而解析后的表单数据使用input字段的name属性值作为键匹配字段数据，也就是说，如果两个表单的提交字段名称都是submit，那么也无法判断是哪个表单的提交字段被单击。
解决问题的第一步就是为两个表单的提交字段设置不同的名称：

```
class SignForm(FlaskForm):
    username=StringField('Username',validators=[DataRequired(),Length(1,20)])
    password=PasswordField('password',validators=[DataRequired(),Length(8,128)])
    signin=SubmitField('Sign in')

class RegisterForm(FlaskForm):
    username=StringField('Username',validators=[DataRequired(),Length(1,20)])
    password=PasswordField('password',validators=[DataRequired(),Length(8,128)])
    email=StringField('Email',validators=[DataRequired(),Email(),Length(1,128)])
    register=SubmitField('Register')
```

在视图函数中，分别实例化这两个表单，根据提交字段的值来区分被提交的表单:

```
from forms import SignForm,RegisterForm
#登录和注册app
@app.route('/login',methods=['POST'])
def login():
    sign_form=SignForm()
    register_form=RegisterForm()

    if sign_form.validate_on_submit() and sign_form.signin.data:
        flash('Sign successful!')
        return redirect(url_for('index'))
    if register_form.validate_on_submit() and register_form.register.data:
        flash('Register successful!')
        return redirect(url_for('index'))
    return render_template('login.html',sign_form=sign_form,register_form=register_form)
```

在视图函数中，为两个表单添加了各自的if判断，在这两个if语句的内部，分别执行各自的代码逻辑。以登录表单( Signin Form)的if判断为例，如果signin_form.submit1.data的值为True，那就说明用户提交了登录表单，这时手动调用signin_form.validate()对这个表单进行验证。
这两个表单类实例通过不同的变量名称传入模板，以便在模板中相应渲染对应的表单字段:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
<form method="POST">
    {{ sign_form.csrf_token }}
    {{ sign_form.username.label }}
    {{ sign_form.username }}
    <br><br>
    {{ sign_form.password.label }}
    {{ sign_form.password }}
    <br><br>
    {{ sign_form.signin }}
</form>
<form method="POST">
    {{ register_form.csrf_token }}
    {{ register_form.username.label }}
    {{ register_form.username }}
    <br><br>
    {{ register_form.password.label }}
    {{ register_form.password }}
    <br><br>
    {{ register_form.email.label }}
    {{ register_form.email }}
    <br><br>
    {{ register_form.register }}
</form>
</body>
</html>
```

#### 多视图处理

除了通过提交按钮判断，更简洁的方法是通过分离表单的渲染和验证实现。这时表单的提交字段可以使用同一个名称，在视图函数中处理表单时也只需使用熟悉的form.validate_on_submit()方法。
在介绍表单处理时，在同一个视图函数内处理两类工作:渲染包含表单的模板(GET请求)、处理表单请求(POST请求)。如果想解耦这部分功能，那么也可以分离成两个视图函数处理。当处理多个表单时，可以把表单的渲染在单独的视图函数中处理：

```
#仅渲染表单
@app.route('/xr_login')
def xr_login():
    sign_form=SignForm()
    register_form=RegisterForm()
    return render_template('login.html',sign_form=sign_form,register_form=register_form)
```

这个视图只负责处理GET请求，实例化两个表单类并渲染模板。另外再为每一个表单单独创建一个视图函数来处理验证工作。处理表单提交请求的视图仅监听POST请求:

```
#仅处理表单
@app.route('/handle_signin',methods=['POST'])
def handle_signin():
    sign_form=SignForm()
    if sign_form.validate_on_submit():
        flash('Sign in !')
        return redirect(url_for('index'))
    return render_template('index.html',sign_form=sign_form)

@app.route('/handle_register',methods=['POST'])
def handle_register():
    register_form=RegisterForm()
    if register_form.validate_on_submit():
        flash('Register !')
        return redirect(url_for('index'))
    return render_template('index.html',register_form=register_form)
```

在HTML中，表单提交请求的目标URL通过action属性设置。为了让表单提交时将请求发送到对应的URL，需要设置action属性:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
</head>
<body>
<form method="POST" action="{{ url_for('handle_signin') }}">
    {{ sign_form.csrf_token }}
    {{ sign_form.username.label }}
    {{ sign_form.username }}
    <br><br>
    {{ sign_form.password.label }}
    {{ sign_form.password }}
    <br><br>
    {{ sign_form.signin }}
</form>
<form method="POST" action="{{ url_for('handle_register') }}">
    {{ register_form.csrf_token }}
    {{ register_form.username.label }}
    {{ register_form.username }}
    <br><br>
    {{ register_form.password.label }}
    {{ register_form.password }}
    <br><br>
    {{ register_form.email.label }}
    {{ register_form.email }}
    <br><br>
    {{ register_form.register }}
</form>
</body>
</html>
```

虽然现在可以正常工作，但是这种方法有一个显著的缺点。如果验证未通过，需要将错误消息的form.errors字典传入模板中。在处理表单的视图中传入表单错误信息，就意味着需要再次渲染模板，但是如果视图函数中还涉及大量要传入模板的变量操作，那么这种方式会带来大量的重复。
对于这个问题，一般的解决方式是通过其他方式传递错误消息，然后统一重定向到渲染表单页面的视图。比如，使用flash()函数迭代form.errors字典发送错误消息(这个字典包含字段名称与错误消息列表的映射)，然后重定向到用来渲染表单的xr_login视图。下面是一个使用flash()函数来发送表单错误消息的便利函数:

```
def flash_errors(form):
            for field, errors in form.errors.items():
                for error in errors:
                    flash(u"Error in the %s field - %s" % (
                        getattr(form, field).label.text,
                        error
                    ))
```

如果希望像往常一样在表单字段下渲染错误消息，可以直接将错误消息字典form.errors存储到 session中，然后重定向到用来渲染表单的multi_form_multi_view视图。在模板中渲染表单字段错误时添加一个额外的判断，从session中获取并迭代错误消息。

## 数据库

数据库是大多数动态Web程序的基础设施，只要想把数据存储下来就离不开数据库。这里提及的数据库(Database）指的是由存储数据的单个或多个文件组成的集合，它是一种容器，可以类比为文件柜。而人们通常使用数据库来表示操作数据库的软件，这类管理数据库的软件被称为数据库管理系统（DBMS,Database Management System)，常见的DBMS有My SQL、Postgre sQL、SQLite、Mongo DB等。为了便于理解，可以把数据库看作一个大仓库，仓库里有一些负责搬运货物（数据）的机器人，而DBMS就是操控机器人搬运货物的程序。

### 数据库分类

数据库一般分为两种，SQL (Structured Query Language，结构化查询语言)数据库和No SQL (Not Only SQL，泛指非关系型)数据库。

SQL数据库指关系型数据库，常用的SQL DBMS主要包括SQL Server、Oracle、My SQL、Postgre SQL、SQLite等。关系型数据库使用表来定义数据对象，不同的表之间使用关系连接。身份信息表示例：

![image-20210401165054805](flask入门.assets/image-20210401165054805.png)

在SQL数据库中，每一行代表一条记录(record)，每条记录又由不同的列(column）组成。在存储数据前，需要预先定义表模式(schema)，以定义表的结构并限定列的输入数据类型。
为了避免在措辞上引起误解，先了解几个基本概念:
1）表(table) :存储数据的特定结构。
2）模式(schema) :定义表的结构信息。
3）列/字段(column/field) :表中的列，存储一系列特定的数据，列组成表。
4）行/记录(row/record) :表中的行，代表一条记录。
5）标量(scalar) :指的是单一数据，与之相对的是集合(collection)。

No SQL最初指No SQL或 No Relational，现在No SQL社区一般会解释为NotOnly SQL。No SQL数据库泛指不使用传统关系型数据库中的表格形式的数据库。近年来，No SQL数据库越来越流行，被大量应用在实时(real-time) Web程序和大型程序中。与传统的SQL数据库相比，它在速度和可扩展性方面有很大的优势，除此之外还拥有无模式( schema-free) 、分布式、水平伸缩（ horizontallyscalable）等特点。
最常用的两种No SQL数据库如下所示:

#### 文档存储

文档存储是No SQL数据库中最流行的种类，它可以作为主数据库使用。文档存储使用的文档类似SQL数据库中的记录，文档使用类JSON格式来表示数据。常见的文档存储DBMS有Mongo DB、Couch DB等。上面身份信息表中的第一条记录使用文档可以表示为:

```
{
    id: 1,
    name: "Nick",
    sex: "Male"
    occupation: "Journalist"
}
```

#### 键值对存储

键值对存储在形态上类似Python中的字典，通过键来存取数据，在读取上非常快，通常用来存储临时内容，作为缓存使用。常见的键值对DBMS有Redis、Riak等，其中Redis不仅可以管理键值对数据库，还可以作为缓存后端 ( cachebackend)和消息代理(message broker)。
另外，还有列存储(column store，又被称为宽列式存储)、图存储（ graphstore）等类型的No SQL数据库，这里不再展开介绍。

No SQL数据库不需要定义表和列等结构，也不限定存储的数据格式，在存储方式上比较灵活，在特定的场景下效率更高。SQL数据库稍显复杂，但不容易出错，能够适应大部分的应用场景。这两种数据库都各有优势，也各有擅长的领域。两者并不是对立的，需要根据使用场景选择适合的数据库类型。大型项目通常会同时需要多种数据库，比如使用My SQL作为主数据库存储用户资料和文章，使用Redis(键值对型数据库）缓存数据，使用Mongo DB(文档型数据库）存储实时消息。大多数情况下，SQL数据库都能满足需求。为了便于开发和测试，示例程序都使用SQLite作为DBMS。对于大型程序，在部署程序前，需要根据程序的特点来改用更健壮的DBMS。

### ORM

在Web应用里使用原生SQL语句操作数据库主要存在下面两类问题:
1、手动编写SQL语句比较乏味，而且视图函数中加入太多SQL语句会降低代码的易读性。另外还会容易出现安全问题，比如SQL注入。
2、常见的开发模式是在开发时使用简单的SQLite，而在部署时切换到My SQL等更健壮的DBMS。但是对于不同的DBMS，需要使用不同的Python接口库，这让DBMS的切换变得不太容易。

尽管使用ORM可以避免SQL注入问题，但仍然需要对传入的查询参数进行验证。另外，在执行原生SQL语句时也要注意避免使用字符串拼接或字符串格式化的方式传入参数。
使用ORM可以很大程度上解决这些问题。它会自动处理查询参数的转义，尽可能地避免SQL注入的发生。另外，它为不同的DBMS提供统一的接口，让切换工作变得非常简单。ORM扮演翻译的角色，能够将的Python语言转换为DBMS能够读懂的SQL指令，让能够使用Python来操控数据库。

尽管ORM非常方便，但如果对SQL相当熟悉，那么自己编写SQL代码可以获得更大的灵活性和性能优势。就像是使用IDE一样，ORM对初学者来说非常方便，但进阶以后也许会想要自己掌控一切。
ORM把底层的SQL数据实体转化成高层的Python对象，这样一来，甚至不需要了解SQL，只需要通过Python代码即可完成数据库操作，ORM主要实现了三层映射关系:
1、表→Python类。
2、字段(列)→类属性。
3、记录(行)→类实例。

比如，要创建一个contacts表来存储留言，其中包含用户名称和电话号码两个字段。在SQL中，下面的代码用来创建这个表:

```
CREATE TABLE contacts(
            name varchar(100) NOT NULL,
            phone_number varchar(32),
        );
```

如果使用ORM，可以使用类似下面的Python类来定义这个表:

```
from foo_orm import Model, Column, String

class Contact(Model):
    __tablename__ = 'contacts'
    name = Column(String(100), nullable=False)
    phone_number = Column(String(32))
```

要向表中插入一条记录，需要使用下面的SQL语句:

```
INSERT INTO contacts(name, phone_number)
        VALUES('Grey Li', '12345678');
```

使用ORM则只需要创建一个Contact类的实例，传入对应的参数表示各个列的数据即可。下面的代码和使用上面的SQL语句效果相同:

```
 contact = Contact(name='Grey Li', phone_number='12345678')
```

除了便于使用，ORM还有下面这些优点:
1、灵活性好。既能使用高层对象来操作数据库，又支持执行原生SQL语句。
2、提升效率。从高层对象转换成原生SQL会牺牲一些性能，但这微不足道的性能牺牲换取的是巨大的效率提升。
3、可移植性好。ORM通常支持多种DBMS，包括MySQL、PostgreSQL、Oracle、SQLite等。可以随意更换DBMS，只需要稍微改动少量配置。

使用Python实现的ORM有SQLAlchemy、 Peewee、Pony ORM等。其中SQLAlchemy是Python社区使用最广泛的ORM之一。SQL-Alchemy，直译过来就是SQL炼金术，会见识到SQLAlchemy的神奇力量。

### 使用Flask-SQLAlchemy管理数据库

扩展Flask-SQLAIchemy集成了SQLAIchemy，它简化了连接数据库服务器、管理数据库操作会话等各类工作，让Flask中的数据处理体验变得更加轻松。首先使用Pipenv安装Flask-SQLAlchemy及其依赖（主要是SQLAlchemy)

```
pipenv install flask-sqlalchemy
```

下面在示例程序中实例化Flask-SQLAlchemy提供的SQLAlchemy类传入程序实例app，以完成扩展的初始化:

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app=Flask(__name__)
db=SQLAlchemy(app)
```

为了便于使用，把实例化扩展类的对象命名为db。这个db对象代表数据库，它可以使用Flask-SQLAlchemy提供的所有功能。

虽然要使用的大部分类和函数都由SQLAlchemy提供，但在Flask-SQLAlchemy中，大多数情况下，不需要手动从SQLAlchemy导入类或函数。在sqlalchemy和sqlalchemy.orm模块中实现的类和函数，以及其他几个常用的模块和对象都可以作为db对象的属性调用。当创建这样的调用时，Flask-SQLAlchemy会自动把这些调用转发到对应的类、函数或模块。

### 连接数据库

DBMS通常会提供数据库服务器运行在操作系统中。要连接数据库服务器，首先要为程序指定数据库URI ( Uniform Resource ldentifier，统一资源标识符)。数据库URI是一串包含各种属性的字符串，其中包含了各种用于连接数据库的信息。

URI代表统一资源标识符，是用来标示资源的一组字符串。URL是它的子集。在大多数情况下，这两者可以交替使用。下表是一些常用的DBMS及其数据库URI格式示例：

![image-20210401174220912](flask入门.assets/image-20210401174220912.png)

在Flask-SQLAlchemy中 ，数据库的URI通过配置变量SQLALCHEMY_DATABASE_URI设置，默认为SQLite内存型数据库( sqlite:///:memory:)。SQLite是基于文件的DBMS，不需要设置数据库服务器，只需要指定数据库文件的绝对路径。使用app.root_path来定位数据库文件的路径，并将数据库文件命名为data.db，如：

```
import os
        ...
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL', 'sqlite:///' +
        os.path.join(app.root_path, 'data.db'))
```

在生产环境下更换到其他类型的DBMS时，数据库URL会包含敏感信息，所以这里优先从环境变量DATABASE_URL获取（注意这里为了便于理解使用了URL，而不是URI) 。

SQLite的数据库URI在Linux或mac OS系统下的斜线数量是4个;在Windows系统下的URl中的斜线数量为3个。内存型数据库的斜线固定为3个。

sQLite数据库文件名不限定后缀，常用的命名方式有foo.sqlite,foo.db，或是注明SQLite版本的foo.sqlite3。
设置好数据库URI后，在Python Shell中导入并查看db对象会获得下面的输出:

```
(helloflask) D:\helloflask\demos\database>python
Python 3.8.5 (tags/v3.8.5:580fbb0, Jul 20 2020, 15:43:08) [MSC v.1926 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> from app import db
>>> db
<SQLAlchemy engine=sqlite:///D:\helloflask\demos\database\data.db>
>>>
```

安装并初始化Flask-SQLAlchemy后，启动程序时会看到命令行下有一行警告信息。这是因为Flask-SQLAlchemy建议设置SQLALCHEMY_TRACK_MODIFICATIONS配置变量，这个配置变量决定是否追踪对象的修改，这用于Flask-SQLAlchemy的事件通知系统。这个配置键的默认值为None，如果没有特殊需要，可以把它设为False来关闭警告信息。

```
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
```

### 定义数据库模型

用来映射到数据库表的Python类通常被称为数据库模型(model) ，一个数据库模型类对应数据库中的一个表。定义模型即使用Python类定义表模式，并声明映射关系。所有的模型类都需要继承Flask-SQLAlchemy提供的db.Model基类。示例程序是一个笔记程序，笔记保存到数据库中，可以通过程序查询、添
加、更新和删除笔记。以下定义了一个Note模型类，用来存储笔记：

```
class Note(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    body=db.Column(db.Text)
```

在上面的模型类中，表的字段（列)由db.Column类的实例表示，字段的类型通过Column类构造方法的第一个参数传入。在这个模型中，创建了一个类型为db.Integer的id字段和类型为db.Text的body列，分别存储整型和文本。常用的SQLAlchemy字段类型如：

![image-20210402174505203](flask入门.assets/image-20210402174505203.png)

字段类型一般直接声明即可，如果需要传入参数，也可以添加括号。对于类似String的字符串列，有些数据库会要求限定长度，因此最好为其指定长度。虽然使用Text类型可以存储相对灵活的变长文本，但从性能上考虑，仅在必须的情况下使用Text类型，比如用户发表的文章和评论等不限长度的内容。
一般情况下，字段的长度是由程序设计者自定的。尽管如此，也有一些既定的约束标准，比如姓名(英语）的长度一般不超过70个字符，中文名一般不超过20个字符，电子邮件地址的长度不超过254个字符，虽然各主流浏览器支持长达2048个字符的URL，但在网站中用户资料设置的限度一般为255。尽管如此，对于超过一定长度的Email和URL，比如20个字符，会在显示时添加省略号的形式。显示的用户名(username）允许重复，通常要短一些，以不超过36个字符为佳。当然，在程序中，可以根据需要来自由设定这些限制值。

当在数据库模型类中限制了字段的长度后，在接收对应数据的表单类字段里，也需要使用Length验证器来验证用户的输入数据。
默认情况下，Flask-SQLAlchemy会根据模型类的名称生成一个表名称，生成规则如下:

```
Message --> message # 单个单词转换为小写
FooBar --> foo_bar # 多个单词转换为小写并使用下划线分隔
```

Note类对应的表名称即note。如果想自己指定表名称，可以通过定义\_tablename\_属性来实现。字段名默认为类属性名，也可以通过字段类构造方法的第一个参数指定，或使用关键字name。根据定义的Note模型类，最终将生成一个note表，表中包含id和body字段。
除了name参数，实例化字段类时常用的字段参数如：

![image-20210402175236305](flask入门.assets/image-20210402175236305.png)

不需要在所有列都建立索引。一般来说，取值可能性多（比如姓名）的列，以及经常被用来作为排序参照的列(比如时间戳)更适合建立索引。
在实例化字段类时，通过把参数primary_key设为True可以将其定义为主键。在定义的Note类中，id字段即表的主键( primary key)。主键是每一条记录(行)独一无二的标识，也是模型类中必须定义的字段，一般命名为id或pk。

### 创建数据库和表

如果把数据库（文件)看作一个仓库，为了方便取用，需要把货物按照类型分别放置在不同货架上，这些货架就是数据库中的表。创建模型类后，需要手动创建数据库和对应的表，也就是常说的建库和建表。这通过对db对象调用create_all()方法实现:

```
(helloflask) D:\helloflask\demos\mydatabasedemo>flask shell
Python 3.8.5 (tags/v3.8.5:580fbb0, Jul 20 2020, 15:43:08) [MSC v.1926 32 bit (Intel)] on win32
App: app [development]
Instance: D:\helloflask\demos\mydatabasedemo\instance
>>> from app import db
>>> db.create_all()
>>>
```

如果将模型类定义在单独的模块中，那么必须在调用db.create_all()方法前导入相应模块，以便让SQLAlchemy获取模型类被创建时生成的表信息，进而正确生成数据表。
通过下面的方式可以查看模型对应的SQL模式（建表语句)︰

```
>>> from sqlalchemy.schema import CreateTable
>>> print(CreateTable(Note.__table__))

CREATE TABLE note (
        id INTEGER NOT NULL,
        body TEXT,
        PRIMARY KEY (id)
)
```
数据库和表一旦创建后，之后对模型的改动不会自动作用到实际的表中。比如，在模型类中添加或删除字段，修改字段的名称和类型，这时再次调用create_all()也不会更新表结构。如果要使改动生效，最简单的方式是调用db.drop_all()方法删除数据库和表，然后再调用db.create_all()方法创建，后面会具体介绍。
也可以自己实现一个自定义flask命令完成这个工作，如：

```
import click
        ...
@app.cli.command()
def initdb():
	db.create_all()
	click.echo('Initialized database.')
```

在命令行下输入flask inintdb即可创建数据库和表:

```
(helloflask) D:\helloflask\demos\mydatabasedemo>flask initdb
Initialized database.

(helloflask) D:\helloflask\demos\mydatabasedemo>dir
 驱动器 D 中的卷是 Data
 卷的序列号是 D6A8-2F0B

 D:\helloflask\demos\mydatabasedemo 的目录

2021/04/02  11:13    <DIR>          .
2021/04/02  11:13    <DIR>          ..
2020/12/16  17:57                21 .flaskenv
2020/12/16  17:57             8,738 app.py
2020/12/16  17:57           122,880 data.db
2020/12/16  17:57    <DIR>          static
2020/12/16  17:57    <DIR>          templates
2021/04/02  11:13    <DIR>          __pycache__
               3 个文件        131,639 字节
               5 个目录 162,468,552,704 可用字节

(helloflask) D:\helloflask\demos\mydatabasedemo>

```

对于示例程序来说，这会在database目录下创建一个data.db文件。

在开发程序或是部署后，经常需要在Python Shell中手动操作数据库（生产环境需注意备份），对于一次性操作，直接处理即可。对于需要重用的操作，可以编写成Flask命令、函数或是模型类的类方法。

### 数据库操作

现在创建了模型，也生成了数据库和表，是时候来学习常用的数据库操作了。数据库操作主要是CRUD，即Create(创建)、Read(读取/查询)、Update(更新）和Delete(删除)。
SQLAlchemy使用数据库会话来管理数据库操作，这里的数据库会话也称为事务(transaction)。Flask-SQLAlchemy自动创建会话，可以通过db.session属性获取。

SQLAlchemy中的数据库会话对象和在前面介绍的Flask中的session无关。数据库中的会话代表一个临时存储区，对数据库做出的改动都会存放在这里。可以调用add()方法将新创建的对象添加到数据库会话中，或是对会话中的对象进行更新。只有当对数据库会话对象调用commit()方法时，改动才被提交到数据库，这确保了数据提交的一致性。另外，数据库会话也支持回滚操作。当对会话调用rollback()方法时，添加到会话中且未提交的改动都将被撤销。

### CURD

这一节会在Python Shell中演示CRUD操作。默认情况下，Flask-SQLAlchemy ( >=2.3.0版本）会自动为模型类生成一个\_repr\_()方法。当在Python Shell中调用模型的对象时，\_repr\_()方法会返回一条类似“<模型类名主键值>”的字符串，比如<Note 2>。为了便于实际操作测试，示例程序中，所有的模型类都重新定义了\_repr\_()方法，返回一些更有用的信息，比如:

```
class Note(db.Model):
	...
    def __repr__(self):
        return '<Note %r>' % self.body
```

在实际开发中，这并不是必须的。另外，为了节省篇幅，后面的模型类定义不会给出这部分代码，具体可到源码仓库中查看。

#### create

添加一条新记录到数据库主要分为三步:
1）创建Python对象（实例化模型类)作为一条记录。
2）添加新创建的记录到数据库会话。
3）提交数据库会话。
下面的示例向数据库中添加了三条留言:

```
(helloflask) D:\helloflask\demos\mydatabasedemo>python
Python 3.8.5 (tags/v3.8.5:580fbb0, Jul 20 2020, 15:43:08) [MSC v.1926 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> from app import db
>>> from app import Note
>>> note1=Note(body='Chyuhung')
>>> note2=Note(body='HaHa,This is a test message')
>>> db.session.add(note1)
>>> db.session.add(note2)
>>> db.session.commit()
>>>

```

在这个示例中，首先从app模块导入db对象和Note类，然后分别创建两个Note实例表示两条记录，使用关键字参数传入字段数据。Note类继承自db.Model基类，db.Model基类会为Note类提供一个构造函数，接收匹配类属性名称的参数值，并赋值给对应的类属性，所以不需要在Note类中定义构造方法。接着调用add()方法把这两个Note对象添加到会话对象db.session中，最后调用commit()方法提交会话。

除了依次调用add()方法添加多个记录，也可以使用add_all()一次添加包含所有记录对象的列表。
可能注意到了，在创建模型类实例的时候并没有定义id字段的数据，这是因为主键由SQLAlchemy管理。模型类对象创建后作为临时对象(transient)，当提交数据库会话后，模型类对象才会转换为数据库记录写入数据库中，这时模型类对象会自动获得id值:

```
>>> note1.id
1
>>> note2.id
2
>>>
```

Flask-SQLAlchemy提供了一个SQLALCHEMY_COMMIT_ON_TEARDOWN配置变量，将其设为True可以设置自动调用commit()方法提交数据库会话。因为存在潜在的Bug，目前已不建议使用，而且未来版本中将移除该配置变量。请避免使用该配置变量，可使用手动调用db.session.commit()方法的方式提交数据库会话。

#### read

已经知道了如何向数据库里添加记录，那么如何从数据库里取回数据呢?使用模型类提供的query属性附加调用各种过滤方法及查询方法可以完成这个任务。
一般来说，一个完整的查询遵循下面的模式:

```
<模型类>.query.<过滤方法>.<查询方法>
```

从某个模型类出发，通过在query属性对应的Query对象上附加的过滤方法和查询函数对模型类对应的表中的记录进行各种筛选和调整，最终返回包含对应数据库记录数据的模型类实例，对返回的实例调用属性即可获取对应的字段数据。
如果执行了上面小节里的操作，数据库现在一共会有两条记录，如：

| ID   | BODY                        |
| ---- | --------------------------- |
| 1    | Chyuhung                    |
| 2    | HaHa,This is a test message |

SQLAlchemy提供了许多查询方法用来获取记录：

![image-20210409165150406](flask入门.assets/image-20210409165150406.png)

表中的first_or_404()、get_or_404()以及 paginate()方法是Flask-SQLAlchemy附加的查询方法。
下面是对Note类进行查询的几个示例。all()返回所有记录:

```
>>> Note.query.all()
[<Note 1>, <Note 2>, <Note 3>, <Note 4>]
>>>
```

```
>>> note1=Note.query.first()
>>> note1
<Note 1>
```

SQLAlchemy还提供了许多过滤方法，使用这些过滤方法可以获取更精确的查询，比如获取指定字段值的记录。对模型类的query属性存储的Query对象调用过滤方法将返回一个更精确的Query对象（后面简称为查询对象）。因为每个过滤方法都会返回新的查询对象，所以过滤器可以叠加使用。在查询对象上调用前面介绍的查询方法，即可获得一个包含过滤后的记录的列表。常用的查询过滤方法如：

![image-20210409171811837](flask入门.assets/image-20210409171811837.png)

filter()方法是最基础的查询方法。它使用指定的规则来过滤记录，下面的示例在数据库里找出了body字段值为“Chyuhung”的记录:

```
>>> Note.query.filter(Note.body=="Chyuhung").first()
<Note 1>
>>>
```

直接打印查询对象或将其转换为字符串可以查看对应的SQL语句:

```
>>> print(Note.query.filter(Note.body=="Chyuhung"))
SELECT note.id AS note_id, note.body AS note_body
FROM note
WHERE note.body = ?
>>>
```

在filter()方法中传入表达式时，除了“==”以及表示不等于的“!=”，其他常用的查询操作符以及使用示例如下所示:

```
filter(Note.body.like('%foo%'))
```

```
filter(Note.body.in_(['foo', 'bar', 'baz']))
```

```
 filter(~Note.body.in_(['foo', 'bar', 'baz']))
```

```
# 使用and_()
from sqlalchemy import and_
filter(and_(Note.body == 'foo', Note.title == 'FooBar'))

# 或在filter()中加入多个表达式，使用逗号分隔
filter(Note.body == 'foo', Note.title == 'FooBar')

# 或叠加调用多个filter()/filter_by()方法
filter(Note.body == 'foo').filter(Note.title == 'FooBar')
```

```
from sqlalchemy import or_filter(or_(Note.body == 'foo', Note.body == 'bar'))
```

和filter()方法相比，filter_by)方法更易于使用。在filter_by()方法中，可以使用关键字表达式来指定过滤规则。更方便的是，可以在这个过滤器中直接使用字段名称。下面的示例使用filter_by()过滤器完成了同样的任务:

```
>>> Note.query.filter_by(body='Chyuhung').first()
<Note 1>
>>>
```

#### update

更新一条记录非常简单，直接赋值给模型类的字段属性就可以改变字段值，然后调用commit()方法提交会话即可。下面的示例改变了一条记录的body字段的值:

```
>>> note=Note.query.get(2)
>>> note.body
'HaHa,This is a test message'
>>> note.body="XIXI,this is a test message!"
>>> db.session.commit()
>>> newnote=Note.query.get(2)
>>> newnote.body
'XIXI,this is a test message!'
>>>
```

只有要插入新的记录或要将现有的记录添加到会话中时才需要使用add()方法，单纯要更新现有的记录时只需要直接为属性赋新值，然后提交会话。

#### delete

删除记录和添加记录很相似，不过要把add()方法换成delete()方法，最后都需要调用commit()方法提交修改。下面的示例删除了id(主键)为2的记录:

```
>>> note=Note.query.get(2)
>>> note
<Note 2>
>>> db.session.delete(note)
>>> db.session.commit()
```

### 在视图函数里操作数据库

在视图函数里操作数据库的方式和在Python Shell中的练习大致相同，只不过需要一些额外的工作。比如把查询结果作为参数传入模板渲染出来，或是获取表单的字段值作为提交到数据库的数据。在这一节，将把上一节学习的所有数据库操作知识运用到一个简单的笔记程序中。这个程序可以让你创建、编辑和删除笔记，并在主页列出所有保存后的笔记。

#### create

为了支持输入笔记内容，先创建一个用于填写新笔记的表单，如下所示:

```
from flask_wtf import FlaskForm
from wtforms import TextAreaField,validators,SubmitField
from wtforms.validators import DataRequired

class NewNoteForm(FlaskForm):
    body=TextAreaField('Body',validators=[DataRequired()])
    submit=SubmitField('Save')
```

创建一个new_note视图，这个视图负责渲染创建笔记的模板，并处理表单的提交，如:

```
from forms import NewNoteForm
@app.route('/new_note',methods=['GET','POST'])
def new_note():
    form=NewNoteForm()
    if form.validate_on_submit():
        body=form.body.data
        note=Note(body=body)
        db.session.add(note)
        db.session.commit()
        flash("Your note saved successful!")
        return redirect(url_for('index'))
    return render_template('new_note.html',form=form)
```

先来看看form.validate_on_submit()返回True时的处理代码。当表单被提交且通过验证时，我们获取表单body字段的数据，然后创建新的Note实例，将表单中body字段的值作为body参数传入，最后添加到数据库会话中并提交会话。这个过程接收用户通过表单提交的数据并保存到数据库中，最后我们使用flash()函数发送提示消息并重定向到index视图。
表单在new_note.html模板中渲染:

```
<form method="POST">
    {{ form.csrf_token }}
    {{ form.body.label }}
    {{ form.body }}
    <br>
    {{ form.submit }}
</form>
```

index视图用来显示主页，目前它的所有作用就是渲染主页对应的模板:

```
@app.route('/index',methods=["GET"])
def index():
    return render_template('index.html')
```

在对应的index.html模板中，添加一个指向创建新笔记页面的链接:

```
<form method="GET">
    {% for message in get_flashed_messages() %}
    {{ message }}
    {% endfor %}
</form>
<h1>Note Book</h1>
<ul>
    <a href="{{ url_for('new_note')}}">New Note</a>
</ul>
```

#### read

在上一节为程序实现了添加新笔记的功能，当在创建笔记的页面单击保存后，程序会重定向到主页，提示的消息显示刚刚提交的笔记已经成功保存了，可是却无法看到创建后的笔记。为了在主页列出所有保存的笔记，需要修改index视图，修改后的index视图如：

```
@app.route('/index',methods=["GET"])
def index():
    form=NewNoteForm()
    notes=Note.query.all()
    return render_template('index.html',notes=notes,form=form)
```

在新的index视图里，像在Python Shell中一样使用Note.query.all()查询所有note记录，然后把这个包含所有记录的列表作为notes变量传入模板，在模板中将笔记们显示出来：

```
<form method="GET">
    {% for message in get_flashed_messages() %}
    {{ message }}
    {% endfor %}
</form>
<h1>Note Book</h1>
<ul>
    <a href="{{ url_for('new_note')}}">New Note</a>
</ul>
<ul>
    <h4>{{ notes|length }}note:</h4>
    {% for note in notes %}
    <p>{{ note.body }}</p>
    {% endfor %}
</ul>
```

在模板中，迭代这个notes列表，调用Note对象的body属性(note.body)获取body字段的值。另外，还通过length过滤器获取笔记的数量。渲染后的示例如：

![image-20210412165333849](flask入门.assets/image-20210412165333849.png)

#### update

更新一条笔记和创建一条新笔记的实现代码几乎完全相同，首先是编辑笔记的表单:

```
class EditNoteForm(FlaskForm):
    body=TextAreaField('Body',validators=[DataRequired])
    submit=SubmitField('Update')
```

会发现这和创建新笔记New Note Form唯一的不同就是提交字段的标签参数(作为\<input\>的value属性)，因此这个表单的定义也可以通过继承来简化:

```
class EditNoteForm(NewNoteForm):
	submit = SubmitField('Update')
```

用来渲染更新笔记页面和处理更新表单提交的edit_note视图如:

```
@app.route('/edit_note/<int:note_id>',methods=['GET','POST'])
def edit_note(note_id):
    form=EditNoteForm()
    note=Note.query.get(note_id)
    if form.validate_on_submit():
        note.body=form.body.data
        db.session.commit()
        flash("Edit successful!")
        return redirect(url_for("index"))
    form.body.data=note.body
    return render_template('edit_note.html',form=form)
```

这个视图通过URL变量note_id获取要被修改的笔记的主键值（id字段)，然后就可以使用get()方法获取对应的Note实例。当表单被提交且通过验证时，将表单中body字段的值赋给note对象的body属性，然后提交数据库会话，这样就完成了更新操作。和创建笔记相同，接着发送提示消息并重定向到index视图。
唯一需要注意的是，在GET请求的执行流程中，添加了下面这行代码:

```
form.body.data = note.body
```

因为要添加修改笔记内容的功能，那么当打开修改某个笔记的页面时，这个页面的表单中必然要包含笔记原有的内容。
如果手动创建HTML表单，那么可以通过将note记录传入模板，然后手动为对应字段中填入笔记的原有内容，比如:

```
<textarea name="body">{{ note.body }}</textarea>
```

其他input元素则通过value属性来设置输入框中的值，比如:

```
<input name="foo" type="text" value="{{ note.title }}">
```

使用WTForms可以省略这些步骤，当渲染表单字段时，如果表单字段的data属性不为空，WTForms会自动把data属性的值添加到表单字段的value属性中，作为表单的值填充进去，不用手动为value属性赋值。因此，将存储笔记原有内容的note.body属性赋值给表单body字段的data属性即可在页面上的表单中填入原有的内容。
模板的内容基本相同，这里不再赘述。最后的工作是在主页笔记列表中的每个笔记内容下添加一个编辑按钮，用来访问编辑页面:

```
<form method="GET">
    {% for message in get_flashed_messages() %}
    <div class="alert">
        {{ message }}
    </div>
    {% endfor %}
</form>
<h1>Note Book</h1>
<ul>
    <a href="{{ url_for('new_note')}}">New Note</a>
</ul>
<ul>
    <h4>{{ notes|length }}note:</h4>
    {% for note in notes %}
        <div class="note">
            <p>{{ note.body }}</p>
            <a href="{{ url_for("edit_note",note_id=note.id) }}">Edit</a>
        </div>
    {% endfor %}
</ul>
```

生成edit_note视图的URL时，我们传入当前note对象的id ( note.id)作为URL变量note_id的值。

#### delete

在程序中，删除的实现也非常简单，不过这里经常会有一个误区。大多数人通常会考虑在笔记内容下添加一个删除链接:

```
<a href="{{ url_for('delete_note', note_id=note.id) }}">Delete</a>
```

这个链接指向用来删除笔记的delete_note视图:

```
@app.route('/delete/<int:note_id>')
def delete_note(note_id):
    note = Note.query.get(note_id)
    db.session.delete(note)
    db.session.commit()
    flash('Your note is deleted.')
    return redirect(url_for('index'))
```

虽然这一切看起来都很合理，但这种处理方式实际上会使程序处于CSRF攻击的风险之中。曾强调过，防范CSRF攻击的基本原则就是正确使用GET和POST方法。像删除这类修改数据的操作绝对不能通过GET请求实现，正确的做法是为删除操作创建一个表单，如下所示:

```
class DeleteNoteForm(FlaskForm):
    submit=SubmitField('Delete')
```

这个表单类只有一个提交字段，因为只需要在页面上显示一个删除按钮来提交表单。删除表单的提交请求由delete_note视图处理，如:

```
from forms import DeleteNoteForm
from flask import abort
@app.route('/delete_note/<int:note_id>',methods=['POST'])
def delete_note(note_id):
    form=DeleteNoteForm()
    if form.validate_on_submit():
        note=Note.query.get(note_id)
        db.session.delete(note)
        db.session.commit()
        flash('Delete successful!')
    else:
        abort("400")
    return redirect(url_for("index"))
```

在delete_note视图的app.route()中,methods列表仅填入了POST，这会确保该视图仅监听POST请求。
和编辑笔记的视图类似，这个视图接收note_id（主键值）作为参数。如果提交表单且通过验证（唯一需要被验证的是CSRF令牌)，就使用get()方法查询对应的记录，然后调用db.session.delete()方法删除并提交数据库会话。如果验证出错则使用abort()函数返回400错误响应。
因为删除按钮要在主页的笔记内容下添加，需要在index视图中实例化DeleteNote-Form类，然后传入模板。在index.html模板中，渲染这个表单:

```
<form method="GET">
    {% for message in get_flashed_messages() %}
    <div class="alert">
        {{ message }}
    </div>
    {% endfor %}
</form>
<h1>Note Book</h1>
<ul>
    <a href="{{ url_for('new_note')}}">New Note</a>
</ul>
<ul>
    <h4>{{ notes|length }}note:</h4>
    {% for note in notes %}
        <div class="note">
            <p>{{ note.body }}</p>
            <a href="{{ url_for("edit_note",note_id=note.id) }}">Edit</a>
            <form method="post" action="{{ url_for("delete_note",note_id=note.id) }}">
                {{ form.csrf_token }}
                {{ form.submit }}
            </form>
        </div>
    {% endfor %}
</ul>
```

将表单的action属性设置为删除当前笔记的URL。构建URL时，URL变量note_id的值通过note.id属性获取，当单击提交按钮时，会将请求发送到action属性中的URL。添加删除表单的主要目的就是防止CSRF攻击，所以不要忘记渲染CSRF令牌字段form.csrf_token。

在HTML中，\<a\>标签会显示为链接，而提交按钮会显示为按钮，为了让编辑和删除笔记的按钮显示相同的样式，为这两个元素使用了同一个CSS类".btn”,具体可以在static/style.css文件中查看。作为替代，可以考虑使用Java Script创建监听函数，当删除按钮按下时，提交对应的隐藏表单。

最终简单的运行效果：

![image-20210415091448367](flask入门.assets/image-20210415091448367.png)

### 定义关系

在关系型数据库中，可以通过关系让不同表之间的字段建立联系。一般来说，定义关系需要两步，分别是创建外键和定义关系属性。在更复杂的多对多关系中，我们还需要定义关联表来管理关系。这一节学习如何使用SQLAlchemy在模型之间建立几种基础的关系模式。

### 配置python shell上下文

在上面的许多操作中，每一次使用flask shell命令启动Python Shell后都要从app模块里导入db对象和相应的模型类。为什么不把它们自动集成到Python Shell上下文里呢?就像Flask内置的app对象一样。这当然可以实现!可以使用app.shell_context_processor装饰器注册一个shell上下文处理函数。它和模板上下文处理函数一样，也需要返回包含变量和变量值的字典，如：

```
# ...
@app.shell_context_processor
def make_shell_context():
	return dict(db=db, Note=Note) # 等同于{'db': db, 'Note': Note}
```

当使用flask shell命令启动Python Shel时，所有使用app.shell_context_processor装饰器注册的shell上下文处理函数都会被自动执行，这会将db和Note对象推送到Python Shell上下文里:

```
(helloflask) D:\helloflask\demos\mydatabasedemo>flask shell
>>> db
<SQLAlchemy engine=sqlite:///D:\helloflask\demos\mydatabasedemo\data.db>
>>> Note
<class 'app.Note'>
```

在这一节演示各种数据库关系时，将编写更多的模型类。在示例程序中，它们都使用shell上下文处理函数添加到shell上下文中，因此可以直接在Python Shell使用，不用手动导入。

### 一对多

将以作者和文章来演示一对多关系:一个作者可以写作多篇文章。一对多关系：

![image-20210415095343393](flask入门.assets/image-20210415095343393.png)

在示例程序中，Author类用来表示作者，Article类用来表示文章，代码如：

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import os

app=Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI']=os.getenv("DATABASE_URL","sqlite:///"+os.path.join(app.root_path,"data.db"))
app.secret_key="chyuhung"
db=SQLAlchemy(app)

class Author(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    name=db.Column(db.String(70),unique=True)
    phone=db.Column(db.String(20))

class Article(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    title=db.Column(db.String(50),index=True)
    body=db.Column(db.Text)
```

将在这两个模型之间建立一个简单的一对多关系，建立这个一对多关系的目的是在表示作者的Author类中添加一个关系属性articles，作为集合(collection）属性，当对特定的Author对象调用articles属性会返回所有相关的Article对象。

### 定义外键

定义关系的第一步是创建外键。外键是(foreign key)用来在A表存储B表的主键值以便和B表建立联系的关系字段。因为外键只能存储单一数据（标量)，所以外键总是在“多”这一侧定义，多篇文章属于同一个作者，所以需要为每篇文章添加外键存储作者的主键值以指向对应的作者。在Article模型中，定义一个author_id字段作为外键:

```
class Article(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    title=db.Column(db.String(50),index=True)
    body=db.Column(db.Text)
    author_id=db.Column(db.Integer,db.ForeignKey('author.id'))
```

这个字段使用db.Foreign Key类定义为外键，传入关系另一侧的表名和主键字段名，即author.id。实际的效果是将article表的author_id的值限制为author表的id列的值。它将用来存储author表中记录的主键值，如：

![image-20210415112321237](flask入门.assets/image-20210415112321237.png)

外键字段的命名没有限制，因为要连接的目标字段是author表的id列，所以为了便于区分而将这个外键字段的名称命名为author_id。

传入Foreign Key类的参数author.id，其中author指的是Author模型对应的表名称，而id指的是字段名，即“表名.字段名”。**模型类对应的表名由Flask-SQLlchemy生成，默认为类名称的小写形式，多个单词通过下划线分隔，你也可以显式地通过\_tablename\_属性自己指定，后面不再提示。**

相关参考文献：https://www.crifan.com/flask_sqlalchemy_database_sqlalchemy_exc_noreferencedtableerror_foreign_key_associated_with_column_events_user_openid_could_not_find_table/

### 定义关系属性

定义关系的第二步是使用关系函数定义关系属性。关系属性在关系的出发侧定义，即一对多关系的“一”这一侧。一个作者拥有多篇文章，在Author模型中，我们定义了一个articles属性来表示对应的多篇文章:

```
class Author(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    name=db.Column(db.String(70),unique=True)
    phone=db.Column(db.String(20))
    articles=db.relationship("Article")
```

关系属性的名称没有限制，可以自由修改。它相当于一个快捷查询，不会作为字段写入数据库中。
这个属性并没有使用Column类声明为列，而是使用了db.relationship()关系函数定义为关系属性，因为这个关系属性返回多个记录，我们称之为集合关系属性。relationship()函数的第一个参数为关系另一侧的模型名称，它会告诉SQLAlchemy将Author类与Article类建立关系。当这个关系属性被调用时，SQLAIchemy会找到关系另一侧(即article表)的外键字段（即author_id)，然后反向查询article表中所有author_id值为当前表主键值(即author.id)的记录，返回包含这些记录的列表，也就是返回某个作者对应的多篇文章记录。

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import os
import click

app=Flask(__name__)

# #Flask-SQLAlchemy建议设置SQLALCHEMY_TRACK_MODIFICATIONS配置变量，
# #这个配置变量决定是否追踪对象的修改，这用于Flask-SQLAlchemy的事件通知系统。
# #这个配置键的默认值为None，如果没有特殊需要，可以把它设为False来关闭警告信息。
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
app.config['SQLALCHEMY_DATABASE_URI']=os.getenv("DATABASE_URL","sqlite:///"+os.path.join(app.root_path,"data.db"))
app.secret_key="chyuhung"

db=SQLAlchemy(app)

# #使用app.shell_context_processor装饰器注册一个shell上下文处理函数
@app.shell_context_processor
def make_shell_context():
    return dict(db=db,Author=Author,Article=Article)

# #初始化数据库
# #实现一个自定义flask命令完成数据库初始化
@app.cli.command()
def initdb():
    db.create_all()
    click.echo("Initialized Database.")

class Author(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    name=db.Column(db.String(70),unique=True)
    phone=db.Column(db.String(20))
    articles=db.relationship("Article")

class Article(db.Model):
    id=db.Column(db.Integer,primary_key=True)
    title=db.Column(db.String(50),index=True)
    body=db.Column(db.Text)
    author_id=db.Column(db.Integer,db.ForeignKey('author.id'))
```

使用自定义flask命令完成数据库初始化，创建数据库：

```
(helloflask) D:\helloflask\demos\newdatabase>flask initdb
Initialized Database.

(helloflask) D:\helloflask\demos\newdatabase>
```

下面会在Python Shell中演示如何对实际的对象建立关系。先创建一个作者记录和两个文章记录，并添加到数据库会话中:

```
(helloflask) D:\helloflask\demos\newdatabase>flask shell
>>> db
<SQLAlchemy engine=sqlite:///D:\helloflask\demos\newdatabase\data.db>
>>> foo = Author(name='Foo')
>>> spam = Article(title='Spam')
>>> ham = Article(title='Ham')
>>> db.session.add(foo)
>>> db.session.add(spam)
>>> db.session.add(ham)
```

### 建立关系

建立关系有两种方式，第一种方式是为外键字段赋值，比如:

```
>>> spam.author_id = 1
>>> db.session.commit()
```

将spam对象的author_id字段的值设为1，这会和id值为1的Author对象建立关系。提交数据库改动后，如果对id为1的foo对象调用articles关系属性，会看到spam对象包括在返回的Article对象列表中:

```
>>> foo.articles
[<Article 1>]
>>> ham.author_id=1
>>> db.session.commit()
>>> foo.articles
[<Article 1>, <Article 2>]
```

另一种方式是通过操作关系属性，将关系属性赋给实际的对象即可建立关系。集合关系属性可以像列表一样操作，调用append()方法来与一个Article对象建立关系:

```
>>> foo.articles.append(spam)
>>> foo.articles.append(ham)
>>> db.session.commit()
```

也可以直接将关系属性赋值给一个包含Article对象的列表。
和前面的第一种方式类似，为了让改动生效，需要调用db.session.commit()方法提交数据库会话。建立关系后，存储外键的author_id字段会自动获得正确的值，而调用Author实例的关系属性articles时，会获得所有建立关系的Article对象:

```
>>> ham.author_id
1
>>> foo.articles
[<Article 1>, <Article 2>]
```

和主键类似，外键字段由SQLAlchemy管理，不需要手动设置。
当通过关系属性建立关系后，外键字段会自动获得正确的值。
在后面的示例程序中，会统一使用第二种方式，即通过关系属性来建立关系。和append()相对，对关系属性调用remove()方法可以与对应的Aritcle对象解除关系:

```
>>> foo.articles.remove(ham)
>>> db.session.commit()
>>> foo.articles
[<Article 1>]
```

也可以使用pop()方法操作关系属性，它会与关系属性对应的列表的最后一个Aritcle对象解除关系并返回该对象。
不要忘记在操作结束后需要调用commit()方法提交数据库会话，这样才可以把改动写入数据库。

使用关系函数定义的属性不是数据库字段，而是类似于特定的查询函数。当某个Aritcle对象被删除时，在对应Author对象的aritcles属性调用时返回的列表也不会包含该对象。
在关系函数中，有很多参数可以用来设置调用关系属性进行查询时的具体行为。常用的关系函数参数如:

![image-20210415175911808](flask入门.assets/image-20210415175911808.png)

当关系属性被调用时，关系函数会加载相应的记录，下表出了控制关系记录加载方式的lazy参数的常用选项：

![image-20210416113139065](flask入门.assets/image-20210416113139065.png)

dynamic选项仅用于集合关系属性，不可用于多对一、一对一或是在关系函数中将uselist参数设为False的情况。
许多教程和示例使用dynamic来动态加载所有集合关系属性对应的记录，这是应该避免的行为。使用dynamic加载方式意味着每次操作关系都会执行一次SQL查询，这会造成潜在的性能问题。大多数情况下只需要使用默认值(select)，只有在调用关系属性会返回大量记录，并且总是需要对关系属性返回的结果附加额外的查询时才需要使用动态加载(lazy='dynamic') 。

### 建立双向关系

在Author类中定义了集合关系属性articles，用来获取某个作者拥有的多篇文章记录。在某些情况下，也许希望能在Article类中定义一个类似的author关系属性，当被调用时返回对应的作者记录，这类返回单个值的关系属性被称为标量关系属性。而这种两侧都添加关系属性获取对方记录的关系我们称之为双向关系(bidirectional relationship)。
双向关系并不是必须的，但在某些情况下会非常方便。双向关系的建立很简单，通过在关系的另一侧也创建一个relationship()函数，我们就可以在两个表之间建立双向关系。我们使用作家(Writer)和书（Book)的一对多关系来进行演示，建立双向关系后的Writer和Book类如：