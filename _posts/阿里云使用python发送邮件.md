> 阿里云将25端口封掉了
python 版本2.6.6 
解决办法使用smtplib.SMTP_SSL方法，端口改为465，这个需要查看邮箱支持
测试无乱码，发送正常
```
# -*- coding: UTF-8 -*-
import smtplib
import traceback
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart


def sendmail(subject, msg, toaddrs, fromaddr, smtpaddr, password):
    '''
    @subject:邮件主题
    @msg:邮件内容
    @toaddrs:收信人的邮箱地址
    @fromaddr:发信人的邮箱地址
    @smtpaddr:smtp服务地址，可以在邮箱看，比如163邮箱为smtp.163.com
    @password:发信人的邮箱密码
    '''
    mail_msg = MIMEMultipart()
    if not isinstance(subject, unicode):
        subject = unicode(subject, 'utf-8')
    mail_msg['Subject'] = subject
    mail_msg['From'] = fromaddr
    mail_msg['To'] = ','.join(toaddrs)
    mail_msg.attach(MIMEText(msg, 'html', 'utf-8'))
    try:
        s = smtplib.SMTP_SSL()
        s.connect(smtpaddr, 465)  # 连接smtp服务器
        s.login(fromaddr, password)  # 登录邮箱
        s.sendmail(fromaddr, toaddrs, mail_msg.as_string())  # 发送邮件
        s.quit()
    except Exception, e:
        print "Error: unable to send email"
        print traceback.format_exc()


if __name__ == '__main__':
    fromaddr = "xxx@126.com"
    smtpaddr = "smtp.126.com"
    toaddrs = ["xxxx@126.com","xxx@139.com"]
    subject = "最新消息"
    password = "xxxx"
    msg = "测试"
    sendmail(subject, msg, toaddrs, fromaddr, smtpaddr, password)

```
