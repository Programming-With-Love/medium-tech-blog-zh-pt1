# 揭开 Python 日志记录的神秘面纱

> 原文：<https://medium.com/globant/demystifying-python-logging-ba015ece202e?source=collection_archive---------0----------------------->

最重要的方面之一，同时也是我在 python 中发现的最弱的方面:处理日志。对我来说，看到程序充满了本质上服务于两个目的的印记似乎很奇怪:调试和作为执行中的进程或程序的出口。在一个项目中，出现了如何以有序和标准的方式处理日志的问题，当然，经过一些研究，我找到了一种方法来实现我想要的结果:通过控制台输出，同时写入磁盘，在一个我喜欢的程序输出文件中:仅使用 [Python](https://python.org/) [日志](https://docs.python.org/2/library/logging.html)库就足以实现我们代码结果的体面输出。

# 我们只使用了 2 个 python 的标准库模块。

```
import time
import logging
```

# 基本配置

我们定义了基本配置，这是将用于写入磁盘的配置，请注意，我们使用了 asctime、levelname(日志级别)和消息本身。我们还配置了默认的“级别”和输出文件的名称(可以有相对或绝对路径)

```
logging.basicConfig(format='%(asctime)s %(levelname)-8s %    (message)s',datefmt='%m/%d/%Y %I:%M:%S',
                  filename='output.log',
                  level=logging.DEBUG)
```

# 安慰

现在是控制台的配置:我们使用 StreamHandler()和默认的 INFO 级别来代替 basicConfig。使用完全相同的输出格式，也许你想使用不同的输出格式。您可以在文档中更好地看到[日志](https://docs.python.org/2/library/logging.html)是如何工作的。

然后，我们将处理程序添加到日志记录中，以便两者同时发生。即写入磁盘并显示在控制台中。

```
console = logging.StreamHandler()
console.setLevel(logging.INFO)formatter = logging.Formatter(
    '%(asctime)s %(levelname)-8s: %(message)s')console.setFormatter(formatter)logging.getLogger('').addHandler(console)
```

# 例子

我将展示一些使用示例，注意，我们必须手动设置我们想要显示的日志级别，也就是说，库本身不能确定它是一个错误、信息还是什么。我们必须根据代码块的可能行为自己完成，注意:

```
a = 'a string'
try:
    b = a + 1
except TypeError as e:
    logging.error('OH! There has been an error: {}'.format(e))
```

# 啊！产量呢？

最后，这是我们新配置的日志的输出。

```
05/17/2018 10:41:44 INFO     Inicio de mi programa
05/17/2018 10:41:46 WARNING  Una advertencia
05/17/2018 10:41:46 ERROR    OH! Ha ocurrido un error: Can't convert 'int' object to str implicitly
05/17/2018 10:41:46 CRITICAL Huston tenemos un problema! No podemos sumar strings y enteros
05/17/2018 10:41:46 INFO     Fin de la diversion (por ahora)
05/17/2018 10:42:56 INFO     Inicio de mi programa
05/17/2018 10:42:58 WARNING  Una advertencia
05/17/2018 10:42:58 ERROR    OH! Ha ocurrido un error: Can't convert 'int' object to str implicitly
05/17/2018 10:42:58 CRITICAL Huston tenemos un problema! No podemos sumar strings y enteros
05/17/2018 10:43:00 INFO     Fin de la diversion (por ahora)
```

# 代码

这是完整的代码:

```
# Don't forget to import your modules
import time
import logging
# log basic conf
logging.basicConfig(format='%(asctime)s %(levelname)-8s %(message)s',
                    datefmt='%m/%d/%Y %I:%M:%S',
                    filename='output.log',
                    level=logging.DEBUG)
# Handler which writes INFO messages or higher to the sys.stderr
console = logging.StreamHandler()
console.setLevel(logging.INFO)
# set a format which is simpler for console use
formatter = logging.Formatter(
    '%(asctime)s %(levelname)-8s: %(message)s')
# tell the handler to use this format
console.setFormatter(formatter)
# add the handler to the root logger
logging.getLogger('').addHandler(console)# lets play
logging.info('Starting the program')
# time delay to see better how time logging works
time.sleep(2)
# lets test a warning
# Notice: we get to handle the severity of the logs
logging.warn('Showing a warning')a = 'a string'
try:
    b = a + 1
except TypeError as e:
    # we can combine our own text with the system output
    logging.error('OH! There has been an error: {}'.format(e))# keep testing this
if isinstance(a, str):
    logging.fatal(
        'Huston we\'ve got a problem cannot sum strings to integers')time.sleep(2)
# end of the fun
logging.info('End of the fun (by now)')
```

# 结论

正如我在上面展示的 python 的日志库或模块，几行代码和一些使用规则可以改善你的脚本或程序。给你的代码输出一个专业的外观，同时将数据发送到屏幕或文件是非常容易的，就像每一个像样的软件所做的那样。从现在开始，停止使用 print 语句来完成这个任务，因为这不是它的主要目的，并利用日志记录的力量！