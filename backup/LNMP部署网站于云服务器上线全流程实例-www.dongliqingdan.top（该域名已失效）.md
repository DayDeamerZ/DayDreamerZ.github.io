# 准备工作
## 1、云服务器：阿里云轻量级服务器
原生配置：（购买服务器时勾选的自带配置）
应用配置：

![Image](https://github.com/user-attachments/assets/6f932dfd-04eb-4b56-ae74-66ef92cb9d8c)

![Image](https://github.com/user-attachments/assets/abe2ec39-5fec-44dc-b59c-0a21188a23eb)

![Image](https://github.com/user-attachments/assets/3560ffe6-3067-4daa-8765-4974d8775688)

系统配置：

![Image](https://github.com/user-attachments/assets/ba351199-6a93-4c9d-b56a-98be604f3131)

## 2、域名
购买域名：
域名DNS解析：
域名备案：

## 3、服务器上线部署软件/平台
目前了解到有：
宝塔面板：操作简便，具有远程连接管理服务器的功能，镜像商店齐全，部署上线多次未果，暂时不推荐
Nginx：是一种轻量级的http服务器，也是反向代理服务器，总之用起来有些复杂，不推荐
tomact: 比较流行的Web 应用服务器, 实际上Tomcat是Apache 服务器的扩展，但运行时它是独立运行 ,  一般不作为部署普通html网页使用， 不推荐
httpd：是apache软件包的名字，apache是超文本传输协议的主程序（http协议就是网站，端口默认80），配置文件简单易懂，推荐

# 网页部署上线流程：
## 1、准备好需要部署的文件
对于我们使用的httpd方法来说，每一个html文件需要其对应的css文件与之处于同一级别目录中，js文件、resouse图片集同理如此。

![Image](https://github.com/user-attachments/assets/f33d1374-943a-4353-a74e-148ba2c108f8)

因此，在前期书写代码时应提前有此布局准备，将所有页面分开成多个子文件夹，资源、css文件或js文件单独配置。（目前建议如此，后续可能找到更好的方法2022.12.1）

![Image](https://github.com/user-attachments/assets/9b6c307a-c6e9-474f-9533-fc1bdefc9700)
（找到了更好的排列方式2022.12.6）

## 2、安装httpd
```
yum install httpd -y
```

只要没看到什么wrn、failed、error什么的，基本就是没问题
httpd安装后 /var文件夹中会多出来个/www/html 子目录，这个子目录就是以后我们用来部署html、css文件的地方。
而httpd的配置文件位置则在/etc/httpd/conf/httpd.conf  配置文件就是用来控制httpd运行配置的地方
这些目录的创建位置都是默认的，基本不会有不同的地方

## 3、把准备好的部署文件塞到/var/www/html这个目录中去
注意！一定要保证所有内容都在/html中 不能给/html整个同级的/css文件夹出来，没有用的
配置文件的设置默认判断部署的内容都在/html中识别，除非你想把配置文件中那么多正则表达式慢慢换掉，否则最好给放/html里面
这里我们只放了首页的文件，假如需要放入多个页面，可以在/var/www/html目录下设置多个子目录，每一个子目录都代表这一个完整的页面内容
如/var/www/html/html01   /var/www/html/html02   …………etc
然后再在这些html010203子目录下放入对应的html、css、js和所用到的图片文件
上传途径：使用xftp软件远程连接服务器

## 4、更改httpd配置文件
```

ServerRoot "/etc/httpd"
Listen 80
Include conf.modules.d/*.conf
User apache
Group apache
ServerAdmin root@localhost

<Directory />
    AllowOverride none
    Require all granted
</Directory>

ServerName localhost:80
DocumentRoot "/var/www/html"

<Directory "/var/www">
    Options Indexes FollowSymLinks
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

<Files ".ht*">
    Require all denied
</Files>

ErrorLog "logs/error_log"
LogLevel warn

<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
<IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
</IfModule>
    CustomLog "logs/access_log" combined
</IfModule>

<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>

<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>

<IfModule mime_module>
    TypesConfig /etc/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>

AddDefaultCharset UTF-8

<IfModule mime_magic_module>
    MIMEMagicFile conf/magic
</IfModule>

EnableSendfile on

<VirtualHost *:80>
ServerName dongliqingdan.top
ServerAlias www.dongliqingdan.top
DocumentRoot /var/www/html
</VirtualHost>
IncludeOptional conf.d/*.conf
```
ServerRoot：看后面内容就知道了，应该是说配置文件的路径的
Listen：指的是监听的端口范围，默认值80
其他的不用管

```
<Directory />
    AllowOverride none
    Require all granted
</Directory>

<Directory "/var/www">
    Options Indexes FollowSymLinks
    AllowOverride None
    # Allow open access:
    Require all granted
</Directory>

<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

/ 那个代表不管什么文件夹中的配置都是如下
同理，/var/www将配置内容限制在了此目录内
这里要说到，我们利用httpd上线网页的方式使用的是设置一个虚拟host的方式，所以不管是哪个目录位置，咱们只要保证有这三个在就行

```
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
```

第三个Require all granted最重要，代表我们获取了改动的权限，如果是Require no granted，一定要记得改掉，不然后续内容无法实现
像这里，我们自然是找到对应路径为"/var/www/html"的<Directory>标签，然后将里面的内容改成如上所示。

```
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>

<Files ".ht*">
    Require all denied
</Files>

ErrorLog "logs/error_log"
LogLevel warn

<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
<IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
</IfModule>
    CustomLog "logs/access_log" combined
</IfModule>

<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
```
像这里面长成这样的，最好都不要碰，别改就是了
唯一值得注意下的就是

```
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
```

表示默认网页首页文件为：index.html，设置好了目录路径后会自动查询其中对应的文件名称
个人建议就按照index这个来，乱改容易出问题

```
DocumentRoot "/var/www/html"

<VirtualHost *:80>
ServerName dongliqingdan.top
ServerAlias www.dongliqingdan.top
DocumentRoot /var/www/html
</VirtualHost>
```
默认的部署文件内容目录路径，如果有很多html010203这类子文件夹，应该要在其后下移一位成/var/www/html/html01（html01是网页首页文件夹）

除了这些外，整个文件中，唯一需要我们手动加上的语句就是

```
ServerName localhost:80

<VirtualHost *:80>
ServerName dongliqingdan.top
ServerAlias www.dongliqingdan.top
DocumentRoot /var/www/html
</VirtualHost>
```

ServerName localhost:80
注意！
这一语句后面的端口号，才是我们真正需要改写的端口号。
假如我们想不通过80端口域名访问，而是直接使用服务器公网ip加上自定义端口号访问网页
那么我们可以境这句话改为
ServerName localhost:你自定义的端口号
如
ServerName localhost:9999 （注意，请确保你自定义的端口未被占用）
此时就算是前面的
Listen:80的端口范围不动，仍为80也不会影响你的自定义端口访问

我们需要使用域名访问（http），因此这里保持默认的80端口范围

<VirtualHost *:80>
ServerName dongliqingdan.top
ServerAlias www.dongliqingdan.top
DocumentRoot /var/www/html
</VirtualHost>

到这里，我们才开始真正设置了我们的虚拟host的配置。
*：后面的是前面ServerName localhost所设置的端口值，这里仍是80二者请确保端口一致

ServerName dongliqingdan.top
表示访问的一级域名

ServerAlias www.dongliqingdan.top
表示访问的二级域名

DocumentRoot /var/www/html
表示该虚拟主机通过一二级域名访问后，将会跳转到的html文件所在查找路径

倘若设置了html010203子目录，那么此处应改写为
DocumentRoot /var/www/html/html01
或
DocumentRoot /var/www/html/html02
……


到这里就修改完毕了。
若使用xftp软件修改，保存后关闭即可。
若使用命令 vi /etc/httpd/conf/httpd.conf 
规则如下
i键，进入读写模式
esc键，退出读写模式
:wq！  保存并退出
:q       直接退出

注：若想要使用http80端口外的自定义端口连接到网页，就不必再新加<VirtualHost>标签，只需要给加个SeverName localhost：9999就行了，因为就算加了也没有用，使用域名的前提是使用http80端口
此时直接使用公网ip加:9999就能够访问了
例如：642.482.936.145:9999
并且，这种方式只适用于自定义端口，80端口是无法用这种方式访问“：”后面加80是不奏效的

## 5、检查配置文件内容语法并重新启动httpd
检查语法
```
httpd -t
```
可能会出现一到两个错误，此时建议自己搜解决方法
本人遇到两个错误
AH00548: NameVirtualHost has no effect and will be removed in the next release /usr/local/apache/conf/extra/httpd-vhosts.conf:1
 解决方法：原因是apache-2.4.x版本后的NameVirtualHost *:80这句已经多余，无需写入配置文档，删除即可
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using localho
解决方法：原因就是端口无法使用，大概率是被其他程序占用了
在root权限下命令行输入代码
```
netstat -lnp | grep 80
```
查看端口80的使用情况，若指定了别的端口自己改
tcp 0      x x.x.x.x:8084        ...   listen   1167/mono
tcp 0      x x.x.x.x:80          ...   listen   1194/nginx
udp 0      x x.x.x.x:2080        ...   listen   14427/drcomauthsvr
可以看到是线程为1194的nginx程序占用了80端口，给他杀掉就行了
```
kill 1194
```
最后重启httpd就大功告成了
```
systemctl restart httpd
```

---------------------------------分割线 2022/12/5------------------------------
经过后续页面的添加过程发现
/var/www/html 目录中的文件/文件夹先后规则如下：

# 一、
在配置文件中设置的默认html查找路径（也就是这里的/var/www/html），所需展示的页面html文件应在此路径的第一级子目录下，也就是 /var/www/html/index.html ("配置文件中设置的查找路径"/"首页.html")
即，index.html不得再向下成为二级子目录中的文件，否则无法查找到。

# 二、
httpd配置文件的查找是以index.html文件为“锚点”向下定位的。
举个例子：

![Image](https://github.com/user-attachments/assets/e45ad554-3c5d-428d-b72d-db75f156d329)

这样的文件夹配置方式是比较合理的类型
注意：若想要html、js、css文件夹里的文件能够被引用到，必须要满足自身位置小于或等于index.html锚点文件所在位置
像这里，html、js、css文件夹与index.html同级，能够成功引用

![Image](https://github.com/user-attachments/assets/a7f22bb9-4d75-4ea5-a555-8a2c582c4ea6)

这种同理，位置小于index.html文件，能够被向下搜寻到

（作为锚点文件，index.html必须被放在查找路径下的第一级目录，但是其他html、css或js文件没有这个要求，同样遵循小于或等于的原则）

下图是错误示范

![Image](https://github.com/user-attachments/assets/2b0a7cd6-ed02-42fd-960f-85fdbb12d9d7)

查找到index.html文件后，无法向上引用其他文件夹的内容，因此会导致可以跳转到其他html文件的页面，但是却没有任何css样式或者js功能，引用png、jpg图片也是同样的道理。

