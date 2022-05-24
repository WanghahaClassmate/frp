<!-- 服务端 -->
[frp官方文档](https://gofrp.org/docs/)
tar -zxvf frp_0.33.0_linux_amd64.tar.gz
# 修改服务端配置文件
vi frps.ini
"""
[common]
# frp监听的端口，默认是7000，可以改成其他的
bind_port = 7000
# 授权码，请改成更复杂的
token = 52010  # 这个token之后在客户端会用到

# frp管理后台端口，请按自己需求更改
dashboard_port = 7500
# frp管理后台用户名和密码，请改成自己的
dashboard_user = admin
dashboard_pwd = admin
enable_prometheus = true

# frp日志配置
log_file = /var/log/frps.log
log_level = info
log_max_days = 3
"""
------------------------------------------------------------------------------------------------------

# 启动frp服务
sudo mkdir -p /etc/frp
sudo cp frps.ini /etc/frp
sudo cp frps /usr/bin
sudo cp systemd/frps.service /usr/lib/systemd/system/
sudo systemctl enable frps
sudo systemctl start frps
# 如果上述命令依次执行完没有任何错误出现，则说明启动成功。
------------------------------------------------------------------------------------------------------

# 防火墙开放端口

# 添加监听端口
sudo firewall-cmd --permanent --add-port=7000/tcp
# 添加管理后台端口
sudo firewall-cmd --permanent --add-port=7500/tcp
sudo firewall-cmd --reload
# a. 如果是ubuntu或者centos 6，请使用ufw/iptables工具放行端口；
# b. 7000和7500两个端口分别对应frps.ini配置中的bind_port和dashboard_port

# 如果出现监听防火墙出现问题 https://jingyan.baidu.com/article/5552ef47f509bd518ffbc933.html
# http://47.93.53.193:7500/ 访问服务端后台地址
------------------------------------------------------------------------------------------------------

<!-- 客户端 -->
# 修改客户端配置文件
vi frpc.ini
"""
# 客户端配置
[common]
server_addr = 服务器ip
server_port = 7000 # 与frps.ini的bind_port一致
token = 52010  # 与frps.ini的token一致

# 配置ssh服务
[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000  # 这个自定义，之后再ssh连接的时候要用

# 配置http服务，可用于小程序开发、远程调试等，如果没有可以不写下面的
[web]
type = http
local_ip = 127.0.0.1
local_port = 8080
subdomain = test.hijk.pw  # web域名
remote_port = 自定义的远程服务器端口，例如8080
"""
# 注意：[ssh]这样的名称必须全局唯一，即就算有多个客户端，也只能使用一次，其他的可以用[ssh2]、[ssh3]等；
# 意思就是说，如果你要配置多个客户端，必须将另外的客户端的[ssh]改为[ssh2]、[ssh3]等，并且remote_port也要变，比如6002，6003等
------------------------------------------------------------------------------------------------------

# 防火墙监听
sudo firewall-cmd --permanent --add-port=6000/tcp
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
# https://blog.csdn.net/weixin_43922901/article/details/109261700


'''
scp -r frp_0.41.0.tar.gz  wanglei@124.71.47.74:/home/wanglei^C
'''