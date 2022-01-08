---
title:  "[linux] 의존성을 잃은 패키지 삭제하기"
date: 2022-01-08 08:41:30 +0900
category:
 - linux
tag: 
 - archlinux
 - ubuntu
 - autoremove
 - pacman
 - apt
---
리눅스에서 뭔가를 패키지를 이용하여 설치하거나 빌드하게 되면 그로 인한 의존성에 의해 의도치 않았던 패키지가 함께 설치되는 것을 볼 수 있다.<br>
이렇게 설치된 패키지를 삭제하고자 할때, [우분투리눅스](https://ubuntu.com "Enterprise Open Source and Linux | Ubuntu")의 경우 다음과 같은 명령을 제공한다.
```
$ sudo apt autoremove
```

그리고 [archlinux](https://archlinux.org)에서는 다음과 같이 조회 명령을 제공하고,
```
$ pacman -Qdtq
```
이것을 응용하여 다음과 같은 명령으로 삭제할 수 있다.
```
$ sudo pacman -Qdtq | pacman -Rs -
```
