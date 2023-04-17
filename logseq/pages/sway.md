# sway
- ## mpv
	-
- ## app
  |program | name|
  |---| ---|
  |**terminal** | foot|
  |**bar** | waybar ttf-nerd-fonts-symbols-2048-em-mono|
  |**Application Launcher** | wofi|
  |**network management tool** | NetworkManager|
  |**Input method framework** |fcitx5|
  |**File Manager** |ranger thunar|
  |**Lockscreen** | swaylock swayidle|
  |**logout**| wlogout|
  |**Shell** | fish|
  |**Music Player** | mpd ncmpcpp|
  |**Media Player**|mpv|
  |**Text Editor**|neovim|
  |**GTK Theme**|Catppuccin|
  |**Icons**| 	Papirus Dark|
  |**Fonts**| 	noto-fonts noto-fonts-cjk noto-fonts-emoji ttf-sarasa-gothic|
  |**Image Viewer**|imv|
  |**Screen Recording**|wf-recorder|
  |**Clipboard**|wl-clipboard|
  |**PDF**|zathura okula |
  |**office**|libreoffice-still libreoffice-still-zh-cn|
## fonts
安装字体包,配置fontconfig
```
noto-fonts noto-fonts-cjk noto-fonts-emoji ttf-sarasa-gothic
```
## 配置音频
```
sudo pacman -S pipewire pipewire-pulse pipewire-alsa pavucontrol  
systemctl enable pipewire-pulse.service
systemctl start --user pipewire-pulse.service
```
如需要图形化界面控制音频，可以安装 `pavucontrol`
## 开启蓝牙
```
sudo pacman -S bluez bluez-utils
sudo modprobe btusb
sudo systemctl enable bluetooth.service
sudo systemctl start bluetooth.service
```
```
$bluetoothctl
sudo vim /etc/modprobe.d/bluetooth-usb.conf
options btusb enable_autosuspend=n
```
## 配置状态栏
```
sudo pacman -S waybar otf-font-awesome
```
- ## 截屏
  ```
  sudo pacman -S grim slurp swappy
  ```
## 配置背光
```
sudo pacman -S brightnessctl
```
- ## 配置输入法
  ```
  sudo pacman -S fcitx5-im \
  fcitx5-chinese-addons \
  fcitx5-pinyin-zhwiki \
  fcitx5-pinyin-moegirl \
  fcitx5-material-color \
  fcitx5-configtool \
  fcitx5-lua
  ```
  设置环境变量
  ```
  INPUT_METHOD DEFAULT=fcitx5
  GTK_IM_MODULE DEFAULT=fcitx5
  QT_IM_MODULE DEFAULT=fcitx5
  XMODIFIERS DEFAULT=\@im=fcitx5
  SDL_IM_MODULE DEFAULT=fcitx
  ```
## vaapi
```
intel-media-driver libva-utils intel-gpu-tools
```
## firefox
中文包 `firefox-i18n-zh-cn`
### Hardware video acceleration
Set `media.ffmpeg.vaapi.enabled` to `true` in `about:config`
- ### custom css
  Set `toolkit.legacyUserProfileCustomizations.stylesheets` to `true` in `about:config`
  browser.uidensity 设置为 1
## thunar
```
/usr/bin/foot nvim %f
install catfish, then catfish --path=%f
```
- ## lf
	- ## apps
	  ````
	  sudo pacman -S trash-cli \
	  dragon-drop \
	  ripgrep \
	  ```
- ## proxy
	- ## 代理
		- ```
		  export all_proxy=socks5h://127.0.0.1:10800 \
		         http_proxy=socks5h://127.0.0.1:10800 \
		         https_proxy=socks5h://127.0.0.1:10800
		  ```
	- ### electron应用
		- ```
		  --proxy-server=socks5://127.0.0.1:10800
		  ```
	-
- ## rsync
  注意source后面的` / `
- ```
  rsync --archive --delete --dry-run --verbose source distination
  ```
- ## scrcpy
  ```
  connect usb
  adb tcpip 5555
  ```
  拔掉usb
  ```
  adb connect 10.10.5.169:5555
  scrcpy
  ```
## samba
Install the samba and  smbclient gvfs-smb package
编辑/etc/samba/smb.conf
```
#======================= Global Settings =====================================
[global]
# workgroup = NT-Domain-Name or Workgroup-Name, eg: MIDEARTH
 workgroup = MYGROUP
# server string is the equivalent of the NT Description field
 server string = Samba Server

 server role = standalone server

log file = /var/log/samba/%m.log
# Put a capping on the size of the log files (in Kb).
 max log size = 50

include = /home/zhao/.config/samba/share.smb.conf

 dns proxy = no
```
添加user
```
sudo smbpasswd -a zhao
```
查看user
```
sudo pdbedit -L -v
```
启动smb
```
systemctl start smb
```
list public shares
```
smbclient -L 127.0.0.1 -U%
```

```
smb://127.0.0.1
```
- ## wine for wechat
  ```
  sudo pacman -S wine-for-wechat wine-wechat-setup
  ```
- ## wf-recorder
  ```
  wf-recorder --audio --file=recording_with_audio.webm
  ```
## issues
### Polkit authorisation for GUI apps
```
sudo pacman -S polkit-gnome
```
添加启动在~/.config/sway/conig
```
exec /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
```
### flicking
```
nvim /etc/modprobe.d/intel.conf
options i915 enable_psr=0
```
- ### 键盘断连暂时解决办法
  ```
  usbcore.autosuspend=-1
  ```
- ## obsidian hidpi
  ```
   cp /usr/share/applications/obsidian.desktop
   env=XCURSOR_SIZE=48 --force-device-scale-factor=2
  ```
- ## timeshift
  要手动启动
- ```
  systemctl enable cronie
  ```