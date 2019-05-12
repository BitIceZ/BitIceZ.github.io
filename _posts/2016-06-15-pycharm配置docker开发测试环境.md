### ubuntu开启docker远程访问

注意：生产环境下请不要使用，本环境只是用于内部网络访问，做开发和测试

#### Docker远程说明

默认情况下，Docker通过守护进程Unix socket(/var/run/docker.sock)来进行本地进程通信，而不会监听任何端口，因此只能在本地使用docker客户端或者使用Docker API来进行操作。
    
若想在其他主机操作Docker主机，就需要让Docker守护进程打开一个HTTP Socket，这样才能实现远程通信。


#### 具体操作

1. 安装docker，官网有说明，然后pull需要用到的镜像

2. VMware中的宿主机ubuntu，

  **直接通过添加源来安装docker **,在`/lib/systemd/system/docker.service`
  中添加`ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375`

  **通过snap安装docker**，在`/etc/systemd/system/snap.docker.dockerd.service`添加`ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375`

3. 重启Docker服务
```
systemctl daemon-reload
systemctl restart docker.service|systemctl restart snap.docker.dockerd.service
```
4. 查看本地端口2375是否开启
```
netstat -plnt|grep 2375
tcp6       0      0 :::2375                 :::*                    LISTEN      20421/dockerd
```

#### 测试

1. 打开pycharm的settings->project->Project Interpreter->add->docker->new,在Engine API URL填入宿主机的ip:2375
2. 选择Image name和Python interpreter path(这个需要在你docker容器中安装的python环境，然后可以直接在终端启动进入python)
3. 提示连接成功，去宿主机用`docker ps -a`可以看到会有一个新的docker容器

