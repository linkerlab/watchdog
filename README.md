# WatchDog

使用方式：

```
docker run -v /run/docker.sock:/run/docker.sock \
  -v /usr/bin/docker:/bin/docker \
  -v /sys/fs/cgroup:/sys/fs/cgroup \
  -d watchdog [your docker commands]
```


原本啟動docker的方式是：

```
docker run -d -p 80:80 nginx
```


如果要透過keeb/docker-watchdog來監控，可以修改為:

```
docker run -v /run/docker.sock:/run/docker.sock \
  -v /usr/bin/docker:/bin/docker \
  -v /sys/fs/cgroup:/sys/fs/cgroup -it watchdog nginx
```

如果需要再簡單直覺一點，可以幫watchdog建立一個alias：

```
alias watchdocker='docker run -v /run/docker.sock:/run/docker.sock \
  -v /usr/bin/docker:/bin/docker \
  -v /sys/fs/cgroup:/sys/fs/cgroup \
  -d watchdog '
```

接下來可以使用下面方式來啟動你的container：

```
watchdocker -p 80:80 nginx
```

測試：

```
root@dca196e1a285:/# ps -ef| grep nginx
root     14570   569  0 14:46 ?        00:00:00 sh start.sh nginx
root     14603   569  0 14:46 ?        00:00:00 nginx: master process nginx -g daemon off;
104      14628 14603  0 14:46 ?        00:00:00 nginx: worker process
root     14676 14652  0 14:47 ?        00:00:00 grep nginx
root@dca196e1a285:/# kill -9 14603
```

程序砍掉之後，watchdog會馬上把程序帶起來，如下(process id已經換成新的)：

```
root@dca196e1a285:/# ps -ef| grep nginx
root     14570   569  0 14:46 ?        00:00:00 sh start.sh nginx
root     14731   569  2 14:56 ?        00:00:00 nginx: master process nginx -g daemon off;
104      14740 14731  0 14:56 ?        00:00:00 nginx: worker process
root     14748 14652  0 14:56 ?        00:00:00 grep nginx
```

附註：該watchdog針對被docker rm -f"砍掉的程序無用，會一併終止watchdog的運作。
