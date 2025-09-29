# rEFInd-Boot-Manager

[![](https://github.com/TechTutoPPT/rEFInd-Boot-Manager/blob/main/cover.PNG)](https://youtu.be/RUNBfFD5F8s)

上一影片中MacBook Air可以透過開機時按著option鍵便能自動搜尋儲存媒體中帶有UEFI啟動的引導, 
這功能對於同一磁碟安裝了多個不同的作業系統情況下的切換相當方便, 但一般電腦沒有內置這功能,
我們能否安裝某工具以達至類似效果呢? 答案就是今次介紹的rEFInd.

首先到rEFInd官網下載安裝檔案:
https://www.rodsbooks.com/refind/getting.html

將下載回來的壓縮檔解壓後進入內裡的refind資料夾, 假如你用的都是x64架構系統, 那可刪除資料夾內的以下目錄及檔案:
目錄: drivers_aa64, drivers_ia32, tools_aa64, tools_ia32
檔案: refind_aa64.efi, refind_ia32.efi
然後將refind.conf-sample重新命名為refind.conf

由於以下操作絕對有機會破壞你現有的啟動引導, 你得明白風險及後果才好進行以下操作!
以系統管理員執行PowerShell, 然後執行以下指令:
掛載ESP分區到U:
```
mountvol U: /S
```
進入剛才refind資料夾的上一層, 例如:
```
cd D:\Desktop\refind-bin-0.14.2\refind-bin-0.14.2
```
將refind這資料夾連同檔案複製到ESP分區的EFI資料夾內:
```
xcopy /E refind U:\EFI\refind\
```
查看Firmware啟動選單配置:
```
bcdedit /enum firmware
```
修改Windows的啟動配置, 使用refind作為啟動引導:
```
bcdedit /set "{bootmgr}" path \EFI\refind\refind_x64.efi
```
修改選單描述:
```
bcdedit /set "{bootmgr}" description "rEFInd"
```
然後重啟電腦, 進入BIOS, 將rEFInd啟動選項設為首項, 保存並離開BIOS後便會進入rEFInd啟動選單頁面,
這樣便得到類似MacBook Air的UEFI多系統引導管理工具.

假如往後想卸載rEFInd, 可按以下步驟操作:
以系統管理員執行PowerShell, 然後執行以下指令:
掛載ESP分區到U:
```
mountvol U: /S
```
進入ESP的EFI資料夾:
```
cd U:\EFI
```
刪除refind資料夾及內裡檔案:
```
rmdir /S refind
```
修改Windows的啟動配置, 改回原來的引導檔:
```
bcdedit /set "{bootmgr}" path \EFI\Microsoft\Boot\bootmgfw.efi
```
修改選單描述:
```
bcdedit /set "{bootmgr}" description "Windows Boot Manager"
```
然後重啟電腦, 進入BIOS, 確認Windows Boot Manager啟動選項是否設為首項便完成.

