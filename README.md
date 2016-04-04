# async_redis
Greenlet-based Redis client for Tornado


Features
=====

- Support basic redis command and pipeline
- Pubsub and sentinel are NOT supported yet
- ONLY Tornado 3.x is supported currently


Dependencies
=====

- redis-py
- Greenlet
- Motor 0.3.2 exactly


Usage
=====

Getting Started

```python

from tornado import gen
from tornado.ioloop import IOLoop
from async_redis import AsyncStrictRedis

r = AsyncStrictRedis()

@gen.coroutine
def set_get():
    yield r.set('foo', 'bar')
    val = yield r.get('foo')
    print val

ioloop = IOLoop.current()
ioloop.run_sync(set_get)
```

Pipeline

```python

from tornado import gen
from tornado.ioloop import IOLoop
from async_redis import AsyncStrictRedis

r = AsyncStrictRedis()

@gen.coroutine
def pipeline():
        pipe = r.pipeline()
        pipe.set('foo', '123')
        pipe.set('bar', '456')
        pipe.mget(('foo', 'bar'))
        res = yield pipe.execute()

        pipe = r.pipeline(transaction=True)
        pipe.set('foo', '123')
        pipe.set('bar', '456')
        pipe.mget(('foo', 'bar'))
        res = yield pipe.execute()

ioloop = IOLoop.current()
ioloop.run_sync(pipeline)
```

Watch/MULTI/EXEC

```python

from tornado import gen
from tornado.ioloop import IOLoop
from async_redis import AsyncStrictRedis

r = AsyncStrictRedis()

@gen.coroutine
def watch():
        yield r.set('a', 0)
        with r.pipeline(transaction=False) as pipe:
            yield pipe.watch('a')
            a = yield pipe.get('a')

            pipe.multi()
            pipe.set('a', int(a) + 1)
            res = yield pipe.execute()

ioloop = IOLoop.current()
ioloop.run_sync(watch)
```


Contributing
============

Fork, patch, test, and send a pull request.


License
=======

[MIT](http://opensource.org/licenses/MIT) ©