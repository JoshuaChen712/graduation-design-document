运行

```
docker run --name mysql -p 13306:3306 -e MYSQL_ROOT_PASSWORD=123456  -v /home/docker-mysql/initdb/:/docker-entrypoint-initdb.d/ -v /home/docker-mysql/data:/var/lib/mysql --restart=on-failure:3 -d mysql:8.0.17
```

