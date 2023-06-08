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
source ~/Desktop/petalinux/2022.2/settings.sh
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/source.png)
### 移動至放置專案的路徑下  
+ 注意：這部分`~/Desktop/petalinux_project/2022.2`請改為自行創建之路徑。  
```
cd ~/Desktop/petalinux_project/2022.2
```
### 生成Petalinux專案目錄 
+ -t：專案類型。  
+ --name：專案名稱。   
+ --template：CPU架構。 
```
petalinux-create -t project --template zynqMP --name Xilinx_KV260 
``` 
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/create.png)
### 移動至專案根目錄
+ 注意：如有自行命名專案名稱，這部分`Xilinx_KV260`請改為自定義名稱。  
```
cd Xilinx_KV260/
```
### 將XSA檔案放入專案底下  
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/XSA.png)  
### 匯入XSA檔案
+ --get-hw-description=：XSA檔案路徑。  
+ --silentconfig：跳過自定義介面。  
```
petalinux-config --get-hw-description=. --silentconfig
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/config-xsa.png)
### 配置PetaLinux設定  
```
petalinux-config
```
進行以下更改     
+ DTG Setting ---> MACHINE_NAME ---> zynqmp-smk-k26-reva
+ Yocto Setting ---> YOCTO_MACHINE_NAME ---> xilinx-k26-kv

### 更改device-tree  
將路徑底下之`system-user.dtsi`改成參考檔案內文  
device-tree檔案路徑：`~/Desktop/petalinux_project/2022.2/KV260/project-spec/meta-user/recipes-bsp/device-tree/files`  
參考檔案：`system-user.dtsi`  
```
/include/ "system-conf.dtsi"

&amba {
	si5332_0: si5332_0 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <125000000>;
	};

	si5332_1: si5332_1 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <25000000>;
	};

	si5332_2: si5332_2 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <48000000>;
	};

	si5332_3: si5332_3 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <24000000>;
	};

	si5332_4: si5332_4 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <26000000>;
	};

	si5332_5: si5332_5 { /* u17 */
		compatible = "fixed-clock";
		#clock-cells = <0>;
		clock-frequency = <27000000>;
	};
};

&psgtr {
	status = "okay";
	clocks = <&si5332_5>, <&si5332_4>, <&si5332_0>;
	clock-names = "ref0", "ref1", "ref2";
};

&zynqmp_dpsub {
	status = "okay";
};


&pinctrl0 { /* required by spec */
        pinctrl_usb0_default: usb0-default {
		mux {
			groups = "usb0_0_grp";
			function = "usb0";
		};
		
		conf {
			groups = "usb0_0_grp";
			slew-rate = <1>;
			io-standard = <1>;
		};
		
		conf-rx {
			pins = "MIO52", "MIO53", "MIO55";
			bias-high-impedence;
		};
		
		conf-tx {
			pins = "MIO54", "MIO56", "MIO57", "MIO58", "MIO59", "MIO60", "MIO61", "MIO62", "MIO63";
			bias-disable;
		};
		
	};
};
/{
	usb_phy0: usb_phy@0 {
		compatible = "ulpi-phy";
		#phy-cells = <0>;
		reg = <0xe0002000 0x1000>;
		view-port = <0x0170>;
		drv-vbus;
	};
};

&usb0 {
	dr_mode = "host";
	status = "okay";
	usb-phy = <&usb_phy0>;
};

&dwc3_0 {
	dr_mode = "host";
	snps,usb3_lpm_capable;
	phy-names = "usb3-phy";
	maximum-speed = "super-speed";
};

&sdhci1 {
	disable-wp; 
	no-1-8-v;
};
```
### 開啟PetaLinux核心設定  
+ -c：配置指定系統。  
```
petalinux-config -c kernel
```
會跳出一個新視窗，可以看到核心版本。  
暫不做更改，Exit離開。
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/kernel.png)
### 建置PetaLinux專案  
```
petalinux-build
```
此時已經生成以下3個檔案在 images/linux 底下。 
   >boot.scr  
   >image.ub  
   >rootfs.cpio  

### 移動至檔案位置  
```
cd images/linux/
```
### 生成BOOT.Bin  
```
petalinux-package --boot --fsbl zynqmp_fsbl.elf --u-boot u-boot.elf --pmufw pmufw.elf --fpga system.bit --force
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/package.png)
# SD卡分區
### 查看SD卡位置  
+ 找到SD卡路徑。  
```
lsblk
```
### 解除掛載  
+ 注意：這部分`/dev/sdc`請改為當下SD卡路徑。  
```
umount /dev/sdc*
```
### 開啟fdisk
+ 注意：這部分`/dev/sdc`請改為當下SD卡路徑。  
```
sudo fdisk /dev/sdc
```
fdisk 用法如下：
+ p 查看分區  
+ d 刪除分區  
+ n 新增分區  
+ w 儲存  
+ q 離開  
### 建立分區
建立2個分區，步驟如下： 
+ 刪除分區： d ---> `Enter` ---> `Enter` ---> `Enter` ---> `Enter`
+ 建立第一分區： n ---> `Enter` --->   +1G   ---> `Enter` ---> `Enter`
+ 建立第二分區： n ---> `Enter` ---> `Enter` ---> `Enter` ---> `Enter`
### SD卡格式化
```
sudo mkfs.vfat -F 32 -n boot /dev/sdc1
sudo mkfs.ext4 -L rootfs /dev/sdc2
```
# 製作開機SD卡
### 移動至專案路徑
```
cd ~/Desktop/petalinux_project/2022.2/Xilinx_KV260/images/linux
```
### 複製檔案到boot分區
```
sudo cp BOOT.bin /media/demo/boot
sudo cp boot.scr /media/demo/boot
sudo cp image.ub /media/demo/boot
```
### 複製檔案到rootfs分區
```
sudo cp rootfs.tar.gz /media/demo/rootfs
```
### 移動至rootfs
```
cd /media/demo/rootfs
```
### 解壓縮rootfs.tar.gz 
```
sudo tar zxvf rootfs.tar.gz 
```
### 刪除rootfs.tar.gz (可不做)
```
sudo rm rootfs.tar.gz 
```

