- ## 安装 archlinux
- ### 准备镜像
  下载[archlinux 镜像](https://archlinux.org/download/)和rufus
- ### 0 .进入安装环境
- ### 1 .禁用 reflector 服务
  ```
  systemctl stop reflector.service
  systemctl status reflector.service
  ```
- ### 2.确认是否为 UEFI 模式
  ```
  ls /sys/firmware/efi/efivars
  ```
- ### 3.连接网络
  ```
  iwctl # 进入交互式命令行
  device list # 列出无线网卡设备名，比如无线网卡看到叫 wlan0
  station wlan0 scan # 扫描网络
  station wlan0 get-networks # 列出所有 wifi 网络
  station wlan0 connect wifi-name # 进行连接，注意这里无法输入中文。回车后输入密码即可
  exit # 连接成功后退出
  ```
- ### 5. 更新系统时钟
  ```
  timedatectl set-ntp true # 将系统时间与网络时间进行同步
  timedatectl status # 检查服务状态
  ```
- ### 6.换源
  ```
  vim /etc/pacman.d/mirrorlist
  ```
  ```
  Server = https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch # 北外
  Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch # 中国科学技术大学开源镜像站
  ```
- ### 7.分区
  |分区 | 大小 |备注|
  |--- | --- | ---|
  |/   |     | 和用户主目录在同一个`Btrfs`文件系统上|
  |/home |    |
  |/boot/efi |   |
  |swap分区 | 8G |
  ```
  lsblk
  cfdisk /dev/YourDisk
  ```
  分区完成后，使用 `fdisk` 或`lsblk`命令复查分区情况
- ### 8.格式化硬盘分区并挂载
- #### 8.1格式化 Swap 分区
  ```
  mkswap /dev/sdxn
  ```
- #### 8.2格式化 Btrfs 分区
  1.整一个分区格式化为 `Btrfs` 文件系统
  ```
  mkfs.btrfs -L myArch /dev/sdxn
  ```
  2.为了创建子卷，我们需要先将 `Btrfs` 分区挂载到 `/mnt` 下
  ```
  mount -t btrfs -o compress=zstd /dev/sdxn /mnt
  ```
  3.使用 `df` 命令复查挂载情况：
  ```
  df -h # -h 选项会使输出以人类可读的单位显示
  ```
  4.通过以下命令创建两个 `Btrfs` 子卷，之后将分别挂载到 `/` 根目录和 `/home` 用户主目录：
  ```
  btrfs subvolume create /mnt/@ # 创建 / 目录子卷
  btrfs subvolume create /mnt/@home # 创建 /home 目录子卷
  ```
  通过以下命令复查子卷情况：
  ```
  btrfs subvolume list -p /mnt
  ```
  子卷创建好后，我们需要将 `/mnt` 卸载掉，以挂载子卷
  ```
  umount /mnt
  ```
- #### 8.3 挂载
  在挂载时，挂载是有顺序的，需要从根目录开始挂载。使用如下命令挂载子卷：
  ```
  mount -t btrfs -o subvol=/@,compress=zstd /dev/nvmexn1pn /mnt # 挂载 / 目录
  mkdir /mnt/home # 创建 /home 目录
  mount -t btrfs -o subvol=/@home,compress=zstd /dev/nvmexn1pn /mnt/home # 挂载 /home 目录
  mkdir -p /mnt/boot/efi # 创建 /boot/efi 目录
  mount /dev/nvmexn1pn /mnt/boot/efi # 挂载 /boot/efi 目录
  swapon /dev/nvmexn1pn # 挂载交换分区
  ```
  使用 `df -h`  和 `free -h` 命令复查挂载情况
- ### 9.安装系统
  ```
  pacstrap /mnt base base-devel linux linux-firmware linux-firmware-marvell
  pacstrap /mnt networkmanager nvim sudo fish
  ```
- #### 10.生成 fstab 文件
  ```
  genfstab -U /mnt > /mnt/etc/fstab
  cat /mnt/etc/fstab
  ```
- #### 11. change root
  ```
  arch-chroot /mnt
  ```
- #### 12.chroot 后的工作
- ##### 设置主机名
  ```
  echo myarch > /etc/hostname
  ```
  接下来在 `/etc/hosts` 设置与其匹配的条目：
  ```
  vim /etc/hosts
  ```
  加入如下内容：
  ```
  127.0.0.1   localhost
  ::1         localhost
  127.0.1.1   myarch.localdomain	myarch
  ```
- ##### 时区
  ```
  ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
  hwclock --systohc --utc
  timedatectl set-ntp true
  ```
- ##### Locale
  编辑 `nvim /etc/locale.gen` ，并取消如下三处的注释：
  ```
  en_US.UTF-8 UTF-8
  zh_CN.UTF-8 UTF-8
  zh_TW.UTF-8 UTF-8
  ```
  然后执行
  ```
  locale-gen
  echo LANG=en_US.UTF-8 > /etc/locale.conf
  ```
- ##### 设置用户
  修改 root 密码
  ```
  passwd
  ```
  创建拥有 root 权限的普通用户
  ```
  EDITOR=nvim visudo
  ```
  删除 `%wheel ALL=(ALL) ALL` 前的注释，然后保存退出
  创建一个有家文件夹的普通用户，并为其设置密码
  ```
  useradd -m -G wheel -s /bin/bash myusername
  passwd username
  ```
- #### 13.安装引导程序
  ```
  pacman -S grub efibootmgr os-prober
  ```
  安装完这3个包以后,安装 GRUB 到 EFI 分区
  ```
  grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
  ```
  编辑 `/etc/default/grub` 文件,去掉 `GRUB_DISABLE_OS_PROBER=false` 的注释
  最后生成 `GRUB` 所需的配置文件：
  ```
  grub-mkconfig -o /boot/grub/grub.cfg
  ```
- #### 14.完成安装
  ```
  exit # 退回安装环境
  umount -R /mnt # 卸载新分区
  reboot # 重启
  ```
- ### archlinux 中的配置
- ##### 配置网络
  ```
  systemctl enable --now NetworkManager
  nmtui
  ```
- ##### 开启archlinuxcn
  ```
  vim /etc/pacman.conf
  ```
  添加
  ```
  [archlinuxcn]
  Server = https://mirrors.bfsu.edu.cn/archlinuxcn/$arch
  
  [archlinuxcn]
  Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch # 中国科学技术大学开源镜像站
  ```
  
  ```
  sudo pacman -S archlinuxcn-keyring
  ```
- ##### 开启蓝牙
  ```
  sudo pacman -S bluez bluez-utils
  sudo modprobe btusb
  sudo systemctl enable bluetooth.service
  sudo systemctl start bluetooth.service
  ```
- ### sway
- ## 安装 windows 10
- ### 时间校准
  开**管理员模式**的 terminal 让 windows 用 UTC 时间
  ```
  reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
  ```
  随后把 windows 的自动对时关了
- ## arch win10联动
- ### 存储共享
- #### archlinux 挂载 NTFS 分区
  首先输入 `id` 查看自己的 uid 和 gid，输入 `lsblk` 查看 windows 分区的 uuid ，最好通过复制或拍照的方式记下来。
  然后编辑 `fstab` ，按照如下格式写配置：
  ```
  UUID=... /mountpoint ntfs-3g uid=...,gid=...,dmask=022,fmask=133   0 0
  ```
  如果需求不高，也可以通过使用指令 `ntfs-3g /dev/YourDiskParts /mountpoint` 来手动挂载
  如上述指令在执行后无法挂载分区或分区只读，尝试清除 NTFS 分区中的缓存
  即使用指令：`sudo ntfs-3g -o remove_hiberfile /dev/YourDiskParts ~/mountpoint`
- #### windows 挂载 btrfs 分区
  有人开发了可以在 windows 下挂载 btrfs 的驱动：[maharmstone](https://github.com/maharmstone/btrfs)
  下载后右键菜单安装autorun.inf
  注意，其会像 U 盘一样自动按顺序安装在一个新的驱动器号，且**不可修改**，即便将驱动卸载并重装驱动，也依旧不可修改。