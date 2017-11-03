# IMAP-Mail-Transfer
Ubuntu自动收取imap邮件根据规则进行转发的配置示例

## 1.需要软件

1. getmail
2. msmtp
3. procmail
4. sendmail

### getmail

> 作用

收取imap邮件

> 安装方法:

参考: http://pyropus.ca/software/getmail/documentation.html#installing

> 配置文件

文件路径: ~/.getmail/getmailrc

``` shell
[options]
read_all = false
delete = false
message_log = ~/.getmail/getmail.log

[retriever]
type = SimpleIMAPSSLRetriever
server = imap.mail.com
username = username
password = password
port = 993
ssl_version = sslv23
ca_certs = /etc/ssl/certs/xxxxxx.pem

[destination]
type = MDA_external
path = /usr/bin/procmail
```

> 说明

ca_certs参数用来配置imap服务器的证书路径。ubuntu系统的CA证书通常保存在`/etc/ssl/certs/`目录下，可以通过下述命令来判断服务器使用哪个Root证书，然后在证书目录中找到相应证书

```
openssl s_client -showcerts -connect imap.mail.com:993 < /dev/null 2>/dev/null | grep '^ *i:' | tail -n 1 
```

### msmtp

> 作用

发送邮件

> 安装方法

```
sudo apt install msmtp
```

> 配置文件

文件路径: ~/.msmtprc

```
account main
host smtp.mail.com
from "xxxx@mail.com"
auth on
user "xxxxx"
password "xxxxx"
logfile ~/.getmail/msmtp.log

account default : main
```

### procmail

> 作用

用来处理邮件规则

> 配置文件

文件路径: ~/.procmailrc

```
PATH=/bin:/sbin/:/usr/bin:/usr/sbin
SHELL=/bin/bash
MAILDIR=~/.getmail/mail/
DEFAULT=$MAILDIR
LOGFILE=~/.getmail/procmail.log
VERBOSE=yes

:0
* ^From.*@gmail.com
! xxxxx@gmail.com
```

> 说明

具体规则参考procmail的文档

### sendmail

> 作用

不明，不安装procmail无法发送邮件

> 配置文件

文件路径: ~/.mailrc

```
set sendmail="/usr/bin/msmtp --debug"
```

### crontab

根据个人需求设置cron的执行频率

```
*/5 * * * * /usr/bin/getmail > /dev/null 2>&1
```
