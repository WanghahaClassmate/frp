## 此项目运行方式
* 服务端
```
bash frps.sh
```
* 客户端
```
bash frpc.sh
```

## 官方安装方式
[frp官方文档](https://gofrp.org/docs/)
# 服务端
* 解压文件
```
tar -zxvf frp_0.33.0_linux_amd64.tar.gz
```
* 修改服务端配置文件
```
vi frps.ini
```
* 添加内容
```
[common]
# frp监听的端口，默认是7000，可以改成其他的
bind_port = 7000
# 授权码，请改成更复杂的，这个token之后在客户端会用到
token = 52010

# frp管理后台端口，请按自己需求更改
dashboard_port = 7500
# frp管理后台用户名和密码，请改成自己的
dashboard_user = admin
dashboard_pwd = admin
enable_prometheus = true

# 服务端日志打印地址
log_file = /var/log/frps.log
log_level = info
log_max_days = 3
```
* 添加防火墙监听端口，7000是使用端口、7500是后台管理端口，没打开防火墙的不用理相关防火墙的操作
```
sudo firewall-cmd --permanent --add-port=7000/tcp
sudo firewall-cmd --permanent --add-port=7500/tcp
# 重启防火墙
sudo firewall-cmd --reload
```
"""
# 防火墙监听 https://blog.csdn.net/weixin_43922901/article/details/109261700
# a. 如果是ubuntu或者centos 6，请使用ufw/iptables工具放行端口；
# b. 7000和7500两个端口分别对应frps.ini配置中的bind_port和dashboard_port

# 如果出现监听防火墙出现问题 https://jingyan.baidu.com/article/5552ef47f509bd518ffbc933.html
# http://47.93.53.193:7500/ 访问服务端后台地址
"""
------------------------------------------------------------------------------------------------------

# 客户端

* 修改客户端配置文件
```
vi frpc.ini
```
* 客户端配置
```
[common]
server_addr = 服务器ip
# 与frps.ini的bind_port一致
server_port = 7000
# 与frps.ini的token一致
token = 52010

# 配置ssh服务
[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
# 这个自定义，之后再ssh连接的时候要用
remote_port = 6000

# 配置http服务，可用于小程序开发、远程调试等，如果没有可以不写下面的
[web]
type = http
local_ip = 127.0.0.1
local_port = 8080
# web域名,可以不填
subdomain = test.hijk.pw
remote_port = 自定义的远程服务器端口，例如8080
```
"""
# 注意：[ssh]这样的名称必须全局唯一，即就算有多个客户端，也只能使用一次，其他的可以用[ssh2]、[ssh3]等；
# 意思就是说，如果你要配置多个客户端，必须将另外的客户端的[ssh]改为[ssh2]、[ssh3]等，并且remote_port也要变，比如6002，6003等
"""
* 防火墙监听,同样，如果没有开启防火墙可以不用一下操作
```
sudo firewall-cmd --permanent --add-port=6000/tcp
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```
## 注意
这里服务端必须是具有指定的公网ip的，如果服务端部署在阿里云或者华为云上（其他的云平台不清楚），需要在安全组中对端口放行，具体度娘一下
eg: [华为云示例](https://blog.csdn.net/qq_26003101/article/details/113629449)
