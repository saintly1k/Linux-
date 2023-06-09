使用 IPv6 測試網站
=
首先需要確定互聯網連接是否具有IPv6，將電腦網路連接到自己的手機網路，可以使用[測試您的 IPv6 連線](test-ipv6.com)進行測試。接下來在虛擬機中設定linux電腦為橋接器，透過IPV6連上網路上，就輸入linux上ipV6的位置，最後可以使用自己的名稱來去該網域。

連接網路磁碟機
=
安裝所需要的套件包，用來做第三方的軟體資料庫(主要用來支援PHP,Mysql,Nginx)，有了這個更方便安裝一些軟體。   
-
`yum install epel-release`   

進行yum的更新
-
`yum update`

安裝套件包httpd(網站伺服器)
- 
`yum install httpd`

檢測使否有支援dav，確認是否開啟
- 
`httpd -M | grep dav`

創建資料夾
-
`mkdir /var/www/html/webdav`

把擁有者和使用者權限切成apache
-
`chown -R apache:apache /var/www/html`  
`chmod -R 755 /var/www/html`

編輯webdav.conf
-
`vi /etc/httpd/conf.d/webdav.conf`
 ```
    DavLockDB /var/www/html/DavLock
    <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/webdav/
        ErrorLog /var/log/httpd/error.log
        CustomLog /var/log/httpd/access.log combined
        Alias /webdav /var/www/html/webdav
        <Directory /var/www/html/webdav>
            DAV On
            #AuthType Basic
            #AuthName "webdav"
            #AuthUserFile /etc/httpd/.htpasswd
            #Require valid-user
            </Directory>
    </VirtualHost>
 ```

最後再重新啟動httpd
-
`systemctl restart httpd.service`

最後打開windows的網路磁碟機，連上linux的ip位置，輸入  
`http:\\192.168.48.163`

bind
=
一開始dig `@127.0.0.1 www.pchome.com.tw`會no servers could be reached
```
[root@centos7-2 user]# dig @127.0.0.1 www.pchome.com.tw

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7 <<>> @127.0.0.1 www.pchome.com.tw
; (1 server found)
;; global options: +cmd
;; connection timed out; no servers could be reached

```
安裝`yum install bind bind-chroot bind-utils`  

一開始named是關的要將其打開  
`systemctl status named` -> `systemctl start named` -> `systemctl status named`(確認狀態)

試試dig`dig @127.0.0.1 www.pchome.com.tw` 和 `dig @127.0.0.1 www.nqu.edu.tw`

安裝完成`host -t A www.nqu.edu.tw 127.0.0.1`

編輯`gedit /etc/named.conf` 將127.0.0.1 -> any 和 localhost -> any

接著重啟`systemctl restart named`

在 cmd 中打上`nslookup www.nqu.edu.tw 192.168.48.138`

編輯`gedit a.com.zone`加上以下
```
$TTL 600 ;10 minutes
@ IN SOA	@ ramy1231863.gmail.com (
		2021031803 ;serial
		10800      ;refresh
		900        ;retry
		604800     ;expire
		86400      ;minimum
		)
@		NS    dns1.a.com.
dns.com.	A     192.168.56.108
dns1		A     192.168.56.108
www		A     192.168.56.150
eshop		CNAME www
ftp		A     192.168.56.150
abc		A     192.168.56.120
```
編輯 `gedit /etc/named.rfc1912.zones` 在最後增加以下
```
zone "a.com" IN {
	type master;
	file "a.com.zone";
	allow-update { none; };
};
```
重啟named`systemctl restart named`

`host -t A ftp.a.com 127.0.0.1`  
`host -t ns a.com 127.0.0.1`  
`host -t cname eshop.a.com 127.0.0.1`

`gedit /etc/named.rfc1912.zones` 在後面加上
```
zone "56.168.192.in-addr.arpa" IN {
	type master;
	file "56.168.192.in-addr.arpa.zone";
	allow-update { none; };
};
```
編輯`gedit /var/named/56.168.192.in-addr.arpa.zone`
```
@ IN SOA	@ ramy1231863.gmail.com (
		2021031803 ;serial
		10800      ;refresh
		900        ;retry
		604800     ;expire
		86400      ;minimum
		)
56.168.192.in-addr.arpa.    IN  NS dns1.a.com.
56.168.192.in-addr.arpa.    IN  NS dns2.a.com.

200.56.168.192.in-addr.arpa.  IN PTR www.a.com.
150.56.168.192.in-addr.arpa.  IN PTR ftp.a.com.
```
最後將named重新啟動並打`nslookup 192.168.56.150 127.0.0.1`出現結果如下即完成
[root@user]# systemctl restart named  
[root@user]# nslookup 192.168.56.150 127.0.0.1
150.56.168.192.in-addr.arpa	name = ftp.a.com.

 **`named-checkconf a.com /var/named/a.com.conf` : 可以看看是否有出現錯誤  
 `named-checkzone a.com /var/named/a.com.zone` : 可以看看是否有出現錯誤**

