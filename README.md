# docker_2-1
# Docker use centos image

### 因為對Linux還不夠熟悉所以想先將 container 裝上所需工具後再 commit 為 image。

### 客製 image 正統作法是透過 dockerfile 來 build 出適合的 image，不是將 container 裝上所需工具後再 commit 為 image

# 實作流程
### 首先先將centos的images從Docker Hub pull 下來

>docker pull centos

### !遇到問題 (Timeout的關係)
>Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

![](https://blog.johnwu.cc/images/x404.png)

![](https://blog.johnwu.cc/images/x405.png)

reset之後便可成功下載

### 執行centos images (產生container/容器)

>docker run -i -t centos

![](https://github.com/a121514191/docker_2/blob/master/centos.PNG)

之後可在裡面下指令

### 我想用這個當基礎來建立環境，所以我先commit他的image(自首不能大寫)

![](https://github.com/a121514191/docker_2/blob/master/lowercase.PNG)

>docker commit 853d first-centos

![](https://github.com/a121514191/docker_2/blob/master/commit.PNG)

### commit完之後便可以看到該名稱在我們的images裡

![](https://github.com/a121514191/docker_2/blob/master/images.PNG)

### 之後我嘗試在裡面安裝http,mysql,apache,php

>docker run -i -t first-centos

>yum install httpd

>systemctl start httpd.service

但在安裝完後httpd後
卡在 systemctl start httpd.service 

![](https://github.com/a121514191/docker_2/blob/master/systemctl.PNG)


### 經查詢後

Docker的設計理念是在容器里面不運行後台服務
正确的使用容器方法是將里面的服务運行在前台

> 解決辦法，以特權模式運行容器

創建容器

> docker run -d --privileged=true first-centos /usr/sbin/init

![](https://github.com/a121514191/docker_2/blob/master/privil.PNG)

進入容器

> docker exec -it centos7 /bin/bash

> systemctl start httpd.service 

![](https://github.com/a121514191/docker_2/blob/master/exec.PNG)

沒跑出錯誤訊息代表成功

### 補充:之前常出現容器錯誤，是因為之前無論對錯，有run這個步驟
### docker ps -a 都會多一個內容
### 所以都查看後先用stop 再用 rm

![](https://github.com/a121514191/docker_2/blob/master/ps.PNG)

![](https://github.com/a121514191/docker_2/blob/master/stop%26rm.PNG)

### 之後會試著將容器環境設定好並且把他打包成image
### 最終目的是想要客製化此image然後成功寫出dockerfile

