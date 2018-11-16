# SSH免密登陆


将本地公钥copy到远程服务器上（如果没有公钥先生成一个即可.ssh目录下允许`ssh-keygen -t rsa`）
```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.100
```

如果远程服务器有变动，提示如下错误
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that the RSA host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
51:e1:cc:58:ec:b9:a3:f1:e8:8f:16:a7:c1:5f:c3:dd.
Please contact your system administrator.
Add correct host key in /user/.ssh/known_hosts to get rid of this message.
Offending key in /user/.ssh/known_hosts:10
RSA host key for 192.168.1.100 has changed and you have requested strict checking.
Host key verification failed.
```
则需要在`.ssh/know-hosts`下删除第十行即可.
------ 

也可直接输入用户名登陆，然后根据提示输入密码即可
```
ssh root@192.168.1.100
```
