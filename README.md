由于需要查一些资料，但是google死活上不去，几天前找到的免费SS账号好像突然用不起了，一怒之下，自己买了搬瓦工VPS，自己搭建了一个SS的梯子。

我觉得有必要说一下，如果你是开发人员，你是需要一个属于自己的VPN的，而不是买的。如果你只是偶尔看看外面的世界，那么无所谓，找找免费的SS帐号就行了。然后不推荐去买帐号，买的帐号，贵的不划算，便宜的，各种限制，比如每个月限制流量，限制登陆的客户端个数，同时登陆客户端个数等等。我也买过一次半年的SS帐号，结果用了不到三个月，就用不了了，商家跑路了，这是最纠心的。不是因为钱，而是因为又要重新找地方买帐号，整天写代码都那么辛苦了，还要花时间来搞这些毫无意义，毫无营养的事情，真的很烦人。所以，推荐开发人员，需要上google等查资料的，都自己搭一个梯子。

# 一：购买搬瓦工VPS

就只是为了翻个墙，我们就买最便宜的就好了$19.99，人民币140不到，这是一个优惠码IAMSMART5FQ956，可以少几块钱，当然，优惠码你可以通过在搬瓦工首页，显示源代码的方式找到，优惠多少看自己的运气。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/1.jpg)

购买流程如下：

## 1.进入搬瓦工购买页面，Billing Cycle选择购买的时间，Location选择洛杉矶机房，然后add to cart。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/2.jpg)

## 2.加载完成后，输入优惠码，点击Validate Code，可以看到优惠了多少，再点击Checkout。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/3.jpg)

## 3.依照图片填入内容。然后Update，通过过后选择Credit Card and AliPay(Stripe)，勾选同意服务，再点击Complete Order。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/4.jpg)

## 4.点击pay now，然后选择支付宝支付，支付完成后你注册的邮箱会收到支付完成的邮件。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/5.jpg)

# 二：开始搭梯子

## 1.进入搬瓦工首页，点击右上角client area按钮，输入刚才注册的邮箱和密码。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/6.jpg)

## 2.加载完成后，选择Services->my services->KiwiVM Control Panel，进入管理页面。
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/7.jpg)

## 3.
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/8.jpg)

该页面可以看到IP地址，物理地址，端口号，硬盘使用情况，流量使用情况等等。我们需要记住的东西有IP地址，端口号，以及root密码（Main controls->Root Password modification可以随机生成）。

默认的系统是centos-6，如果需要修改，先在Main controls停止后，然后在Install new OS里安装，很快。

我选择的系统是ubuntu-15.10-x86_64。尽量不要选精简版本，因为精简版有很多命令因为环境变量的原因无法使用，比如sudo等命令。

如果想快速的搭好梯子，使用KiwiVM Extras下的Shadowsocks Server就可以自动安装了。没有试过是什么效果，但不建议这么做。



## 4.如果是选择了自动安装的，可以跳过此步骤。

### 4.1打开终端（我用的MAC电脑），使用SSH命令连接VPS。
```
ssh -p 23333 root@11.11.11.11

ssh -p 端口号 root@IP地址
```
连接成功后，输入密码，就是刚才随机生成的root密码。

### 4.2进入成功后，可以先把root密码给修改了，我最开始就忘了修改，然后后面给忘了，又要去重新生成，挺郁闷的。
```
sudo passwd root
```
然后输入两次新密码即可。

### 4.3然后安装python版本的SS服务端：

首先，我们选的系统中已经有python了，所以无需安装python，但是我们需要先安装pip：
```
wget "https://pypi.python.org/packages/source/p/pip/pip-1.5.4.tar.gz#md5=834b2904f92d46aaa333267fb1c922bb" --no-check-certificate

tar -xzvf pip-1.5.4.tar.gz

cd pip-1.5.4

python setup.py install
```

最后这一步会报错：ImportError: No module named 'setup tools'

我们需要修复该错误：
```
wget http://pypi.python.org/packages/source/s/setuptools/setuptools-0.6c11.tar.gz

tar zxvf setuptools-0.6c11.tar.gz

cd setuptools-0.6c11

python setup.py build

python setup.py install
```

此时setup tools安装好了，我们返回上一层目录文件夹内，再次使用install安装好pip。

```

cd ..

python setup.py install

```
pip安装好后，我们安装shadowsocks。
```
pip install shadowsocks
```
### 4.4增加服务端配置文件
```
cd /etc

vi shadowsocks.json
```
进入vi编辑器后，输入i进入输入模式，复制如下格式进去。（请按照自己的实际数据修改参数）

单用户：
```

{

"server":"your_server_ip",

"server_port":8388,

"password":"yourpassword",

"timeout":300,

"method":"rc4-md5",

"fast_open":false,

"workers": 1

}
```
多用户：
```
{

"server":"your_server_ip",

"port_password":{

"8381":"pass1",

"8382":"pass2",

"8383":"pass3",

"8384":"pass4"

},

"timeout":300,

"method":"rc4-md5",

"fast_open":false,

"workers":1

}
```
各字段含义：

server：服务器 IP地址 (IPv4/IPv6)

server_port：服务器监听的端口，一般设为80，443等，注意不要设为使用中的端口

password：设置密码，自定义

timeout：超时时间（秒）

method：加密方法，可选择 “aes-256-cfb”, “rc4-md5”等等。推荐使用 “rc4-md5”

fast_open：true 或 false。如果你的服务器 Linux 内核在3.7+，可以开启 fast_open 以降低延迟。

workers：workers数量，默认为 1

编辑完成后，按下键盘esc键，然后输入:wq保存退出vi。

### 4.5启动SS服务：
```
ssserver -c /etc/shadowsocks.json -d start
```
### 4.6停止SS服务：
```
ssserver -d stop
```
# 三：客户端配置

客户端，下载好，按照刚才的配置填入就可以了。

如下：
![image](https://github.com/flywo/CreateShadowsocksServer/blob/master/9.jpg)

然后你可以试试：谷歌香港https://www.google.com.hk，能打开，那恭喜你了。打不开，先看看SS是否打开状态，然后检查SS服务器配置是否正确。
