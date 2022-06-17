### Jenkins 部署

该部分小于有非常详细的文档，此处简单介绍jenkins部署流程，以及使用时我遇到的问题

1.拉镜像

```
sudo docker pull jenkinsci/blueocean
```

2.运行容器

```
sudo docker run --name jenkins --restart always -d -p 10880:8080 -p 15000:50000 -v /home/jenkins:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
```

### 遇到的问题

##### jenkins执行docker命令无权限解决方案

1.尝试运行如下命令

```
chown jenkins:docker /var/run/docker.sock
```

2.停掉原来的，重启一个jenkins容器

##### 使用Jenkins api创建任务报错

进入jenkins管理界面，在jenkins控制台输入：

```
hudson.security.csrf.GlobalCrumbIssuerConfiguration.DISABLE_CSRF_PROTECTION = true
```

##### 无法运行python脚本

由于docker的隔离性，为了运行python脚本，需要在jenkins容器中安装python环境

```
docker exec -it {jenkins-container-id} /bin/bash
```

blueocean 的jenkins下使用apk命令安装python ，使用apk add命令