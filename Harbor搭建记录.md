### Harbor搭建记录

建议参考官方文档

#### 一.准备

想要安装Harbor，首先需要安装Docker，Docker-Compose以及Openssl

#### 二.下载安装包

前往[Harbor releases page](https://github.com/goharbor/harbor/releases),选择合视的版本进行下载

此处有在线与离线两种版本, 这里我们选择离线版. 二者区别在于后续是否需要联网拉镜像.

本次搭建选择了2.4.1版本

下载后解压到/opt/harbor

```
bash $ tar xzvf {your-harbor-tar-file} -C /opt/harbor
```

#### 三.配置HTTPS连接

只有配置了HTTPS连接,才能从其他主机访问到Harbor所在主机

生成CA私钥

```
openssl genrsa -out ca.key 4096
```

生成CA证书

注意修改ip，后面均如此

```
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Shanghai/L=Shanghai/O=example/OU=Personal/CN=192.168.1.115" \
 -key ca.key \
 -out ca.crt
```

生成服务器私钥,此处修改服务器的ip地址

```
openssl genrsa -out 192.168.1.115.key 4096
```

生成CSR

```
openssl req -sha512 -new \
    -subj "/C=CN/ST=Shanghai/L=Shanghai/O=example/OU=Personal/CN=192.168.1.115" \
    -key 192.168.1.115.key \
    -out 192.168.1.115.csr
```

新建v3.ext 写入如下内容

```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
IP.1=192.168.1.115
EOF
```

使用v3.ext为Harbor生成证书

```
openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in 192.168.1.115.csr \
    -out 192.168.1.115.crt
```

接下来为Docker和Harbor提供证书

首先将服务器证书与Key copy到Harbor中

```
cp yourdomain.com.crt /data/cert/
cp yourdomain.com.key /data/cert/
```

将crt转化为cert,以备docker使用

```
openssl x509 -inform PEM -in yourdomain.com.crt -out yourdomain.com.cert
```

```
cp yourdomain.com.cert /etc/docker/certs.d/yourdomain.com/
cp yourdomain.com.key /etc/docker/certs.d/yourdomain.com/
cp ca.crt /etc/docker/certs.d/yourdomain.com/
```

重启Docker

```
systemctl restart docker
```

#### 四.配置Harbor YML

在/opt/harbor下有harbor.yml.tmpl文件,将其修改如下

```
hostname: 192.168.1.115
http:
    port: 80
https:
    port: 443
    certificate: /data/cert/192.168.1.115.crt
    private_key: /data/cert/192.168.1.115.key
harbor_admin_password: Harbor12345
data_volume: /data
```

#### 五.安装

在/opt/harbor目录下,运行如下命令

```
./prepare
./install.sh
```

然后通过访问预设端口,就可以打开Harbor UI界面
