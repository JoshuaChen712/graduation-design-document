Gitlab部署

1.拉镜像

```
sudo docker pull gitlab/gitlab-ce:14.8.0-ce.0
```

2.运行

其中几个挂载目录用于持久化数据到本地，方便移植

```
sudo docker run -d  -p 10543:443 -p 10180:80 -p 10222:22 --name gitlab --restart always -v /home/gitlab/config:/etc/gitlab -v /home/gitlab/logs:/var/log/gitlab -v /home/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce:14.8.0-ce.0
```

3.修改gitlab.rb

```ruby
vim /home/gitlab/config/gitlab.rb

external_url 'http://101.34.255.125'

gitlab_rails['gitlab_ssh_host'] = '101.34.255.125'
gitlab_rails['gitlab_shell_ssh_port'] = 10222
```

4.重启gitlab

```ruby
docker restart gitlab
```

5.设置root密码

初始的root账号会出现密码错误的问题，解决方法如下

```
 docker exec -it gitlab /bin/bash
 gitlab-rails console
 
 u=User.where(id:1).first
 u.password=12345678    
 u.password_confirmation=12345678 
 u.save!
 quit
```

