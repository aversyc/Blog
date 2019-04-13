##### Python -- 收发邮件

1. **stmp**

   ```python
   # 发送带链接&文件的邮件
   from smtplib import SMTP
   from email.mime.text import MIMEText
   from email.mime.multipart import MIMEMultipart, MIMEBase
   from email.header import Header
   from email import encoders
   
   from_addr = 'zhehongcai@outlook.com'
   from_pwd = 'czh941128'
   to_addr = '919193833@qq.com'
   smtp_server = 'smtp.office365.com'
   smtp_port = 587
   
   # Create the body of the message (a plain-text and an HTML version).
   text = "Hi!\nHow are you?\nHere is the link you wanted:\nhttps://www.python.org"
   html = """\
   <html>
     <head></head>
     <body>
       <p>Hi!<br>
          How are you?<br>
          Here is the <a href="https://www.python.org">link</a> you wanted.
       </p>
     </body>
   </html>
   """
   
   msg = MIMEMultipart('alternative')
   msg['From'] = from_addr
   msg['To'] = to_addr
   msg['Subject'] = Header('Python SMTP 邮件测试', 'utf-8')
   
   msg.attach(MIMEText(text, 'plain', 'utf-8'))
   msg.attach(MIMEText(html, 'html', 'utf-8'))
   
   # 添加附件就是加上一个MIMEBase，从本地读取一个图片:
   with open('/home/caicai/Documents/简历.pdf', 'rb') as f:
       # 设置附件的MIME和文件名，这里是png类型:
       mime = MIMEBase('multipart', 'pdf', filename='简历.pdf')
       # 加上必要的头信息:
       mime.add_header('Content-Disposition', 'attachment', filename='简历.pdf')
       # 把附件的内容读进来:
       mime.set_payload(f.read())
       # 用Base64编码:
       encoders.encode_base64(mime)
       # 添加到MIMEMultipart:
       msg.attach(mime)
   
   server = SMTP(smtp_server, smtp_port)
   server.starttls()       # 调用starttls()，创建安全连接
   server.set_debuglevel(1)
   server.login(from_addr, from_pwd)
   server.sendmail(from_addr, [to_addr], msg.as_string())
   server.quit()
   ```

2. 
