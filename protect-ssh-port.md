## Centos7 修改SSH 端口

1.修改/etc/ssh/sshd_config 

```
nano /etc/ssh/sshd_config
```

修改内容如下：

```
Port 20000
```

2.修改firewall配置

```
firewall-cmd --zone=public --add-port=20000/tcp --permanent 
firewall-cmd --reload
```

permanent是保存配置，不然下次重启以后这次修改无效。

确认是否添加成功，执行下面命令返回yes就是成功。
```
firewall-cmd --zone=public --query-port=20000/tcp
```

如果是阿里云、aws这些云服务器，需要增加20000端口到安全组白名单里面。


3.修改SELinux

使用以下命令查看当前SElinux 允许的ssh端口：

```
semanage port -l | grep ssh
```


如果提示 semanage command not found ：

```
yum provides /usr/sbin/semanage 或者 yum whatprovides /usr/sbin/semanage
```

然后
```
yum -y install policycoreutils-python
```

如果SELinux未开启则先开启

查看SELinux状态
```
/usr/sbin/sestatus -v      ##如果SELinux status参数为enabled即为开启状态
```

开启SELinux

修改/etc/selinux/config 文件

将SELINUX=enforcing改为SELINUX=disabled

重启机器即可

添加20000端口到 SELinux
```
semanage port -a -t ssh_port_t -p tcp 20000
```

确认是否成功，如果成功会输出:ssh_port_t                    tcp    20000, 22
```
semanage port -l | grep ssh
```

4.重试ssh
```
systemctl restart sshd.service
```