- ## 安装
  id:: 634f9558-0f09-4a69-8042-c315c83b16a4
  ```
  sudo pacman -S smartdns-china-list-git
  ```
- ## 配置
	- ### 编辑/etc/smartdns/smartdns.conf
	  ```
	  # （可选）引入额外的规则列表，用绝对路径
	  conf-file /etc/smartdns/accelerated-domains.china.smartdns.conf
	  conf-file /etc/smartdns/apple.china.smartdns.conf
	  conf-file /etc/smartdns/google.china.smartdns.conf
	  conf-file /etc/smartdns/ad.conf
	  
	  # 本地监听端口
	  bind [::]:53
	  
	  # 缓存大小
	  cache-size 4096
	  
	  # 重启时读取之前的缓存
	  cache-persist yes
	  
	  # 缓存文件存放位置
	  cache-file /var/cache/smartdns.cache
	  
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
	  
	  # 传统 UDP 协议（以阿里 DNS 为例）
	  #server 223.5.5.5
	  
	  # DNS Over TLS （以 CloudFlare DNS 为例）
	  #server-tls 1.0.0.1
	  
	  # DNS Over Https (以烧饼 DNS 为例)
	  #server-https https://doh.dns.sb/dns-query -group china -group example
	  
	  server 114.114.114.114 -group china -exclude-default-group
	  server-https https://223.5.5.5/dns-query -group china -exclude-default-group
	  server-tls dns.pub -group china -exclude-default-group
	  
	  server 8.8.8.8
	  server 1.1.1.1
	  server 74.82.42.42
	  server-https https://doh.dns.sb/dns-query 
	  ```
	-
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