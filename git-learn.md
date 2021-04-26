### 添加公钥

+ 在我们从github 上面下载代码的时候，我们常常需要git clone下相关的仓库的地址，但是，我们可能有时候会遇到这种报错信息。

  ```git
  root@iZbp1gw2t7alb7hn2cbbprZ:/demo# git clone git@github.com:Q1mi/bubble.git
  Cloning into 'bubble'...
  Warning: Permanently added the RSA host key for IP address '52.74.223.119' to the list of known hosts.
  git@github.com: Permission denied (publickey).
  fatal: Could not read from remote repository.
  
  Please make sure you have the correct access rights
  and the repository exists.
  ```

  

出现这种情况的原因是我们缺少公钥，我们可以通过

```
$ ssh-keygen -t rsa -C "ncuyanping666@126.com"(引号里面是github的账户名)
```

这样我们就可以将获取公钥，如下：

```
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:SVRmX3iXKDRQcyvjJFJQuYjZsdxr38sE1isqRfWp3sM ncuyanping666@126.com
The key's randomart image is:
+---[RSA 2048]----+
|       .+=O= oo .|
|       o.+ +=oo..|
|      =.*.o++o.. |
|     o *.=+ +o   |
|        S .+..   |
|         +... .  |
|        o o.+o   |
|       .  .o+E   |
|        ..   oo  |
+----[SHA256]-----+
```



获取到公钥之后，我们可以将我们的利用

```
cat /root/.ssh/id_rsa.pub（公钥的存储路径）
```

通过这样的命令，我们获取到公钥，然后加入到github里面的SSH Key 这里就行了