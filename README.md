python的缓存库(cacheout)



链接：


项目: https://github.com/dgilland/cacheout

文档地址: https://cacheout.readthedocs.io

PyPI(下载链接): https://pypi.python.org/pypi/cacheout/

TravisCI(下载链接): https://travis-ci.org/dgilland/cacheout








特性：

后端使用字典进行缓存

使用缓存管理轻松访问多个缓存对象

当使用模块级缓存对象，重构运行时的缓存设置

最大缓存大小限制

默认的缓存时间设置以及缓存项自定义存活时间

批量的设置、获取、删除操作

线程安全

多种缓存机制的实现：

FIFO(先进先出)

LIFO(后进先出)

LRU (最近最少使用机制)

MRU (最近最多使用机制)

LFU (最小频率使用机制)

RR (随机替换机制)





线路图：

 层级缓存(多层级缓存)

支持缓存事件监听

获取缓存对象时的常规表示方法

获取缓存对象不存在时的回调处理支持

统计缓存

要求：

Python >= 3.4

快速开始：

下载安装：

pip install cacheout
通过创建一个缓存对象来开始了解：

from cacheout import Cache
cache = Cache()
默认的缓存的大小为256，默认存活时间是关闭的，这些属性可以通过如下设置：

cache = Cache(maxsize=256, ttl=0, timer=time.time)  
# defaults
设置一个缓存可以通过cache.set():

cache.set(1, 'foobar')

获取缓存键的值通过：cache.get():

assert cache.get(1) == 'foobar'

为每个键值对设置存活过期时间：

cache.set(3, {'data': {}}, ttl=1)
assert cache.get(3) == {'data': {}}
time.sleep(1)
assert cache.get(3) is None
缓存函数的计算结果：

@cache.memoize()
def func(a, b):    
    pass
为缓存函数提供了键值对的存活时间：

@cache.memoize(ttl=5, typed=True)
def func(a, b):    
     pass
     # func(1, 2) has different cache key than func(1.0, 2.0), whereas,
# with "typed=False" (the default), they would have the same key
函数解除缓存：

@cache.memoize()
def func(a, b):    
    pass
func.uncached(1, 2)
复制机制：

assert cache.copy() == {1: 'foobar', 2: ('foo', 'bar', 'baz')}
删除缓存中的一个键值对：

cache.delete(1)
assert cache.get(1) is None
清除整个缓存：

cache.clear()
assert len(cache) == 0
为get、set、delete设置了批量方法：

cache.set_many({'a': 1, 'b': 2, 'c': 3})
assert cache.get_many(['a', 'b', 'c']) == {'a': 1, 'b': 2, 'c': 3}
cache.delete_many(['a', 'b', 'c'])
assert cache.count() == 0
重置已经初始化的缓存对象：

cache.configure(maxsize=1000, ttl=5 * 60)
通过cache.keys(), cache.values(), and cache.items()获取所有的键、值、以及键值对：

cache.set_many({'a': 1, 'b': 2, 'c': 3})
assert list(cache.keys()) == ['a', 'b', 'c']
assert list(cache.values()) == [1, 2, 3]
assert list(cache.items()) == [('a', 1), ('b', 2), ('c', 3)]
迭代整个缓存的键:

for key in cache:    
    print(key, cache.get(key))    
# 'a' 1
# 'b' 2
?# 'c' 3
检测键是否还存在于缓存中通过cache.has() and key in cache方法：

assert cache.has('a')
assert 'a' in cache
通过使用CacheManager来管理多个缓存对象：

from cacheout import CacheManager
cacheman = CacheManager({'a': {'maxsize': 100},                        
                         'b': {'maxsize': 200, 'ttl': 900},                        
                         'c': {})
cacheman['a'].set('key1', 'value1')
value = cacheman['a'].get('key')

cacheman['b'].set('key2', 'value2')
assert cacheman['b'].maxsize == 200
assert cacheman['b'].ttl == 900

cacheman['c'].set('key3', 'value3')

cacheman.clear_all()
for name, cache in cacheman:    
    assert name in cacheman    
    assert len(cache) == 0
更多的细节请查看整篇文档https://cacheout.readthedocs.io.


