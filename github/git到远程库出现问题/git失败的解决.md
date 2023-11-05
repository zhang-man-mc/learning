1. # **push到远程仓库不能连接到该端口**

   ** **`Failed to connect to 127.0.0.1 port 46189 after 2021 ms: Couldn't connect to server`****



![69906514071](C:\Users\19125\AppData\Local\Temp\1699065140717.png)



**原因 :**commit不上去时，我将git的全局代理设置为了与网络状态 --代理--手动设置代理--端口一样。而下次重启greenhub时该端口自动变了，而git的全局代理端口还是上一次那个。



**解决**：取消git的全局代理

git config --global --reset http.proxy

git config --global --reset https.proxy