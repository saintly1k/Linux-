## Centos優勢  
centos 對於伺服器的架設很穩定，維護一次可以運行很久且在linux的世界裡不是最新就是最好，穩定度才是最重要的。

## VirtulBox安裝教學
- 下載網址：  
>https://www.virtualbox.org/wiki/Downloads
- 把Extension Pack加到VM VirtualBox，建立新的虛擬機
- 選擇redhat 或是 other linux，記憶體至少分配1024，磁碟預設8GB
- 硬碟類型選擇預設
- 存放硬碟(50G以上)如果是動態就會根據使用狀況分配，如果硬碟空間夠大，也可以選擇固定大小，執行速度會較快
## Centos設定教學
>https://blog.gtwang.org/linux/centos-7-installation-tutorial/
## 網路型態
- NAT  
在不同私有網路，就算同IP，也無法通訊(因為外面連不進去)  
私有網路就是使用NAT，連到外網  
虛擬機(Linux)可以連到物理機(win)，物理機連不到虛擬機(物理機在外面，虛擬機在內部私有網路)  
虛擬機透過虛擬網卡再連到虛擬NAT設備(類似Router)，在連到物理機  
- Bridged Adapter  
讓自己的機器和虛擬機可以互連(IP位置經過網路遮罩後相同，可以互ping)  
可以直接連到網路上(虛擬機跟網際網路上任何節點地位相同)  
這種模式下Linux主機可以當作Server，需要選擇真正連線到的網路(ethernet、 wifi ...)
- Internal Network  
虛擬機只能和虛擬機通訊，且一定要在相同區域網路下(IP要手動設定)，這個是封閉式網路，無法連上網  
- Host-only Adapter  
會產生一張虛擬的網路卡，只能本地互連，虛擬機無法連到外網  
- NAT Network  
在同一個網域內開虛擬機，虛擬機就可以互相通訊(NAT network)，NAT 的虛擬機無法互相通訊  
## Linux指令
- 關機指令： `halt -p`  --> `poweroff`  --> `shutdown` ，其中 `halt` 的 `-p` 代表poweroff
- 超級管理者 > su
- 切換資料夾 > cd
- shutdown: 默認情況為1分鐘後關機，+n表示 n 分鐘後關機，在這個過程中 shutdown -c 可以取消、shutdown -h now 立即關機、shutdown -r 電腦重啟
- 查看網路信息 > ifconfig
- 管理原提示符號 > #
- 一般使用者提示符號 > $
- 清除螢幕畫面 > clear
- 移動資料夾到指定位置 > mv file path
- 查看Linux版本 > uname -r ，使用uname -a 可以查看更多資訊
- 修改密碼 > passwd username
- 新增使用者 > useradd username
- ssh登入 > ssh username@localhost
- 列出資料夾下的內容 > ls
- 顯示test.txt的詳細訊息 > ls -l test.txt
- -h把檔案大小變成K為單位 > ls -l -h test.txt
- 顯示隱藏檔案 > ls -a
- 查看home下所有檔案 > ls -alh /home
- 顯示目前位置 > pwd
- 回到家目錄 > cd ~
- 查看當前在使用的shell > echo $SHELL
-  顯示指令的完整內容 > man+command 
- 查看帳號，前面是系統帳號，後面是使用者帳號 > more /etc/passwd
- x代表密碼，真正的密碼放在其他地方，後面的1000代表是群組ID，如果小於1000就代表是系統帳號，大於1000代表是一般使用者 > user: x:1000:1000:user:home/user:bin/bash
- 作為column的分割符號，打印出第一行和第七行 > awk -F: {print $1, $7} /etc/passwd
- 硬連結，對 a 產生連結 > ln a link
- 連結不會產生連接數量 > ln -S a link
- tcp、udp、nonresult 代表不解析，顯示IP不顯示協議、listen、process > netstat -tunlp | grep 80
-  創建一個空文件 > touch .a
- 在安裝的時候常會被lock住，刪除掉下面出現的pid，9代表相關的行程也會刪除 > sudo kill -9 pidnumber
- 加上文件或資料夾 > stat
- 查看資料夾本身的內容 > ls -l -d dir
- 拷貝目錄到本地資料夾 > cp -r /etc . 
- 把檔案的行數標示出來，使用less做行數限制 > cat -n filename | less
- 查看文件內容的尾巴和頭 > tail、head
- 創建資料夾和刪除資料夾 > mkdir、rmdir
- 查看目前使用kernal version > uname -r
- 判斷前面的指令是否執行成功 > echo $?
- 直接從網路上下載檔案 > wget url
- 清除文件裡面的內容 > > a.txt
- 返回上一個資料夾 > cd -
- 直接輸入文字到a.txt裡面 > cat <<EOF > a.txt
- 賦予可執行的權限 > chmod +x ls
- 查看資料夾的權限及位元 > file usr/bin/mv
- 改變指令執行結果 > echo "rm -f /ect" > ls
- 知道某個執行檔的位置 > which
- 查看資料使用量 > df -h
- 查看某個資料夾的使用情形 > du /home/user -h
- 查看是否正確執行 > echo $?
- 查看是否有這個使用者 > id user
- 為程式碼加上可執行權限 > chmod -x file
- passwd需要使用額外指令去完成管道 > echo "tom" | passwd --stdin tom
- 權限全部開啟 > chmod 777 filename
- 等同於ls -l > ll
- 可以看到CDROM、硬碟、和一些硬體設備 > lsblk
- 查看目前使用的是哪個終端和信息 > ls -l /proc/self/fd
- 等於> pwd > echo ~+
- 顯示上一個所在的路徑 > echo ~-
- 查看bash位置 > which bash
- 找出用戶的UID > cat /etc/passwd | grep user2 | awk -F':' '{print $3}'
- 可以看到行程樹 > pstree
- 查看系統的行程和工作 > top
- 查看記憶體使用量 > free -m
- 顯示行程所有訊息 > ps -l
- 查看kill的參數 > kill -l
- 不讓伺服器斷線，且可以更改配置檔案 > kill -1 httpd_pid
- 暴力的殺掉不受控制的行程 > kill -9 pid
- 查看背景執行工作 > jobs
- 把背景執行的第一個工作丟到前景來 > fg 1
- 讓背景執行的第一個工作繼續執行 > bg 1
- 讓程式停頓60秒 > sleep 60
- !!代表上一個指令 > sudo !!
- 查看後面的指令是內部指令還是外部命令 > type -a echo
- 顯示系統的環境變亮還有自己定義的變量 > set
- 這個跟su一樣是切換成超級使用者，不過環境變量會不一樣 > su -
## 架設伺服器
先ping 8.8.8.8看有沒有連到網路，如果還是不行，就用指令查看防火牆有沒有關 

