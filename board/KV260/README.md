# 目標
1. 通過PetaLinux生成以下檔案：
   >BOOT.Bin  
   >boot.scr  
   >image.ub  
   >rootfs.cpio  
   
2. 將SD卡分區。  
3. 將檔案放入SD卡使KV260能正常開機。
# 開發環境
PetaLinux：2022.2  
Vivado：2022.2
# 事前作業
1. 請先完成PetaLinux安裝。  
2. 通過Vivado生成XSA檔案。  
3. 創建好放置專案的目錄。  
   筆者將專案集中放在 ~/Desktop/petalinux_project/2022.2/ 底下。
# PetaLinux檔案生成步驟
### 執行PetaLinux環境變數  
```
source ~/Desktop/petalinux/2022.2/settings.sh 
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/source.png)
### 移動至放置專案的路徑下  
```
cd ~/Desktop/petalinux_project/2022.2/  
```
### 生成Petalinux專案目錄  
```
petalinux-create -t project --name Xilinx_KV260 --template zynqMP  
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/create.png)
### 移動至專案根目錄
```
cd Xilinx_KV260/  
```
### 將XSA檔案放入專案底下  
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/XSA.png)  
### 匯入XSA檔案
```
petalinux-config --get-hw-description=. --silentconfig  
```
![image](https://github.com/Lamb0421/petalinux/blob/main/board/KV260/Iamge/config-xsa.png)
### 開啟PetaLinux設定  
```
petalinux-config  
```
進行以下更改   
>Image Packaging Configuration ---> root filesystem type ---> EXT4(SD/emmc/SATA/USB)  
>Device node of SD device ---> /dev/mmcblk1p2  

### 更改device-tree  
路徑：  
參考檔案：system-user.dtsi  
### 開啟PetaLinux核心設定  
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
### 移動至檔案位置  
```
cd images/linux/  
```
### 生成BOOT.Bin  
```
petalinux-package --boot --fsbl zynqmp_fsbl.elf --u-boot u-boot.elf --pmufw pmufw.elf --fpga system.bit --force  
```

