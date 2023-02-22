- # placeholder
- # haproxy
	- ## Configure Let's Encrypt
	  As root, install acme.sh:
	  ```
	  git clone https://github.com/Neilpang/acme.sh.git
	  cd ./acme.sh
	  ./acme.sh --install
	  . ~/.bashrc
	  ```
	  Issue ECC and RSA certificates for example.org (assuming an HTTP server running at /var/www/html):
	  ```
	  site=archlinux.icu
	  acme.sh -k ec-256 -d $site -d www.$site --issue --standalone
	  acme.sh -k 2048 -d $site -d www.$site --issue --standalone
	  ```
	-
	- ## Install in bundled format as required by HAProxy for supporting dual certificates:
	  ```
	  mkdir -p /etc/haproxy/certs
	  acme.sh --install-cert --ecc -d $site --key-file /tmp/$site.key --fullchain-file /tmp/$site.crt --reloadcmd "cat /tmp/$site.* >/etc/haproxy/certs/$site.pem.ecdsa; rm /tmp/$site.*; service haproxy restart"
	  acme.sh --install-cert -d $site --key-file /tmp/$site.key --fullchain-file /tmp/$site.crt --reloadcmd "cat /tmp/$site.* >/etc/haproxy/certs/$site.pem.rsa; rm /tmp/$site.*; service haproxy restart"
	  ```
	- ## Set up a forward proxy
	  `apt install tinyproxy`
	  Edit `/etc/tinyproxy/tinyproxy.conf`:
	  ```
	  User tinyproxy
	  Group tinyproxy
	  PidFile "/run/tinyproxy/tinyproxy.pid"
	  MaxClients 100
	  MinSpareServers 5
	  MaxSpareServers 20
	  StartServers 10
	  Port 8888
	  
	  Listen 127.0.0.1
	  LogFile "/dev/null"
	  DisableViaHeader Yes
	  ```
	- ## Set up HAProxy
	  `apt install haproxy`  on server. (Make sure it is 1.8.15+ or 1.9+.)
	  Append the following to  `/etc/haproxy/haproxy.cfg` :
	  ```
	  userlist users
	          user name insecure-password pass
	  
	  frontend haproxy_tls
	          bind :443 ssl crt /etc/haproxy/certs/ alpn h2,http/1.1 curves X25519:P-256:P-384:P-521 ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-CHACHA20-POLY1305:ECDHE+AES128:RSA+AES128:ECDHE+AES256:RSA+AES256:ECDHE+3DES:RSA+3DES
	          option http-use-proxy-header
	          acl login base_dom login-key.test
	          acl auth_ok http_auth(users)
	          http-request auth if login !auth_ok
	          http-request redirect location https://google.com if login auth_ok
	          use_backend proxy if auth_ok
	          default_backend masquerade
	  
	  backend proxy
	          http-request del-header proxy-authorization
	          server proxy 127.0.0.1:8888
	  
	  backend masquerade
	          server nginx 127.0.0.1:80
	  ```
	-
	-
	-
	-
	-
	-
	-
	-
- # disbale ipv6
  :LOGBOOK:
  CLOCK: [2022-09-05 Mon 16:15:07]--[2022-09-05 Mon 16:15:10] =>  00:00:03
  :END:
	- cat /proc/sys/net/ipv6/conf/all/disable_ipv6
	- ## 永久生效
	  ```
	  sudo vim /etc/sysctl.conf
	  net.ipv6.conf.all.disable_ipv6=1
	  net.ipv6.conf.default.disable_ipv6=1
	  net.ipv6.conf.lo.disable_ipv6=1
	  sudo sysctl -p
	  ```
	-
-