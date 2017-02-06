#写在前面

在没有接触到C和Python之前，我也常用BAT和一堆第三方的exe做事情

年少无知的岁月呀~

----
##  并列语句语法

顺序执行 &

前者正确才执行 &&

前者错误才执行 ||

----
#来一个死循环吧 for

> 用于结束进程，或者DNS查询（买了个域名tmd咋解析还没好

    for /l %i in (1,1,9999999) do ...

----
#结束进程 taskkill

> woc，咋我开了这么多cmd，一个个结束太烦了，不如taskkill一波

    taskkill /f /im cmd.exe
    
----
#内存整理 free

> 微软自己出的一个内存整理工具，需要管理员权限；原理我没搞懂

> exe在download/empty.exe

    empty *

----
#睡一会 SleepX

> 程序需要等待一定时间再继续运行就可以sleepx啦

> exe在download/SleepX.exe，作者Bill Stewart (bstewart@iname.com)

    SleepX 10
    
    REM等待5s，如果用户等不及可以按键，此时 not "%errorlevel%" == "0"
    SleepX -k 5
    
----
#命令行的爬虫 curl

![cURL](https://curl.haxx.se/logo/curl-logo.svg)

> 大名鼎鼎的cURL，不必多言；只是它的命令行的运行方式与libcurl用起来差异很大（如比较php的curl用法）

> 官方：https://curl.haxx.se/

> 简单入门：http://www.bathome.net/thread-1761-1-1.html

> **将curl转为python requests** http://curl.trillworks.com/

[下载7.51 x64版本](download/curl.exe)

```
REM 在bat中REM命令表示注释行

REM 简单的get一下
curl http://ip.cn

REM 保存到文件并断点续传（可以不指定文件名-O）
curl -o iplist.txt -c  http://f.ip.cn/rt/chnroutes.txt

REM POST请求，设置Referer，并使用代理
curl http://httpbin.org/post --data "something=somedata" -H "Referer: http://github.com/zjuchenyuan/" --proxy socks5://127.0.0.1:1080

REM 文件上传 @文件名
REM POST模式下的文件上的文件上传，比如
REM <form method="POST" enctype="multipart/form-data" action="http://cgi2.tky.3web.ne.jp/~zzh/up_file.cgi">
REM <input type=file name=upload>
REM <input type=submit name=nick value="go">
REM </form>
REM 这样一个HTTP表单，我们要用curl进行模拟，就该是这样的语法：
curl -F upload=@localfile -F nick=go http://cgi2.tky.3web.ne.jp/~zzh/up_file.cgi

REM 登录路由器
curl http://192.168.1.1 -u admin:admin

REM 存下Set-Cookie
curl -D cookie0001.txt http://www.yahoo.com

REM 使用存储的Cookie
curl -b cookie0001.txt http://www.yahoo.com

REM dict协议查字典，显示详细的请求信息
curl dict://www.dict.org/d:computer -v
```

----

# BAT的命令们

##判断文件夹存在

```
if exist DIRNAME\nul echo Yes!
```

## 创建硬链接

Win7及以上：
```
mklink /H Link Target
```

目录还需要/J
```
mklink /H /J Link Target
```

WinXP只能用：
```
fsutil hardlink create <new filename> <existing filename>
```

----

## 端口转发

此命令需要管理员权限

```
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=转发出的端口 connectaddress=转发的源IP地址 connectport=转发的源端口
```