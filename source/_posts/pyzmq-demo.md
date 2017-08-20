title: pyzmq 问答模式，超时重试，多线程
date: 2016-03-05 23:20:18
tags:
- python
- zmq
- pyzmq
categories:
- python
---
最近做的东西，用到了zmq的问答模式，使用起来跟普通的socket十分类似。下面从一个基本的例子，扩展到超时判断，再扩展到多线程处理。

<!-- more -->
# 基本的例子
下面的程序看起来就和普通socket一样，但zmq做了很多，例如消息不丢失，无论先启动server还是client，都是可以的，普通socket是做不到的。还有保持长链接，失败重试，很多很多.

最大的不同，在REP与REQ模式下，server和client天然是1对多的，在普通的socket中，服务器要能应对多个client，最简单也要在循环中，对每一个请求开一个线程。

## server
```python
import zmq

context = zmq.Context()
socket = context.socket(zmq.REP)
socket.bind('tcp://*:6666')

while True:
    msg = socket.recv_string()
    print(msg)
    socket.send_string('world')

socket.close()
context.term()
```
## client
```python
import zmq

context = zmq.Context()
socket = context.socket(zmq.REQ)
socket.connect("tcp://localhost:6666")

socket.send_string('hello')
ret = socket.recv()
print(ret)

socket.close()
context.term()
```

## 其他协议

除了tcp协议，还包括ipc协议和inproc协议，ipc在同一个物理机器上，可以实现进程通信，inproc是在内存中。

# 超时判断

client在send之后，recv接受服务器端回发的信息，如果服务器崩溃，没有返回，则client会一直阻塞着，即使server重启也没法响应，如果希望client等待有超时重连机制，下面这个例子稍加改造就能使用了。

## server
```python
import zmq
import time

context = zmq.Context()
socket = context.socket(zmq.REP)
socket.bind('tcp://*:5555')
s = 0
while True:
    msg = socket.recv_string()
    s = (s + 1) % 5
    time.sleep(s)
    print(msg)
    socket.send_string('world')

socket.close()
context.term()
```
和刚才基本一样，不过server每次收到信息，就会挂起5秒。

## client
希望能够client发出信息后，3秒不返回，就不等待了。
```python
import zmq
import time

context = zmq.Context()
socket = context.socket(zmq.REQ)
socket.connect("tcp://localhost:5555")

poll = zmq.Poller()
poll.register(socket, zmq.POLLIN)

while True:
    socket.send_string('hello')
    while True:
        socks = dict(poll.poll(3000))
        if socks.get(socket) == zmq.POLLIN:
            ret = socket.recv()
            print(ret)
            break
        else:
            socket.setsockopt(zmq.LINGER, 0)
            socket.close()
            poll.unregister(socket)
            socket = context.socket(zmq.REQ)
            socket.connect("tcp://localhost:5555")
            poll.register(socket, zmq.POLLIN)
            socket.send_string('hello')
            print('confused')

socket.close()
context.term():
```

# 多线程

上面的例子，因为服务执行过程缓慢，所以链接的客户端都阻塞在那里了。如果希望服务执行的代码是多线程并行的，可以借助zmq的路由和device机制。

## server
```python
import zmq
import time
import threading

url_worker = 'inproc://ping-workers'
url_router = 'ipc://server.ipc' #这次以ipc协议为例，tcp也可以呦
worker_num = 10

context = zmq.Context()

router = context.socket(zmq.XREP) #或者是zmq.ROUTER,他们的值都是6
router.bind(url_router)

workers = context.socket(zmq.XREQ) #或者是zmq.DEALER,他们的值都是5
workers.bind(url_worker)

def worker(name, url_worker, context):
    print('worker {0} start'.format(name))
    worker = context.socket(zmq.REP)
    worker.connect(url_worker)
    while True:
        try:
            message = worker.recv_string()
            time.sleep(3)
            print('worker {0} recv {1}'.format(name,message))
            worker.send_string('world from {0}'.format(name))
        except:
            print('worker {0} error'.format(name))
            break
    worker.close()

for i in range(worker_num):
    thread = threading.Thread(target=worker, args=(i, url_worker, context,))
    thread.start()

zmq.device(zmq.QUEUE, router, workers)

router.close()
workers.close()
context.term()
```
## client
```python
import zmq
import time

context = zmq.Context()
socket = context.socket(zmq.REQ)
socket.connect("ipc://server.ipc")

poll = zmq.Poller()
poll.register(socket, zmq.POLLIN)

while True:
    socket.send_string('hello')
    while True:
        socks = dict(poll.poll(4000))
        if socks.get(socket) == zmq.POLLIN:
            ret = socket.recv()
            print(ret)
            break
        else:
            socket.setsockopt(zmq.LINGER, 0)
            socket.close()
            poll.unregister(socket)
            socket = context.socket(zmq.REQ)
            socket.connect("ipc://server.ipc")
            poll.register(socket, zmq.POLLIN)
            socket.send_string('hello')
            print('confused')
```
多开几个client，感受一下！
