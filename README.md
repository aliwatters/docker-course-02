# Docker Course 02 - from the official docker youtube channel.

https://www.youtube.com/playlist?list=PLkA60AVN3hh_6cAz8TUGtkYbJSL2bdZ4h

Cons: main one 360p - max resolution - needs 720p.

## Tutorial 1

Hello World - docker in vagrant, installed in ubuntu and centos. (apt-get and yum) - then manual install of binary due to old versions in the package manager.

## Tutorial 2

Busybox based examples - `docker run -it busybox` - etc eg `-itd`

`docker attach <containerID>`

One useful option `docker ps --no-trunc=true` - full container IDs

Touches on docker stop/kill, --name

And "docker killall" == `docker kill $(docker ps -q)` and `docker rm $(docker ps -aq)`

## Tutorial 3 - volumes

Basic command is:

`docker run -it -v /host/directory/name:/container/directory imagename`

In practice:

```
ali@pluto:~/git/docker-course-02$ cd busybox-01/
ali@pluto:~/git/docker-course-02/busybox-01$ mkdir shared
ali@pluto:~/git/docker-course-02/busybox-01$ docker run -it -v /home/ali/git/docker-course-02/busybox-01:/shared busybox
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
c00ef186408b: Pull complete 
ac6a7980c6c2: Pull complete 
Digest: sha256:e4f93f6ed15a0cdd342f5aae387886fba0ab98af0a102da6276eaf24d6e6ade0
Status: Downloaded newer image for busybox:latest
/ # cd shared
/shared # touch testfile.txt
/shared # ls
shared        testfile.txt
/shared # mv testfile.txt shared/.
/shared # ls
shared
/shared # exit
ali@pluto:~/git/docker-course-02/busybox-01$ ls shared/
testfile.txt
```

### Volumes from! - is neat.

Dockerfile
```
FROM ubuntu:latest
VOLUME ["/src", "/config"]
CMD ["/bin/bash"]
```

Then build an image - `docker build -t unbuntu-vols-01 .`

```
ali@pluto:~/git/docker-course-02/busybox-01$ docker run -it -v /test unbuntu-vols-01
root@961e791ec381:/# ls
bin  boot  config  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  src  srv  sys  test  tmp  usr  var
root@961e791ec381:/# ls config/
root@961e791ec381:/# cd config/
root@961e791ec381:/config# touch example.cfg
root@961e791ec381:/config# cd ../test
root@961e791ec381:/test# touch test.txt

// new terminal

ali@pluto:~/git/docker-course-02/busybox-01$ docker run -it --volumes-from 961e791ec381 unbuntu-vols-01 
root@79fec08d9462:/# ls /config
root@79fec08d9462:/# ls /config/
example.cfg
root@79fec08d9462:/# ls /test/
test.txt
```

So both share the /test /config /src - volumes - changes on one happen on the other.


### Backups of a containers internal files

`docker run --rm --volumes-from <source-container-name> -v $(pwd):/backup busybox tar cvf /backup/<source-container-name>.tar /<directory>`

```
ali@pluto:~/git/docker-course-02/busybox-01$ docker run --rm --volumes-from 79fec08d9462 -v $(pwd):/backup busybox tar cvf /backup/79fec08d9462.tar /test
tar: removing leading '/' from member names
test/
test/test.txt

ali@pluto:~/git/docker-course-02/busybox-01$ ls
79fec08d9462.tar  Dockerfile  shared
```







