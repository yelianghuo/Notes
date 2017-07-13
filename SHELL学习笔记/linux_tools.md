# 本文记录在实习过程中遇到的linux shell命令
    * ps
    
    * crontab
    
    * ansible
    
  * find: 
      find ./ -ctime -2/+2/2 : 找前2天内的所有数据/找2天之后的数据/好
    应用场景:
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

        