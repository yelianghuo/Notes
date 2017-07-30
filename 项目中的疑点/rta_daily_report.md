
## 2017_06_19
    * pusher/elastic_serach_pusher.py中,` '{}_{}_{}'.format(。。。) `中的`_`用来
    连接三个主键不合适，因为type中会出现`_`
    
    
## mysql拉取的数据，然后再进行字符串处理的报错

* 问题: 从数据库中获取数据，然后对于某个特定字段进行判断，若存在就处理
```
    for line in open(join_data):
        obj = json.loads(line)
        if '@type' and 'last_second_domain' in obj.keys():
            if obj['@type'] in {'rta', 'b2t'} and (obj['last_second_domain'].find('appsflyer') > -1
                                                   or obj['last_second_domain'].find('onelink') > -1):
                obj['mode'] = obj['mode'] | 1 << 2
```
obj是从数据库中拉取的json数据(另一个进程获取的，存为json格式)，这里从数据库获取的数据`'last_second_domain': null `有这种情况，
导致在这里`obj 中会存在'last_second_domain': None` 这样的映射字段，因此报错`None object have not attribute .find()。
    