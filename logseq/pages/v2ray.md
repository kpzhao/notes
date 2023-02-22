- ## 配置系统时区
  ```
  rm -f /etc/localtime
  cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
  ```
- ## 安装caddy
  按照[官网](https://caddyserver.com/docs/install)进行安装即可
  ```
  sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
  curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
  curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
  sudo apt update
  sudo apt install caddy
  ```
- ## 安装v2ray
  按照官方[安装脚本](https://github.com/v2fly/fhs-install-v2ray)安装
  ```
  curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
  curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh
  bash install-release.sh
  bash install-dat-release.sh
  systemctl enable v2ray
  ```
- ## 配置初始参数
  ```
  domainName="$1"
  port="2333"
  uuid="`uuidgen`"
  path="/`pwgen -A0 6 8 | xargs |sed 's/ /\//g'`"
  v2rayConfig="/usr/local/etc/v2ray/config.json"
  caddyconfig="/etc//etc/caddy/Caddyfile"
  ```
- ## 配置v2ray
  ```
  echo '
  {
      "log": {
          "access": "/var/log/v2ray/access.log",
          "error": "/var/log/v2ray/error.log",
          "loglevel": "warning"
      },
      "inbounds": [
          {
              "listen": "127.0.0.1",
              "port": 54321,
              "protocol": "vmess",
              "settings": {
                  "clients": [
                      {
                          "id": "b1c91d35-5b44-42e5-b792-53e075c88dd0",
                          "level": 1,
                          "alterId": 0,
                          "email": "samber@gmail.com"
                      }
                  ],
                  "disableInsecureEncryption": false
              },
              "streamSettings": {
                  "network": "ws"
              },
              "sniffing": {
                  "enabled": true,
                  "destOverride": [
                      "http",
                      "tls"
                  ]
              }
          }
      ],
      "outbounds": [
          {
              "protocol": "freedom",
              "settings": {
                  "domainStrategy": "UseIP"
              },
              "tag": "UseIP"
          },
          {
              "protocol": "freedom",
              "settings": {
                  "domainStrategy": "UseIPv4"
              },
              "tag": "UseIPv4"
          },
          {
              "protocol": "freedom",
              "settings": {
                  "domainStrategy": "UseIPv6"
              },
              "tag": "UseIPv6"
          },
          {
              "protocol": "blackhole",
              "settings": {
              },
              "tag": "blocked"
          }
      ],
      "routing": {
          "domainStrategy": "IPOnDemand",
          "domainMatcher": "mph",
          "rules": [
              {
                  "type": "field",
                  "ip": [
                      "0.0.0.0/8",
                      "10.0.0.0/8",
                      "100.64.0.0/10",
                      "127.0.0.0/8",
                      "169.254.0.0/16",
                      "172.16.0.0/12",
                      "192.0.0.0/24",
                      "192.0.2.0/24",
                      "192.168.0.0/16",
                      "198.18.0.0/15",
                      "198.51.100.0/24",
                      "203.0.113.0/24",
                      "::1/128",
                      "fc00::/7",
                      "fe80::/10"
                  ],
                  "outboundTag": "blocked"
              },
              {
                  "type": "field",
                  "protocol": [
                      "bittorrent"
                  ],
                  "outboundTag": "blocked"
              }
          ]
      }
  }
  ' > $v2rayConfig
  ```
- ## 配置caddy
  ```
  echo "
  archlinux.icu
  {
      tls samber@gmail.com {
          on_demand
      }
      encode gzip
  #    多用户 多path
  #    import Caddyfile.multiuser
      handle_path /53e075c88dd0 {
          reverse_proxy localhost:54321
      }
      handle {
          reverse_proxy https://www.1ting.com {
              trusted_proxies 0.0.0.0/0
              header_up Host {upstream_hostport}
          }
      }
  }
  " > $caddyconfig
  ```
- ## 输出配置信息
  ```
  echo "
  域名: $domainName
  端口: 443
  UUID: $uuid
  安全: tls
  传输: websocket
  路径: $path
  协议：vmess
  额外ID: 0"
  ```
- ## 开启bbr
	- ###
	- ### 修改系统变量
	  ```
	  echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf && echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
	  ```
	- ### 保存生效
	  ```
	  sysctl -p
	  ```
	- ##
	- ### 查看内核是否已开启BBR
	  ```
	  sysctl net.ipv4.tcp_available_congestion_control
	  ```
	- ### 查看BBR是否启动
	  ```
	  lsmod | grep bbr
	  ```