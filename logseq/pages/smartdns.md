- ## 安装
  id:: 634f9558-0f09-4a69-8042-c315c83b16a4
  ```
  sudo pacman -S smartdns-china-list-git
  ```
- ## 配置
  collapsed:: true
	- ### 编辑/etc/smartdns/smartdns.conf
	  ```
	  # bind 监听 指定 IP/端口 的 UDP 查询请求
	  # bind-tcp 监听 指定 IP/端口 的 TCP 查询请求
	  bind [::]:53
	  
	  # 载入 ChinaList
	  conf-file /etc/smartdns/accelerated-domains.china.smartdns.conf
	  conf-file /etc/smartdns/apple.china.smartdns.conf
	  conf-file /etc/smartdns/google.china.smartdns.conf
	  
	  # 最大缓存域名个数：16384
	  cache-size 16384
	  # 强制启用缓存
	  cache-persist yes
	  # 缓存文件路径
	  cache-file /tmp/smartdns.cache
	  
	  # 日志级别：信息
	  log-level info
	  # 最大返回 IP 个数
	  max-reply-ip-num 16
	  # 预请求域名：缓存预热。加速解析速度，优化用户体验。
	  prefetch-domain yes
	  # 智能双栈：智能在 iPv6 和 iPv4 之间选择一个最好的进行连接。
	  # 在检测到 iPv6 连接质量不如 iPv4 时，阻断 AAAA 解析。防止操作系统优先使用 iPv6。
	  # 建议关闭，在 DNS 支持双栈，而客户端仅支持单栈时会发生故障。（别问我咋知道的，测出来的...）
	  dualstack-ip-selection no
	  # 测速模式：tcp ping 或 icmp ping
	  # SmartDNS 允许您指定多个 DNS 上游，并智能选择最快的进行查询服务。
	  speed-check-mode tcp:443,ping
	  
	  # 阿里上游服务器
	  server 223.5.5.5 -group china -exclude-default-group
	  server 223.6.6.6 -group china -exclude-default-group
	  server 2400:3200::1 -group china -exclude-default-group
	  server 2400:3200:baba::1 -group china -exclude-default-group
	  
	  # 腾讯上游服务器
	  server 119.29.29.29 -group china -exclude-default-group
	  server 2402:4e00:: -group china -exclude-default-group
	  server 2402:4e00:1:: -group china -exclude-default-group
	  
	  # 南京信风上游服务器
	  server 114.114.114.114 -group china -exclude-default-group
	  server 114.114.115.115 -group china -exclude-default-group
	  
	  # SB DNS
	  server 185.222.222.222
	  server 185.184.222.222
	  # Cloudflare DNS
	  server 1.1.1.1
	  server 1.0.0.1
	  
	  # IQDNS
	  server-https https://i.passcloud.xyz/dns-query
	  server-https https://a.passcloud.xyz/dns-query
	  server-https https://a.passcloud.xyz/hk
	  server-https https://a.passcloud.xyz/am
	  server-https https://a.passcloud.xyz/us
	  server-https https://a.passcloud.xyz/sz
	  
	  # 不知道为什么，这个地址只会返回 127.0.0.0。
	  # 手动指定 localhost 的 IP 为 ::1
	  address /localhost/[::1] 
	  ```
	- ### 阻止其他程序再次修改 /etc/resolv.conf （通常是在说 NetworkManager）
	  你可以通过创建并编辑 /etc/NetworkManager/conf.d/01-dns.conf 文件，并写入以下内容来实现。
	  ```
	  [main]
	  dns=none
	  ```
-
- ## 启动
  ```
  systemctl enable --now smartdns.service
  ```