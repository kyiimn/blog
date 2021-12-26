---
layout: single
title:  "[Linux] btrfs Subvolume 사용하기"
date: 2021-12-26 14:11:59 +0900
categories:
 - linux
tag: 
 - linux
 - btrfs
---
# btrfs는?
[btrfs](https://btrfs.wiki.kernel.org/)는 오라클에서 개발한 리눅스 파일시스템의 하나로 이름답게(btrfs라는 이름은 **B**-**T**ree **F**ile**S**ystem의 약자, 혹자들은 **B**u**t**ter **F**ile **S**ystem이라고도 함) B-tree를 기반으로 하고 있다.<br><br>
특징으로는 파일시스템 자체가 매우 가볍고, CoW(Copy-on-Write)방식을 사용한다. 그리고 이 글에서 소개하고자 하는 서브볼륨 기능을 제공하고, 스냡샷, 파일시스템 압축 등을 지원한다. 또한 SSD에 최적화된 모드도 제공한다.<br><br>
2007년부터 개발되기 시작하여 리눅스 커널 3.10부터 기본트리에 통합되고, 현재는 매우 안정된 상태이다.<br><br>

# Subvolume 사용하기
btrfs의 subvolume은 기본적으로 Posix 파일트리(디렉터리)이지만 독립적으로 마운트가 가능하다. 상위 볼륨과 디스크 공간은 공유하고 있지만, 독립된 볼륨으로 사용이 가능하다는 얘기다.<br><br>

## Subvolume 생성하기
1. 파일시스템이 btrfs인 파티션을 마운트
{% highlight bash %}
# mount -t btrfs /dev/sdb1 /data
{% endhighlight %}

2. Subvolume 생성
{% highlight bash %}
# btrfs subvolume create /data/download-by-scott
# btrfs subvolume create /data/download-by-tiger
# btrfs subvolume create /data/download-by-lion
# btrfs subvolume create /data/download-by-orion
{% endhighlight %}

3. 생성된 Subvolume 확인
{% highlight bash %}
# btrfs subvolume list /data
ID 258 gen 12 top level 5 path download-by-scott
ID 259 gen 12 top level 5 path download-by-tiger
ID 260 gen 12 top level 5 path download-by-lion
ID 261 gen 12 top level 5 path download-by-orion
{% endhighlight %}

## Subvolume을 특정사용자에게 할당하기
{% highlight bash %}
# chown scott /data/download-by-scott
# chown tiger /data/download-by-tiger
# chown lion /data/download-by-lion
# chown orion /data/download-by-orion
{% endhighlight %}

## 마운트하기
{% highlight bash %}
# mount -o subvolid=258 /dev/sdb1 /home/scott/Downloads
# mount -o subvolid=259 /dev/sdb1 /home/tiger/Downloads
# mount -o subvol=download-by-lion /dev/sdb1 /home/lion/Downloads
# mount -o subvol=download-by-orion /dev/sdb1 /home/orion/Downloads
{% endhighlight %}

## fstab에 등록하기
{% highlight bash %}
/dev/sdb1   /home/scott/Downloads   btrfs   subvol=download-by-scott    0 0
/dev/sdb1   /home/tiger/Downloads   btrfs   subvol=download-by-tiger    0 0
/dev/sdb1   /home/lion/Downloads   btrfs   subvolid=260    0 0
/dev/sdb1   /home/orion/Downloads   btrfs   subvolid=261    0 0
{% endhighlight %}
