## Day 5 - 運行 minikube

### 本日共賞

* 運行 minikube
* minikube 常用指令

### 希望你知道
* [安裝 k8s](https://ithelp.ithome.com.tw/articles/10192748)

<br/>
#### 運行 minikube

安裝好 minikube 後，只需要透過指令便可啟動 k8s 叢集，指令如下

```
$ minikube start
Starting local Kubernetes v1.8.0 cluster...
Starting VM...
Downloading Minikube ISO
 140.01 MB / 140.01 MB [============================================] 100.00% 0s
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
```

第一次啟動 minikube 時，會花費一點時間下載需要的 ISO 檔。minikube 啟動的同時，會一併把 kubectl 需要的憑證 (credentials) 配置好。以便之後可以透過 kubectl 來對 minikube 進行操作。

還記得昨天安裝完 kubectl 後檢查版本時候的錯誤嗎？minikube 啟動後再執行一次取得版本指令：

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.2", GitCommit:"bdaeafa71f6c7c04636251031f93464384d54963", GitTreeState:"clean", BuildDate:"2017-10-24T19:48:57Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.0", GitCommit:"0b9efaeb34a2fc51ff8e4d34ad9bc6375459c4a4", GitTreeState:"dirty", BuildDate:"2017-10-17T15:09:55Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}
```

你就會發現原本 `localhost:8080 was refused ...` 的錯誤消失了，取代的是目前 Server 端的資訊。

另外，如果你想要指定 k8s 的版本，例如 `v1.7.3` 可以用下列指令

```bash
$ minikube start --kubernetes-version v1.7.3
```

如果你想查看 minikube 支援哪些 k8s 版本：

```bash
$ minikube get-k8s-versions
The following Kubernetes versions are available when using the localkube bootstrapper: 
	- v1.8.0
	- v1.7.5
	- v1.7.4
	- v1.7.3
	- v1.7.2
	- v1.7.0
	- v1.7.0-rc.1
...
```

這樣你就可以知道有哪些版本可以使用。

#### minikube 常用指令

**1. 檢查 minikube 版本**

```
$ minikube version
minikube version: v0.24.1
```

**2. 檢查 minikube 狀態**

```
$ minikube status
minikube: Running
cluster: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.99.100
```

由上述訊息得知，minikube 已經順利運行並被配置 ip 192.168.99.100。

>可以試試看當 minikube 停止後會出現什麼訊息

**3. 取得 ip**

```
$ minikube ip
192.168.99.100
```

`minikube ip` 可以取得 minikube 運行時的 IP Address

> 當需要存取 Node 的時候就需要 IP Address，之後如果需要用到會再提示


**4. SSH 連線**

minikube 建立的虛擬機你可以把它想像成一台運行 Linux 的電腦，所以你可以透過 SSH 的方式連到這台電腦，指令如下：

```bash
$ minikube ssh
                         _             _            
            _         _ ( )           ( )           
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __  
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

$ cat /proc/version   <=== 查看 Linux 版本
Linux version 4.9.13 (jenkins@jenkins) (gcc version 5.4.0 (Buildroot 2017.02) ) #1 SMP Thu Oct 19 17:14:00 UTC 2017
```

查看 `/proc/version` 可以知道虛擬機運行的 Linux 版本。

還記得我們在 [第一天](https://ithelp.ithome.com.tw/articles/10192193) 提到過 k8s 使用 Docker 當作預設的容器環境，因此你可以在這裡使用 Docker 的指令查看相關內容：

```bash 
$ docker version   <=== 查看 Docker 版本
docker version
Client:
 Version:      17.06.0-ce
 API version:  1.30
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:15:15 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.06.0-ce
 API version:  1.30 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:51:55 2017
 OS/Arch:      linux/amd64
 Experimental: false
 
$ docker ps   <=== 查看運行的容器
CONTAINER ID        IMAGE                                                            COMMAND                  CREATED             STATUS              PORTS                                                                NAMES
147e39f9d23a        nginx                                                            "nginx -g 'daemon ..."   9 minutes ago       Up 9 minutes                                                                             k8s_autossl-nginx_autossl-nginx-85b86879fb-585gr_default_0c5f8d94-e494-11e7-931e-080027641f6a_1
b7031331e3a2        gcr.io/google_containers/defaultbackend                          "/server"                9 minutes ago       Up 9 minutes                                                                             k8s_default-http-backend_default-http-backend-wkvbd_kube-system_534c694b-e488-11e7-931e-080027641f6a_1
...
```

當你利用 `docker ps` 查看正在運行的容器，你就會發現一大堆 k8s 相關的容器正在運作。

> 看到這麼多容器在運作，會不會嚇一大跳呢？還記得我們在 [k8s 安裝須知](https://ithelp.ithome.com.tw/articles/10192448) 有提到過，k8s 是可以手動安裝的，基本上就是安裝你看到的這些。

如果要離開，就輸入 `exit` 指令即可。


**4. Dashboard 介面**

```
$ minikube dashboard
```

執行上述指令後，minikube dashboard 便會被啟動。我們可以透過 dashboard 來監測 k8s 叢集內的運作狀態。Dashboard 畫面如下

![https://ithelp.ithome.com.tw/upload/images/20171223/20107062eL29DBednW.png](https://ithelp.ithome.com.tw/upload/images/20171223/20107062eL29DBednW.png)

>還記得與 k8s 溝通的三種方式嗎？ Dashboard 就是其中一種，我們會在之後的文章詳細說明。

**5. 刪除 k8s 叢集**

```
$ minikube delete
Deleting local Kubernetes cluster...
Machine deleted.
```

透過 delete 命令，minikube 會把 k8s 叢集刪除。而下次啟動 minikube 會重新建立叢集。

>有時候，系統被弄的亂七八糟的時候，把整個叢集刪除是個不錯的方法。但是系統正式上線以後，這樣的機會可能就不多了。

**6. 停用 minikube**

```
$ minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
```

想知道更多，可以參考[這裡](https://github.com/kubernetes/minikube/tree/master/docs)


本文同步發表於 [https://jlptf.github.io/ironman2018-day5/](https://jlptf.github.io/ironman2018-day5/)
