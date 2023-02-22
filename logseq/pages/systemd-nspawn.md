- ## 安装
  ```
  sudo pacman -S debootstrap ubuntu-keyring 
  set container_name ubuntu
  mkdir $container_name
  set codename jammy
  set repository_url 'https://mirrors.ustc.edu.cn/ubuntu/'
  debootstrap --include=systemd-container \
  --components=main,universe,multiverse \
  $codename $container_name $repository_url
  ```
  安装完成后，运行 systemd-nspawn --machine=$container_name 即可启动进入崭新的容器。
- ## 
  ```
  ```
- ```
  useradd --create-home ubuntu
  su --login ubuntu
  mkdir --parents ~/.config ~/.local/share
  ```
- ## 启动
  ```
  systemd-nspawn --machine=ubuntu \
  --bind-ro=/tmp/.X11-unix/ \
  --setenv=DISPLAY=$DISPLAY \
  --bind=/dev/dri/card0 \
  -u ubuntu
  ```
-