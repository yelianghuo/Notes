# 函数参数
    位置参数：即类似于c++中一般的参数
    默认参数：def power(x, n=2):
    可变参数：def calc(*numbers)  可变参数在函数调用时自动组装为一个tuple
        Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去
    关键字参数：调用时指定参数的名称，且与函数声明时的参数名称一致。使用关键字参数允许函数调用时参数的顺序与声明时不一致，仅根据参数的指定进行赋值。
    关键字可变长参数：允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict
        eg：def person(name, age, **kw):
                print('name:', name, 'age:', age, 'other:', kw)
        也可以先组装出一个dict，然后，把该dict转换为关键字参数传进去：
            >>> extra = {'city': 'Beijing', 'job': 'Engineer'}
            >>> person('Jack', 24, **extra)
    命名关键字参数：
                
    参数定义的顺序必须是：位置参数、默认参数、可变参数、命名关键字参数和关键字参数。
    
    
# argparse模块学习 
    http://kuanghy.github.io/2016/06/30/python-argparse
    http://www.jianshu.com/p/fef2d215b91d
    
    
# 思考
    mpy.mdb可以改写成工厂模式
    
# gitlab上面一个项目使用另一个项目
    把另一个项目当做一个模块：：https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97
    
# git fetch git merge 代替 git pull
    https://www.oschina.net/translate/git-fetch-and-merge?print