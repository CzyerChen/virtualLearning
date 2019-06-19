### docker容器如何修改hosts

#### 方法一：直接修改/etc/hosts
- 想像linux一样操作，可是里面没有vi这种命令哦，echo "ip hostname" >> /etc/hosts 只能这样子写，需要root用户权限
- 如果需要添加多个，可能就是类似于集群的配置的情况，cat hosts.txt >> /etc/hosts，也是需要root用户权限的

#### 方法二：docker run的时候指定hosts
- docker run --add-host=mysql:10.180.8.1 --name test mysql:5.7
- 那就会使用host：mysql  ip:10.180.8.1

#### 方法三：docker-compose.yml文件中，通过配置参数extra_hosts来实现
```text
extra_hosts:
 - "host1:162.242.195.82"
 - "host2:50.31.209.229"
```
- 你再进入容器，cat /etc/hosts的时候，就能够看到两个hostname了

                       