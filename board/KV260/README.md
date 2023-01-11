# 目標
1. 通過PetaLinux生成以下檔案：
>BOOT.Bin  
>boot.scr  
>image.ub  
>rootfs.cpio  

2.   
3. 並將其放入SD卡使KV260能正常開機。
# 開發環境
PetaLinux：2022.2  
Vivado：2022.2
# 事前作業
1. 安裝好PetaLinux  
2. 通過Vivado生成XSA檔案  
3. 創建好放置專案的目錄  
   筆者將專案集中放在 ~/Desktop/petalinux_project/2022.2/ 底下
# PetaLinux檔案生成步驟
### 執行PetaLinux環境變數  
```
source ~/Desktop/petalinux/2022.2/settings.sh 
```
### 移動至放置專案的路徑下  
```
cd ~/Desktop/petalinux_project/2022.2/  
```
### 生成Petalinux專案目錄  
```
petalinux-create -t project --name Xilinx_KV260 --template zynqMP  
```
### 移動至專案根目錄
```
cd Xilinx_KV260/  
```
### 匯入Vivado所產生之XSA檔案  
```
petalinux-config --get-hw-description=. --silentconfig  
```
### 開啟PetaLinux設定  
```
petalinux-config  
```
進行以下更改  
>DTG Setting ---> MACHINE_NAME ---> zynqmp-smk-k26-reva  
>Yocto Setting ---> YOCTO_MACHINE_NAME ---> xilinx-k26-kv  
>Image Packaging Configuration ---> root filesystem type ---> EXT4(SD/emmc/SATA/USB)  
>Device node of SD device ---> /dev/mmcblk1p2  

### 更改device-tree  
路徑：  
參考檔案：  
### 開啟PetaLinux核心設定  
```
petalinux-config -c kernel  
```
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

![image](https://user-images.githubusercontent.com/122330661/211705993-41549394-efc3-481e-86e1-090003267f0e.png)

![image](https://user-images.githubusercontent.com/122330661/211705588-668abf71-114c-4645-9cad-fded1b57e9c3.png)

![image](https://user-images.githubusercontent.com/122330661/211705114-86069eaf-bb5d-4136-a4bd-ae58d3155c36.png)
![image](https://user-images.githubusercontent.com/122330661/211705173-d940037e-64b4-4d2d-8f85-6886c03f863e.png)

![image](https://user-images.githubusercontent.com/122330661/211704140-82fe98ce-242b-407e-8f12-9a636d11cf31.png)
![image](https://user-images.githubusercontent.com/122330661/211704172-f941a49b-3c64-45ac-9d74-149bc299d813.png)

![image](https://user-images.githubusercontent.com/122330661/211704083-6702ebca-8d76-4d03-b607-5254ce064983.png)

![image](https://user-images.githubusercontent.com/122330661/211704022-8d727a1f-e185-4a7d-a1a8-bb4fd05b926a.png)

![image](https://user-images.githubusercontent.com/122330661/211703966-15ea7b87-1fc9-4f51-81ea-e5da44db825d.png)

![image](https://user-images.githubusercontent.com/122330661/211703613-0445f718-15c3-4822-8cf0-5a27cef00d36.png)
![image](https://user-images.githubusercontent.com/122330661/211702606-8acd54b6-9dce-49b2-adc5-ebd3b381689f.png)
![image](https://user-images.githubusercontent.com/122330661/211702855-6d001c97-18eb-4009-ab98-2db684cd6dda.png)
![image](https://user-images.githubusercontent.com/122330661/211703026-b0293e0c-7753-4c80-a808-c41ce70873aa.png)
![image](https://user-images.githubusercontent.com/122330661/211703376-255e2841-168a-4188-867d-271dca42b367.png)
