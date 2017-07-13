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