docker保存鏡像
=
`systemctl start docker` 先啟動docker

`docker images` 看看現在docker的images

```
[root@localhost user]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    54c9d81cbb44   4 weeks ago    72.8MB
hello-world   latest    feb5d9fea6a5   5 months ago   13.3kB
```
跑起docker
```
 [root@localhost user]# docker run -it ubuntu bash
```
ls，顯示全部
```
root@6d95e0b3d05a:/# ls
bin   dev  home  lib32  libx32  mnt  proc  run   srv  tmp  var
boot  etc  lib   lib64  media   opt  root  sbin  sys  usr
```
切到/home目錄
```
root@6d95e0b3d05a:/# cd /home/
```
ls
```
root@6d95e0b3d05a:/home# ls
```
創建一個文件，裡面加上一些內容
```
root@6d95e0b3d05a:/home# echo "hi" > hi.txt
root@6d95e0b3d05a:/home# cat hi.txt
hi
root@6d95e0b3d05a:/home# ls
hi.txt
```
**ctrl+q,ctrl+p:暫時中離**  

複製一個ubuntu `docker commit 6d9 ubuntu:v1`

docker images
```
[root@ user]# docker commit 6d9 ubuntu:v1
sha256:cb04d1bd239a051ab9317dca052b8a81546485c064f61f631d739a487af21a12
[root@ user]# docker images
REPOSITORY                  TAG       IMAGE ID       CREATED          SIZE
ubuntu                      v1        cb04d1bd239a   11 seconds ago   72.8MB
christina110810505/ubuntu   v1        9d68aacc6a22   14 hours ago     72.8MB
busybox                     latest    829374d342ae   4 days ago       1.24MB
httpd                       latest    faed93b28859   8 days ago       144MB
ubuntu                      latest    54c9d81cbb44   5 weeks ago      72.8MB
hello-world                 latest    feb5d9fea6a5   5 months ago     13.3kB
```
docker stop 6d9  
docker rm 6d9
```
[root@ user]# docker stop 6d9
6d9
[root@ user]# docker rm 6d9
6d9
```

docker run -it cb0  
cd /home  
ls  
`cat hi.txt` 這時候，就可以看到剛剛保留的鏡像
```
[root@localhost user]# docker run -it cb0
root@c762d085a3fc:/# cd /home/
root@c762d085a3fc:/home# ls
hi.txt
root@c762d085a3fc:/home# cat hi.txt
hi
```
在linux開啟終端  
1. docker pull busybox  
2. docker images  
3. docker run -it busybox:latest  
4. docker run -it busybox sh  

在同一台linux新的tab中

5. docker images  
6. docker ps -a  
7. docker exec -it 55c sh

這樣就順利完成在另外一個終端機上開一個tab來連

如何刪除所有執行中的docker
=
現在有需多執行中的docker，如何將其全部刪除
```
[root@ user]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
556b267505cf   busybox   "sh"      18 seconds ago   Up 16 seconds             heuristic_hellman
e9b00a42f21f   busybox   "sh"      51 seconds ago   Up 48 seconds             goofy_gauss
[root@localhost user]# docker ps -a -q
556b267505cf
664d0a5d56c3
e9b00a42f21f
56cf5a3f7fa1
8d89492e5a67
7a176a1f5e29
528fc5d9109c
6adfde82ab6e
9a4f5ee2cc3b
f47288b2e0e1
bec980f1ae4e
f86b4bdbac69
0608baafbd55
c492f623d1d4
e52f0461fde7
``` 
使用指令`docker rm -f $(docker ps -a -q)`，將其刪除乾淨
```
[root@localhost user]# docker ps -a -q
[root@localhost user]# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
將docker pull 及 push
=
`docker images`  
```
[root@ user]# docker images
REPOSITORY                  TAG       IMAGE ID       CREATED             SIZE
christina/ubuntu            v1        9d68aacc6a22   About an hour ago   72.8MB
christina110810505/ubuntu   v1        9d68aacc6a22   About an hour ago   72.8MB
ubuntu                      v1        9d68aacc6a22   About an hour ago   72.8MB
busybox                     latest    829374d342ae   4 days ago          1.24MB
httpd                       latest    faed93b28859   7 days ago          144MB
ubuntu                      latest    54c9d81cbb44   5 weeks ago         72.8MB
hello-world                 latest    feb5d9fea6a5   5 months ago        13.3kB
```
用httpd連線docker
=
netstat -tunlp | grep 80

docker run -d -p 8888:80 httpd(第一個數字是本地端)
ifconfig  

之後再windows和linux上分別輸入ip位址  
for linux : ` curl 192.168.48.175:8888`  
for windows : `http://192.168.48.175:8888/`