>systemctl status firewalld  // 查看防火牆狀態
systemctl disable firewalld  // 之後開機防火牆都會關閉
systemctl stop firewalld  // 防火牆馬上關閉
// 後面的 d 代表 daemon  (守護進程)

>yum install httpd
systemctl start httpd  // 啟動http伺服器，架設在enp0s8上
systemctl status httpd
## 重製root密碼
> https://autumncher.pixnet.net/blog/post/462809249-%E3%80%90centos7%E3%80%91%E5%A6%82%E4%BD%95%E9%87%8D%E7%BD%AEroot%E5%AF%86%E7%A2%BC-(how-to-recover-root-pas
## file system
在linux系統下的檔案系統有很多: ext2、ext3、ext4、xfs...
## 磁碟分割
分割有3種: primary、extanded、logic  
最基本的可以把硬碟分成4份P(C、D、E、F)  
linux進行磁碟分割時，至少會占用2個磁碟分割區(P+P、P+L)，一個是根目錄(/)，一個是swap  
/: 根目錄，所有的目錄、文件、設備都在/之下， /就是Linux文件系統的組織者，也是最上級的領導者  
/bin: 存放二進位文件，在一般的系統當中，都可以在這個目錄下找到Linux常用的命令。系統所需要的那些命令位於此目錄  
/sbin: 這個目錄是用來存放系統管理員的系統管理程序。大多是涉及系統管理命令的存放，是超級權限使用者root的可執行命令存放地，普通使用者無權執行這個目錄底下的命令  
/boot: Linux的內核及引導系統程序所需要的文件目錄，放文件的映象檔  
/dev: 在這個目錄中包含了所有Linux系統中使用的外部設備。但是這裡不是放的外部設備的驅動程式(驅動程式可以讓硬體和操作系統可以更好的配合)，Linux裡面的驅動程式很齊全，大多不用額外安裝  
/etc: 存放配置文件，每個軟體通常會有，像是個人偏好，設定之類的都會在這裡，管理員權限，系統變量設定  
/home: 裡面放每個使用者的文件夾(存放使用者的主目錄)，root比較特別直接在根目錄底下  
/lib: 存放函式庫(C語言)  
/media: 存放光碟內容  
/mnt: 掛載第三方文件  
/opt: 安裝軟體  
/proc: 安裝虛擬文件  
/root: 權限最高的使用者，第一個創建的使用者  
/user: 安裝某個軟體，只對某個使用者，就會把資料放到這個資料夾底下  
/var: 存放臨時文件，紀錄或變動型檔案，數據庫的文件會大量使用  
## Linux系統載入
Linux開機先載入BIOS，檢查硬體設備後，再使用GRUB啟動軟體，最後將Linux 的核心載入，再執行systemD，啟動服務程式
## 使用遠端破解密碼
SSH  
>https://null-byte.wonderhowto.com/how-to/gain-ssh-access-servers-by-brute-forcing-credentials-0194263/

