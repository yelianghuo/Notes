杂文
=============

  ## getopt.getopt(args, options[, long_options])
  
   1. sys.argv
   2. argparse
   3. getopt
   
    def h_getopt():
    args, args_else = getopt.getopt(sys.argv[1:], 'cb:', ['mm', 'lala='])
    for key, value in args:
        print('key: ', key, 'value: ', value)
    print(args_else)
    
    ./justForSample -c -b 5 --mm --lala /home
    
    output:
      key:  -c value:  
      key:  -b value:  5
      key:  --mm value:  
      key:  --lala value:  /home
    
    
   
   http://hackerxu.com/2014/08/15/getopt.html
   
  * os.getcwd()在python中可获取当前路径
  
### chr(), ord()
   
    * chr() : 把数值转化为ascii码字符
    * ord() : 把ascii码字符转化为数值
    * ‘9’=57 'A'=65 '\0'=0
    
    
### python中打开文件，向里面写入数据

* 参考:https://stackoverflow.com/questions/7127075/what-exactly-the-pythons-file-flush-is-doing
``` 
这里会涉及到2种类型的缓冲区

1. 内部缓冲区
2. 操作系统缓冲区

  内部缓冲区被运行时的程序创建，目的是为了避免每次写操作都要系统调用来加速程序的执行。
替代的是，当你写操作时，会先到缓冲区，直到缓冲区满了，缓冲区里面的数据才会被系统调用写到
实际的文件。
  然而，由于存在系统缓冲区，上面的操作不意味着数据会被写到磁盘。而是从内部缓冲区拷贝奥系统缓冲区。
  如果在你写操作的时候，数据还在缓冲区中，当前电脑关机的话，数据是不会写入到磁盘保存的。 
So, in order to help with that you have the flush and fsync methods, on their respective objects.

The first, flush, will simply write out any data that lingers in a program buffer to the actual file. Typically this means that the data will be copied from the program buffer to the operating system buffer.

Specifically what this means is that if another process has that same file open for reading, it will be able to access the data you just flushed to the file. However, it does not necessarily mean it has been "permanently" stored on disk.

To do that, you need to call the os.fsync method which ensures all operating system buffers are synchronized with the storage devices they're for, in other words, that method will copy data from the operating system buffers to the disk.

Typically you don't need to bother with either method, but if you're in a scenario where paranoia about what actually ends up on disk is a good thing, you should make both calls as instructed.
```
