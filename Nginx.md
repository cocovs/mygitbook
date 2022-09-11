# Nginx

Nginx 是一个免费的、开源的、高性能的 HTTP 和反向代理服务，主要负责负载一些访问量比较大的站点。Nginx 可以作为一个独立的 Web 服务，也可以用来给 Apache 或是其他的 Web 服务做反向代理。相比于 Apache，Nginx 可以处理更多的并发连接，而且每个连接的内存占用的非常小。

## 安装

安装依赖包

```shell
apt-get install gcc
apt-get install libpcre3 libpcre3-dev
apt-get install zlib1g zlib1g-dev
# Ubuntu14.04的仓库中没有发现openssl-dev，由下面openssl和libssl-dev替代
#apt-get install openssl openssl-dev
sudo apt-get install openssl 
sudo apt-get install libssl-dev
```

运行配置文件

````shell
./configure




#结果
Configuration summary

  + using system PCRE library
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
````

安装：

```shell
make
sudo make install
```

查看安装是否成功

```shell
ubuntu@VM-24-7-ubuntu:~/mypackage/nginx-1.22.0$ whereis nginx
nginx: /usr/local/nginx
```











Nginx文件目录：

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/nginx$ ls -ll
total 16
drwxr-xr-x 2 root root 4096 Aug 30 18:11 conf
drwxr-xr-x 2 root root 4096 Aug 30 18:11 html
drwxr-xr-x 2 root root 4096 Aug 30 18:11 logs
drwxr-xr-x 2 root root 4096 Aug 30 18:11 sbin #执行文件放在这里

```



运行Nginx：

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/sbin$ sudo ./nginx
```

访问80端口：

![image-20220830181705587](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220830181705587.png)









### 常用命令

```shell
cd /usr/local/nginx/sbin/

./nginx  #启动
./nginx -s stop  #停止 
./nginx -s quit  #安全退出  保证安全的情况下慢慢退出
./nginx -s reload  #重新加载配置文件
ps aux|grep nginx  #查看nginx进程
./nginx -t         # 测试配置文件是否正确
```

一般配置文件修改后需要使用`./nginx -s reload`重新加载配置文件

相关命令：

```shell
# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop
# 查看防火墙规则
firewall-cmd --list-all
# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 开放80端口
firewall-cmd --permanent --add-port=80/tcp
# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp
#重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload
# 参数解释
1、firwall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；

```







### 修改配置文件

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/conf$ pwd
/usr/local/nginx/conf
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/conf$ sudo vim nginx.conf
```

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220830200709016.png" alt="image-20220830200709016" style="zoom:80%;" />



**检查配置文件是否有错误：**

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/sbin$ sudo ./nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```



保存以后，应该`./nginx -s reload`重新加载配置文件





### 反向代理部署

使用 nginx 作为反向代理来部署我们的程序，按下面的内容修改 nginx 的配置文件。

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        access_log   /var/log/bluebell-access.log;
        error_log    /var/log/bluebell-error.log;

        location / {
            proxy_pass                 http://127.0.0.1:8084;
            proxy_redirect             off;
            proxy_set_header           Host             $host;
            proxy_set_header           X-Real-IP        $remote_addr;
            proxy_set_header           X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
    }
}
```









### 可能出现的错误



**重启时pid失效情况，此时应该`-c`重新指定配置文件**

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/sbin$ sudo ./nginx -s reload
nginx: [error] invalid PID number "" in "/usr/local/nginx/logs/nginx.pid"
```

解决方法：

```shell
# 重新指定nginx.conf文件
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/sbin$ sudo ./nginx -c /usr/local/nginx/conf/nginx.conf
ubuntu@VM-24-7-ubuntu:/usr/local/nginx/sbin$ sudo ./nginx -s reload
```

还有一种解决方式，就是在logs文件夹下创建一个nginx.pid，然后nginx启用默认使用得nginx.pid也可以。