-
- ```
  * redir: Works with certain iptables setup.
  
        (Redirecting locally originated traffic)
  sudo iptables -t nat -A OUTPUT -d 206.81.14.56 -j RETURN \
  && sudo iptables -t nat -A OUTPUT -p tcp -j REDIRECT --to-ports 1080 \
  && sudo iptables -t nat -A PREROUTING -p tcp -j REDIRECT --to-ports 1080
  
  sudo iptables -t nat -F
  ```
	-
	- ```
	  sudo iptables -t nat -N V2RAY 
	  sudo iptables -t nat -A V2RAY -d 192.168.0.0/16 -j RETURN 
	  sudo iptables -t nat -A V2RAY -p tcp -j RETURN -m mark --mark 0xff 
	  sudo iptables -t nat -A V2RAY -p tcp -j REDIRECT --to-ports 12345
	  sudo iptables -t nat -A PREROUTING -p tcp -j V2RAY
	  sudo iptables -t nat -A OUTPUT -p tcp -j V2RAY 
	  ```