寫腳本將docker自動新增，自動刪除
=
新增自動新增的腳本`gedit docker_httpd_setup.sh`，在其中加上以下腳本
```
#!/usr/bin/bash
for i in {1..5}
do
    portno=`expr 9000 + $i`
    docker run -d -p $portno:80 httpd
done
```
給其可執行直權線`chmod +x docker_httpd_setup.sh `

執行`./docker_httpd_setup.sh`

`docker ps -a`看所有執行

創造自動刪除的腳本`gedit docker_httpd_teardown.sh`，加入以下
```
#!/usr/bin/bash

docker rm -f $(docker ps -a -q)
```
給其可執行職權線`chmod +x docker_httpd_teardown.sh `

`./docker_httpd_teardown.sh`，執行完後就看到images中沒東西了
```
[root@localhost user]# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
使用`docker exec -it 953 bash`進入，並且進入到htdocs資料夾中，就可以看到剛剛所mapping的
```
[root@localhost user]# docker exec -it 953 bash
root@9537447f1800:/usr/local/apache2# cd htdocs/
root@9537447f1800:/usr/local/apache2/htdocs# ls
hi.htm
root@9537447f1800:/usr/local/apache2/htdocs# cat hi.htm
ho
```
透過load balance做web server
=
建立一個腳本`gedit prepare_web.sh`這個腳本主要是讓他自動產生web，並將每個web中加上hi.htm，其中`-p`是當出現過就不會重複產生 
    
 ```
    #!/usr/bin/bash
    for i in {1..5};
    do 
        mkdir -p myweb$i
        cd myweb$i
        echo $i > hi.htm
        cd .. 
    done
 ```
`chmod +x prepare_web.sh` 增加可執行的權限

`./prepare_web.sh` 執行可以看到結果

編輯掛載資料夾docker_httpd_setup.sh `gedit docker_httpd_setup.sh`

```
    #!/usr/bin/bash
    for i in {1..5};
    do
        portno=`expr 9000 + $i`
        docker run -d -p $portno:80 -v /home/user/myweb$i:/usr/local/apache2/htdocs httpd
    done