nmap: 可以獲得虛擬機的資訊  
hydra: 可以對帳號密碼使用暴力破解，不過需要提供字典，可以上網使用rockyou.txt  
## 切換kernal使用特定版本的功能
>https://kernel.org/

>https://www.ltsplus.com/linux/rhel-centos-7-compile-kernel
## 專有名詞介紹
集縮比: 一台網路設備(switch)，拉線的比例，拉到user的線比上拉到core network線的比例，多拉幾條線，集縮比也會增加，可以給多個使用者使用(像是社區使用網路)，但是頻寬會降低  
PT test: 滲透(Penetration)測試，幫忙公司做安全檢測，用各種方法入侵，如果入侵成功，就修復  
白帽: 資安公司幫忙找公司的資訊漏洞，可以讓公司相對安全  
黑帽: 就像是駭客，直接打入公司，向公司要錢之類的  
灰帽: 介於黑帽和白帽之間，兩邊偶爾都幹一下  
堆疊(stacking): 會有loop的問題，造成boardcast storm，可以使用STP(Spanning Tree Protocol; 生成樹協定)，把灰色部分變成樹狀結構，解決問題  
## 命令管道
管道可以把一系列命令連接起來，意味著第一個命令的輸出將作為第二個命令的輸入，通過管道傳遞給第二個命令，第二個命令的輸出又將作為第三個命令的輸入，以此類推，並不是所有東西都可以用管道(|)傳輸，有一些需要使用額外指令才可以執行
## 搜尋檔案
>https://blog.gtwang.org/linux/unix-linux-find-command-examples/
## grep
>https://dotblogs.com.tw/xerion30476/2021/05/21/Linux
## 掛載格式硬碟
>https://helloworld.pixnet.net/blog/post/47458574-centos-linux-mount-exfat-%E6%A0%BC%E5%BC%8F%E7%A1%AC%E7%A2%9F
## Shell
寫腳本第一行必須要告訴系統shell在哪裡(shell 一開始要宣告直譯器)  
## 目錄的權限
r: 代表可以列出該目錄的檔案清單，可以利用ls指令立出該目錄的內容  
w: 代表該目錄中的檔案與目錄都可以異動，例如: 寫入檔案、建立、刪除、與更名檔案與目錄  
x: 代表可進入該目錄中，也就使使用cd指令進入該目錄  
## crach
> https://kknews.cc/zh-tw/code/3vpeby3.html
## process
程式在執行時，會把程式碼寫入記憶體，這時在運行的的程式也可以稱作行程，將一顆CPU的可用時間切割為很小的單位，可以讓CPU執行多件工作，因為切換process的時間夠快，所以感覺很像多工  
早期系統最先執行的子行程是init， 不過現在已經被systemd取代，因為init的起動速度較慢，是一個一個啟動，而systemd是使用parallel(相關的行程可以平行生成)，只需要幾個步驟就可以啟動。系統的第一個子行程的PID(process id)為1
## pstree
> https://blog.gtwang.org/linux/linux-pstree-command-tutorial/
## firework
> https://blog.gtwang.org/linux/centos-7-firewalld-command-setup-tutorial/