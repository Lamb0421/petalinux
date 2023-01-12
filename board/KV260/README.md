# 目標
[1. 通過PetaLinux生成以下檔案：](#PetaLinux檔案生成步驟)  

  >BOOT.Bin  
  >boot.scr  
  >image.ub  
  >rootfs.cpio  
   
[2. 將SD卡分區。](#SD卡分區)  
[3. 將檔案放入SD卡使KV260能正常開機。](#製作開機SD卡)
# 開發環境
PetaLinux：2022.2  
Vivado：2022.2
# 事前作業  
+ 請先完成PetaLinux安裝。  
   筆者將PetaLinux 2022.2 安裝在`~/Desktop/petalinux/2022.2`底下。  
+ 通過Vivado生成XSA檔案。  
+ 創建好放置專案的目錄。  
   筆者將專案集中放在`~/Desktop/petalinux_project/2022.2`底下。
# PetaLinux檔案生成步驟
### 執行PetaLinux環境變數  
+ 注意：這部分`~/Desktop/petalinux/2022.2`請改為自行安裝之路徑。  
```
$ source ~/Desktop/petalinux/2022.2/settings.sh 
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/source.png)
### 移動至放置專案的路徑下  
+ 注意：這部分`~/Desktop/petalinux_project/2022.2`請改為自行創建之路徑。  
```
$ cd ~/Desktop/petalinux_project/2022.2  
```
### 生成Petalinux專案目錄 
+ -t：專案類型。  
+ --name：專案名稱。   
+ --template：CPU架構。 
```
$ petalinux-create -t project --name Xilinx_KV260 --template zynqMP  
``` 
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/create.png)
### 移動至專案根目錄
+ 注意：如有自行命名專案名稱，這部分`Xilinx_KV260`請改為自定義名稱。  
```
$ cd Xilinx_KV260/  
```
### 將XSA檔案放入專案底下  
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/XSA.png)  
### 匯入XSA檔案
+ --get-hw-description=：XSA檔案路徑。  
+ --silentconfig：跳過自定義介面。  
```
$ petalinux-config --get-hw-description=. --silentconfig  
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/config-xsa.png)
### 配置PetaLinux設定  
```
$ petalinux-config  
```
進行以下更改   
+ Image Packaging Configuration ---> root filesystem type ---> EXT4(SD/emmc/SATA/USB)  
+ Image Packaging Configuration ---> Device node of SD device ---> /dev/mmcblk1p2  

### 更改device-tree  
將路徑底下之`system-user.dtsi`改成參考檔案內文  
device-tree檔案路徑：`~/Desktop/petalinux_project/2022.2/KV260/project-spec/meta-user/recipes-bsp/device-tree/files`  
參考檔案：`system-user.dtsi`  
```
/include/ "system-conf.dtsi"
/ {
	chosen {
                bootargs = "earlycon console=ttyPS1,115200 clk_ignore_unused root=/dev/mmcblk1p2 rw init_fatal_sh=1 cma=1000M ";
                stdout-path = "serial1:115200n8";
        };
};

&sdhci1 { /* FIXME - on CC - MIO 39 - 51 */
	status = "okay";
	no-1-8-v;
	disable-wp;
	broken-cd;
	xlnx,mio-bank = <1>;
	/* Do not run SD in HS mode from bootloader */
	sdhci-caps-mask = <0 0x200000>;
	sdhci-caps = <0 0>;
	max-frequency = <19000000>;
};
```
### 開啟PetaLinux核心設定  
+ -c：配置指定系統。  
```
$ petalinux-config -c kernel  
```
會跳出一個新視窗，可以看到核心版本。  
暫不做更改，Exit離開。
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/kernel.png)
### 建置PetaLinux專案  
```
$ petalinux-build  
```
此時已經生成以下3個檔案在 images/linux 底下。 
   >boot.scr  
   >image.ub  
   >rootfs.cpio  

### 移動至檔案位置  
```
$ cd images/linux/  
```
### 生成BOOT.Bin  
```
$ petalinux-package --boot --fsbl zynqmp_fsbl.elf --u-boot u-boot.elf --pmufw pmufw.elf --fpga system.bit --force  
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/package.png)
# SD卡分區
### 查看SD卡位置
```
$ lsblk
```
### 解除掛載
```
$ umount /dev/sdd*
```
### 開啟fdisk
```
$ sudo fdisk /dev/sdd
```
### 刪除所有分區

### 建立分區

### SD卡格式化
```
$ sudo mkfs.vfat -F 32 -n boot /dev/sdd1
$ sudo mkfs.ext4 -L rootfs /dev/sdd2
```
# 製作開機SD卡
### 移動至專案路徑
```
$ cd ~/Desktop/petalinux_project/2022.2/Xilinx_KV260/images/linux
```
### 複製檔案到boot分區
```
$ sudo cp BOOT.bin /media/demo/boot
```
### 複製檔案到rootfs分區
```
$ sudo cp rootfs.cpio /media/demo/rootfs
```
### 解壓縮rootfs.cpio
```
$ sudo cpio -idm < rootfs.cpio
```
### 刪除rootfs.cpio
```
$ sudo rm rootfs.cpio
```
### 複製檔案到rootfs分區
```
$ cd ~/Desktop/petalinux_project/2022.2/Xilinx_KV260/images/linux
```
