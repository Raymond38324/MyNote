# socket编程
## TCP

### 收发单次数据
#### **服务端**

```python
import socket
host = '127.0.0.1'
port = 8080
socket_server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
socket_server.bind((host, port))# 绑定ip和端口
socket_server.listen()# 让一个socket能够被外部链接,并设置最大连接数
coon, addr = socket_server.accept()　# 接受外部链接，返回一个coon对象和地址

data = coon.recv(1024)　# 接受数据，如果没有收到数据，会阻塞在这
print(data)
coon.send(b'Hello client')　# 发送数据
coon.close()　# 关闭连接
socket_server.close()# 关闭socket
```
#### **客户端**
```python
import socket
host = '127.0.0.1'
port = 8080
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect((host,port))# 连接到ip和端口对应的socket对象
client.send(b'Hi server')
res_data = client.recv(1024)
print(res_data)
client.close()
```
### 收发多次数据

#### **服务端**
```python
import socket
host = '127.0.0.1'
port = 8080
socket_server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
socket_server.bind((host, port))# 绑定ip和端口
socket_server.listen()# 让一个socket能够被外部链接,并设置最大连接数
while True:
    coon, addr = socket_server.accept()　# 接受外部链接，返回一个coon对象和地址

    data = coon.recv(1024).decode('utf8')　# 接受数据，如果没有收到数据，会阻塞在这
    if data == 'exit':
    	coon.send(b'exit')
    	break
    print(data)
    send_data = input("输入要发送的信息:").strip().encode('utf8')
    coon.send(send_data)　# 发送数据
coon.close()　# 关闭连接
socket_server.close()# 关闭socket
```
#### 客户端
```python
import socket
host = '127.0.0.1'
port = 8080
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect((host,port))# 连接到ip和端口对应的socket对象
while True:
	send_data = input("输入要发送的信息:").strip().encode('utf8')
	client.send(send_data)
	res_data = client.recv(1024).decode('utf8')
	if res_data == "exit":
		client.send(b'exit')
		break
	print(res_data)
client.close()
```