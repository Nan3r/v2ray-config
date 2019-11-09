# v2ray-config
v2ray config

## 环境配置

- V2Ray 4.12.0 (Po) 20190112
- TOR

## 说明

1. 安装v2ray

   ```
   curl -Ls  https://install.direct/go.sh |bash
   ```

2. 安装tor

   ```
   apt-get install tor -y
   ```

3. 默认监听9050端口，不用修改，因为v2ray配置里面也是9050端口，当然tor也可以配置Exitnodes等。

4. 修改client.json中的vpsip，uuid以及server.json的uuid。

5. 由于是vmess协议，所有客户端与服务端要同步。

6. 如果想做透明代理，修改client.json其中的一个inbound为任意门协议，具体可以参加官网的配置。

## 透明代理

给一个我的透明代理配置，主要是tcp，UDP要再加一些其他的。

```
echo 1 > /proc/sys/net/ipv4/ip_forward
v2ray 透明代理(dns:8.8.8.8)：
iptables 配置(双网卡)：
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -t nat -A POSTROUTING  -o eth0 -j MASQUERADE
iptables -t nat -A PREROUTING -p udp --dport 53 -j DNAT --to-destination 8.8.8.8:53
iptables -t nat -N v2ray
iptables -t nat -A v2ray -d 10.10.0.0/16 -j RETURN
iptables -t nat -A v2ray -p tcp -j REDIRECT --to-ports 1081
iptables -t nat -A v2ray -p udp -j REDIRECT --to-ports 1081
iptables -t nat -A PREROUTING -p tcp -j V2RAY

单网卡：
iptables -t nat -N V2RAY
iptables -t nat -A V2RAY -d 192.168.0.0/16 -j RETURN
iptables -t nat -A V2RAY -p tcp -j REDIRECT --to-ports 1081
iptables -t nat -A PREROUTING -p tcp -j V2RAY

v2ray 设置：
"inboundDetour":[
{
	"domainOverride":["tls","http"],
	"port":1081,
	"protocol":"dokodemo-door",
	"settings":{
		"network":"tcp,udp",
		"timeout": 30,
		"followRedirect":true
	}
}
]
```

