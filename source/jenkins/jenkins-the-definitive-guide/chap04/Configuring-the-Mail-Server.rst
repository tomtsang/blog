================================
Configuring-the-Mail-Server
================================

配置 邮件服务
================================

http://120.25.204.216:8080/

系统管理/系统配置

http://120.25.204.216:8080/configure

Jenkins Location
    系统管理员邮件地址 写入: zengyl@jiaolongchuhai.com

邮件通知
 	SMTP服务器	写入:smtp.exmail.qq.com
 	用户默认邮件后缀 写入: @jiaolongchuhai.com
    点击高级

    使用SMTP认证 打勾
 	用户名	写入: zengyl@jiaolongchuhai.com  注意:这里的写入的邮箱必须与 刚刚 系统管理员邮件地址 保持一致.
 	密码	写入: 对应密码••••••••••••••••••••••••••••••••••••••••••••••••••••••••••
 	使用SSL协议	不要打勾	这里为什么不打勾,我也不清楚是为什么?谁知道的?
 	SMTP端口	写入: 587
 	字符集	写入: UTF-8

测试邮件股务:

    通过发送测试邮件测试配置 打勾
    Test e-mail recipient 写入: 1336463719@qq.com , 点击 Test configuration, 如果出现了 Email was successfully sent, 并收到邮件,说明成功了.

