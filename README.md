# 安裝petalinux 2022.2
### 下載網址
https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/embedded-design-tools.html
### 安裝必要包
```
sudo apt-get install tofrodos iproute2 gawk xvfb gcc git make net-tools libncurses5-dev tftpd zlib1g-dev libssl-dev flex bison libselinux1 gnupg wget diffstat chrpath socat xterm autoconf libtool tar unzip texinfo gcc-multilib build-essential libsdl1.2-dev libglib2.0-dev libssl-dev screen pax gzip zlib1g:i386 libtinfo5
```
### 變更檔案權限
```
chmod 777 petalinux-v2022.2-10141622-installer.run
```
### 安裝
```
./petalinux-v2022.2-10141622-installer.run --dir ~/Desktop/petalinux/2022.2
``` 
### shell 改為 bash
```
sudo dpkg-reconfigure dash
```

![image](https://user-images.githubusercontent.com/122330661/211512672-9db11600-51ba-43ae-bcc4-a255bd7981b6.png)

