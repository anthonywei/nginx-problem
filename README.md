# nginx-problem
记录nginx使用上面的一些问题，nginx在同时支持http和https问题，移动浏览器部分图片不显示

这不是一个项目，而是记录最近在使用nginx的时候出现的”奇怪现象“

首先我的网站打算采用https来全站使用，第一个解决的就是http访问的时候自动跳转到https

解决方案典型的：rewrite或者301 return，如下
server {
    listen       80;
    server_name  xxx.com;
    rewrite ^(.*)$ https://xxx.com/$1 permanent;
}

这样存在一个问题，当我访问xxx.com的时候，是会跳转到https://xxx.com的，但是在访问xxx.com/aaa/bb的时候，就不会跳转了

虽然按照nginx的配置文件写法，这么看理论上不会有这种情况，但是实际使用就是会出现

然后，第二个问题来了，我想我的网站同时支持http和https，按照一般做法，我做了两个配置文件

default.conf
ssl.conf
两个文件内容大致相同，只是default配置80端口，ssl配置443和ssl，结果奇怪的问题出现了，我们在使用移动QQ浏览器访问网站时

某些图片在http协议下无论怎么刷新就是不显示，图片不大，网略也很好，但是用https协议去访问相同的页面时，就可以显示图片

这个问题我们测试了safari，QQ浏览器，UC web，最后就是在QQ浏览器有问题，每次都是。

实在是无语，手机QQ浏览器各种调试困难。

最后试了下，把default.conf和ssl.conf合并到一个文件

如下：
server {
    listen 80;
    listen       443 ssl;
    server_name xxx.com;
    
    //其他内容
    //...
  }
  
再试，就可以了，呃，所以参考之前自己在运维nginx的时候，各种rewrite的奇怪现象，这些现象的共同点是，解释的不够清楚

或者说理论说不通，先记录下，给需要的人。

另外nginx的不同版本对配置文件的一些细小的变动挺敏感的，例子不举了，用过的自然知道