```
打開docker: `systemctl start docker`

先刪除所有行程後執行程式

``` 
docker rm -f `docker ps -a -q`
```
```
[root@t user]# docker rm -f `docker ps -a -q`
69e6d0d489df
e78b0e1cfb09
40ce5a1f8171
f3870b3870d7
458924792f83
c762d085a3fc
ec47c98158bd
9537447f1800
2bbb4672f4a9
```
執行結果
```
[root@ user]# docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@localhost user]# ./docker_httpd_setup.sh 
adc8b24be481d38fca8c3c91822f5676717c5b85d200b53a0add8de7866c9884
251dcc4c9652313a2fc32f31eb2ea9e777eb1aa9475bc96de008b781f4ca9092
9b5255c9578ceed47cf5c907cb1cde7f5e22bd7554c7c1804a975478b4c07fe3
a1e0cee45c2b4ab60a2cda3b174752da910f4e01300de80f53ba40f22ae094f4
be5ace8b3cd1c03b8484e9c30ac705de07e4c21401fdf58e377101d63809d6b1
```
執行`./docker_httpd_setup.sh `結果
```
[root@localhost user]# ./docker_httpd_setup.sh 
adc8b24be481d38fca8c3c91822f5676717c5b85d200b53a0add8de7866c9884
251dcc4c9652313a2fc32f31eb2ea9e777eb1aa9475bc96de008b781f4ca9092
9b5255c9578ceed47cf5c907cb1cde7f5e22bd7554c7c1804a975478b4c07fe3
a1e0cee45c2b4ab60a2cda3b174752da910f4e01300de80f53ba40f22ae094f4
be5ace8b3cd1c03b8484e9c30ac705de07e4c21401fdf58e377101d63809d6b1
```
執行行程`docker ps`
```
[root@localhost user]# docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS                                   NAMES
be5ace8b3cd1   httpd     "httpd-foreground"   10 minutes ago   Up 10 minutes   0.0.0.0:9005->80/tcp, :::9005->80/tcp   crazy_lehmann
a1e0cee45c2b   httpd     "httpd-foreground"   10 minutes ago   Up 10 minutes   0.0.0.0:9004->80/tcp, :::9004->80/tcp   upbeat_hermann
9b5255c9578c   httpd     "httpd-foreground"   10 minutes ago   Up 10 minutes   0.0.0.0:9003->80/tcp, :::9003->80/tcp   romantic_hopper
251dcc4c9652   httpd     "httpd-foreground"   10 minutes ago   Up 10 minutes   0.0.0.0:9002->80/tcp, :::9002->80/tcp   funny_chaplygin
adc8b24be481   httpd     "httpd-foreground"   10 minutes ago   Up 10 minutes   0.0.0.0:9001->80/tcp, :::9001->80/tcp   peaceful_stonebraker
```
安裝 docker-compose
=
下載stable release的docker-compose binary:  
curl -L   
"https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

給予執行權限:  
chmod +x /usr/local/bin/docker-compose

測試安裝是否完成:  
docker-compose --version

Centos編譯核心
=
kernel穩定安裝版  
`wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.19.237.tar.xz`

解壓縮`tar xvJf linux-4.19.237.tar.xz`
```
[root@localhost linux-4.19.237]# uname -r
3.10.0-1160.el7.x86_64
[root@localhost linux-4.19.237]# cp -v /boot/config-3.10.0-1160.el7.x86_64 .config
‘/boot/config-3.10.0-1160.el7.x86_64’ -> ‘.config’
```
jumpservere send gmail
=
```
cd Dockerfile
docker-compose -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose.yml up
```
grep之用法
-
```
[root@ user]# cat test 
aaa
bbbbb
AAAaaa
BBBBASDABBDA
[root@ user]# grep -A2 b test
bbbbb
AAAaaa
BBBBASDABBDA
[root@ user]# grep -B1 b test
aaa
bbbbb
[root@ user]# grep -C1 b test
aaa
bbbbb
AAAaaa
[root@ user]# grep -c aaa test
2
[root@ user]# grep -e AAA -e bbb test
bbbbb
AAAaaa
[root@ user]# grep -in b test
2:bbbbb
4:BBBBASDABBDA
[root@ user]# grep -o ASDA test
ASDA
[root@ user]# grep -q aa test
[root@ user]# echo $?
0
[root@ user]# grep -v aaa test
bbbbb
BBBBASDABBDA
[root@ user]# grep -w aaa test
aaa
[root@ user]# cat grep.txt
cat: grep.txt: No such file or directory
[root@ user]# vi grep.txt
[root@ user]# cat grep.txt 
aaa
[root@ user]# grep -f grep.txt test
aaa
AAAaaa
```
zabbix
=
ansible playbooks
=
安裝 `yum install -y ansible`

編輯 `/etc/ansible/hosts` 來設置群組
```
[app1]
192.168.56.102

[app2]
192.168.56.104

[myapp]
192.168.56.102
192.168.56.104
```
重新啟動 sshd
```
systemctl restart sshd
```
ansible 的用法
```sh
ansible all --list-hosts   列出當前所有管理的機器
ansible app1 --list-hosts  列出 app1 群組所管理的機器
ansible app2 --list-hosts  列出 app2 群組所管理的機器
ansible myapp --list-hosts 列出 myapp 群組所管理的機器
```
格式
可用 `[01:10]` 代表 01 ~ 10  
如 `192.168.73.[01:10]` 代表 `192.168.73.01` ~ `192.168.73.10`

常用選項  
`-m`, module  
`C`, check：檢查指令是否正確  
`-v`, `-vv`, `-vvv`：顯示詳細過程（由易到難）

`rpm -q vsftpd` 查看是否安裝 `vsftpd` 套件 (q:query)
```
ansible app1 -m command -a "rpm -q vsftpd"
```
查看使用者 `user` 是否存在
`getent`：
```bash
ansible app1 -m command -a "getent passwd user"  以"X"隱藏密碼
ansible app1 -m command -a "getent shadow user"  查看加密後的密碼
```
`shell`複雜的指令建議採用此模組  
`creates`當檔案存在時，不執行後面的指令
```
ansible app1 -m shell -a "creates=/tmp/test ls /tmp"
```

`removes`當檔案存在時，執行後面的指令
```
ansible app1 -m shell -a "removes=/tmp/test ls /tmp"
```
