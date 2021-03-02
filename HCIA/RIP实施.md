默认情况下RIPv1与RIPv2不兼容，在*接口视图*下可以调整接口运行RIPv1，使*协议视图*下运行RIPv2时设备接口能够正常收发RIPv1的路由

示例：修改接口下的RIP版本

```
[R1-GigabitEthernet0/0/0]dis this		#在接口视图下运行RIPv1
 ip address 10.0.123.1 255.255.255.0 
 rip version 1
[R1-rip-1]dis this						#在协议视图下运行RIPv2
rip 1
 undo summary
 version 2
 network 10.0.0.0

[R1]dis rip 1 inte g0/0/0 ver
 GigabitEthernet0/0/0(10.0.123.1)
  State           : UP          MTU    : 500
  Metricin        : 0       
  Metricout       : 1       
  Input           : Enabled     Output : Enabled     
  Protocol        : RIPv1
  Send version    : RIPv1 Packets					#发送RIPv1报文
  Receive version : RIPv1 Packets					#接收RIPv1报文
  Poison-reverse                : Disabled 
  Split-Horizon                 : Enabled
  Authentication type           : None 
  Replay Protection             : Disabled 
```

示例：接口视图下调整RIP参数

```
[R2-GigabitEthernet0/0/0]rip metricin 10			#所有从此接口收到的RIPv2路由的度量值+10
[R2-GigabitEthernet0/0/0]rip metricout 11			#所有从此接口更新出去的RIPv2路由的度量值+10
[R4-Serial2/0/0]undo rip output 					#禁止发送RIP报文，常用于与主机相连
[R1-Serial2/0/0]undo rip input						#禁止接收RIP报文
```

**静默端口**的优先级大于`input`和`output`，静默端口在*协议视图*下配置，用于指定设备接口配置为抑制状态，只接收RIP报文，更新自己的路由表，不发送RIP报文

```
[R1-rip-1]silent-interface s2/0/0					#设置s2/0/0接口为静默端口
```

场景：关闭RIP的组播更新，采用单播更新（peer）

```
[R4-rip-1]silent-interface all						#先把所有接口全部静默，然后单播指定邻居
[R4-rip-1]peer 10.0.14.1
```

没有指定静默端口时指令邻居，会同时发送组播和单播报文