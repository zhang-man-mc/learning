

python 装饰器**



1. 装饰器的**特性**：


1. 能把被装饰的函数替换成另外一个函数
2. 装饰器在加载模块时立即运行
3. 在被装饰的函数定义之后立即运行

函数 装饰器在被导入时立即运行，而被装饰的函数只在明确调用时运行(在main明确调用时)

2. 装饰器**真实的使用场景**：

      1. 装饰器通常在一个模块中定义，然后应用到其他模块中的函数、
      2.  大多数装饰器会在内部定义一个函数，然后将其返回【`依赖闭包实现`】
      3.  用类来实现装饰器，而不是函数
   4.  如果原封不动的返回被装饰的函数，它的使用场景举例：
            1. python web框架把函数添加到中央注册处（把URL模式映射到生成HTTP响应的函数上的注册处，这种注册装饰器可能会也可能不会修改被装饰的函数）
            2. 把被装饰的函数名字添加到特定的列表中（判断折扣最大的列表也有函数名称，在任意模块中定义，只要使用了@装饰器即可）

3.  装饰器的典型行为：

    1.  把被装饰的函数替换成另外一个函数
    2.  二者接受相同的参数，而且返回被装饰函数本该返回的值，同时做一些额外操作

4. **闭包**：

   def  f():

   ​	free_v = 12

   ​	def g():

   ​		nonlocal free_v

   ​		free_v = 14

   ​		....

   ​	return g()

   1. 返回的 内部函数+外部的自由变量 =  闭包
   2. 当外部的自由变量free_v是不可变的对象，例如整型，字符串，元组时，对其修改值时，要在内部函数用nonlocal将变量声明为自由变量。

5.  **参数化装饰器**：

    ​	装饰器函数无参数时，写法为2层

    ​	有参数时，写法为三层。这使有参数的装饰器一直难写，难读。后面用类来实现装饰器，代替函数写法

    ​				第一层把参数传递给装饰器函数。第二层为装饰器函数。第三层为包装函数

    1.  python会把被装饰函数作为第一个参数传递给装饰器函数。

    2.  那么，怎么让装饰器函数接受其他的参数尼？

    3.  答：创建装饰器工厂函数，把参数传递给他，然后再把它应用到要装饰的函数上。（3层）

        ​

6.  有参装饰器的可选参数（省去那对括号--利用仅限关键词参数）



7. 类实现有参装饰器

   1. 函数替换（有参装饰器）

      1. 有参装饰器起始就是2次的函数调用
      2. 用类和类实例的调用来代替2次的函数调用


~~~  py
class timer:
    """装饰器：打印函数耗时

    :param print_args: 是否打印方法名和参数，默认为 False
    """
	#第一次调用函数，传参数
    def __init__(self, print_args):
        self.print_args = print_args
	#第二次调用函数
    def __call__(self, func):
        @wraps(func)
        def decorated(*args, **kwargs):
            st = time.perf_counter()
            ret = func(*args, **kwargs)
            if self.print_args:
                print(f'"{func.__name__}", args: {args}, kwargs: {kwargs}')
            print('time cost: {} seconds'.format(time.perf_counter() - st))
            return ret

        return decorated
~~~

2. 实例替换（可以实现无参和有参装饰器）---用类实例来替换原始函数

   1. 无参装饰器

   ~~~   py
   class DelayedStart:
       """在执行被装饰函数前，等待 1 秒钟"""
       def __init__(self, func):
           update_wrapper(self, func) ➊
           self.func = func
       def __call__(self, *args, **kwargs): ➋
           print(f'Wait for 1 second before starting...')
           time.sleep（1）
           return self.func(*args, **kwargs)
       def eager_call(self, *args, **kwargs): ➌
           """跳过等待，立刻执行被装饰函数"""
           print('Call without delay')
           return self.func(*args, **kwargs)
   ~~~

   2. 有参装饰器

      ~~~ py
      class DelayedStart:
          """在执行被装饰函数前，等待一段时间
          :param func: 被装饰的函数
          :param duration: 需要等待的秒数
          """
          def __init__(self, func, *, duration=1): ➊
              update_wrapper(self, func)
              self.func = func
              self.duration = duration
          def __call__(self, *args, **kwargs):
              print(f'Wait for {self.duration} second before starting...')
              time.sleep(self.duration)
              return self.func(*args, **kwargs)
          def eager_call(self, *args, **kwargs): ...
      def delayed_start(**kwargs):
          """装饰器：推迟某个函数的执行"""
          return functools.partial(DelayedStart, **kwargs) ➋
          
      ~~~

      函数替换与实例替换的区别：

      1.函数替换本质与装饰器函数并无区别，虽然是基于类来实现的，单用来替换原函数的对象仍然是个普通的包装函数

      2.实例替换最终会用一个类实例来替换原函数

      3.写法区别：函数替换的__init__()无需传入原函数，而实例替换的__init__(需传入原函数

   用类实现装饰器的优点：

   1. 更方便地管理装饰器的内部状态，
   2. 更自然地为被装饰对象追加额外的方法和属性。

   ​