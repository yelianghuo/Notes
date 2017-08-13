# 本文记录在实习过程中遇到的linux shell命令

## 
  
### find:
 
* find ./ -ctime -2/+2/2 : 找前2天内的所有数据/找2天之后的数据/好

* 应用场景:
    
    crontab定时任务时，定时的删除某段时间内的数据文件
          ```
          ansible ${host} -m cron -a "
          user='${user}'
          name='clean_rta_daily_report_data'
          minute='0'
          hour='16'
          job='find ${dest}/${output}/data ${dest}/${output}/timestamp_data -ctime -6 | xargs rm -rf'
          " -s
              
          ```    
          
###  windows下，在gitlab pull下来的shell脚本，然后通过securecrt rz到开发机上面运行，error：'syntax error near unexpected token'。       
  
#### 解决方案 :

* 大致就是windows下面记事本中编辑的文件会自动在换行的地方添加^M符号,在vim中的一般模式下：%s/^M//g
* 参考链接:`http://blog.csdn.net/xyp84/article/details/4435899`
  
#### 引发的思考，就上面的`%s/^M//g`,这些东西的含义，因此次找到vim中查找与替换
   * 参考链接 `http://harttle.com/2016/08/08/vim-search-in-file.html`
   *
     
 
###`` '' ""的区别

    ``等价于$():在执行一条命令时，会先将其中的 ``，或者是$() 中的语句当作命令执行一遍，再将结果加入到原命令中重新执行
    '',"":单引号将剥夺其中的所有字符的特殊含义，而双引号中的'$'（参数替换）和'`'（命令替换）是例外
    http://blog.csdn.net/fhm727/article/details/47129445
 
 
 
### shell中字符串的截取

    ${varible:n1:n2}:截取变量varible从n1开始长度为n2的子串
    ${varible##*string} 从左向右截取最后一个string后的字符串
    
    http://www.cnblogs.com/liuweijian/archive/2009/12/27/1633661.html
  
  
### wc命令

     Linux wc命令 用于统计指定文件中的字节数、字数、行数，并将统计结果显示输出。wc是(Word Count)的缩写，即统计单词数
 
 
### zcat命令

    zcat命令用于不真正解压缩文件，就能显示压缩包中文件的内容的场合。
     
     
### inode值
    
    用echo追加内容到某个文件inode值不会改变，但是用vim打开文件修改文件内容就会导致inode变化
#### 暂时的解释
  * vim打开某个文件的时候，会有个临时文件，临时文件的inode值就已经和源文件不一样，vim编辑完之后，最终保存文件的inode值和之前的文件、临时文件都不一样
  
  * <http://www.ruanyifeng.com/blog/2011/12/inode.html>
  
  
### '\n' with \r

* 参考：<http://www.jianshu.com/p/23804b0b03c8>

### free
查看当前机器的内存使用情况
* -m: 显示以兆字节为单位
* -b: 以字节来显示总内存

### cache buffer
* write bufffer 大量数据在往磁盘写的时候，不可能每次都进行文件写入到磁盘的操作需要有缓冲区把写入的文件缓存起来然后一次性系统调用写入磁盘。减轻系统的压力，加快写入速度。

* read cache 翻译为快取，即在读文件的时候可以去磁盘中搜索，但是这样耗时，因此弄一个暂存的地方记录之前查询过的结果，当该查询再次到来的时候，直接从cache里面拿。

物理内存和磁盘的区别？为什么数据先存到内存中，读取速度很快，为什么在磁盘上面就慢？
