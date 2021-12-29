---
title:  "[Linux] Archlinux 설치 순서"
date: 2021-12-26 16:27:57 +0900
category:
 - linux
tag: 
 - linux
 - archlinux
---
![Archlinux 로고](/blog/assets/images/posts/2021/install-archlinux/archlogo.8a05bc7f6cd1.png)


## ISO 파일 다운로드
[Archlinux](https://archlinux.org "Arch Linux") 홈페이지에서 다음과 같은 방법으로 설치 ISO 다운로드를 제공한다.
  - BitTorrent
  - HTTP 다운로드

그리고 다음과 같은 형태의 이미지파일도 제공한다.
  - NetBoot용 iPXE 파일 다운로드 (BIOS, UEFI 모두 지원)
  - Vagrant 이미지
  - Docker 이미지
  - VM용 이미지 (기본 qcow2파일, libvirt용, virtualbox용)

여기서는 설치 ISO를 다운로드 한다.<br><br>

## 부팅
다운로드 받은 ISO파일을 이용하여 USB나 CD/DVD 등 설치 미디어를 만들어 부팅을 한다. 설치 미디어는 BIOS방식과 UEFI방식 모두 지원하고 있다. 다만 사용자는 자신의 PC가 어떤 방식으로 부팅이 되는지 알고 있어야 한다.<br>
윈도11 사용자라면 UEFI방식의 부팅 방식을 사용하고 있을텐데, 이때 반드시 보안부트 기능은 꺼두어야 한다.<br>
그리고 기존에 다른 배포판의 리눅스 사용자라면 다음과 같은 명령을 통해 UEFI 부트 방식인지 확인할 수 있다.
```
# ls /sys/firmware/efi
```
이제 만들어진 설치 미디어를 통해 부팅을 한다.
3가지 부팅 모드를 지원하는데, 첫번째 것을 선택하면 무난하다.<br><br>

## 네트워크 활성화
부팅 후 다음 명령을 통해 지금 네트워크가 활성화 되었는지 확인해본다.
```
# ping -c 3 google.com
```
만약 타임아웃이 발생한다면 네트워크를 수동으로 활성화해야 한다.

### 무선네트워크
**iwctl** 명령을 이용한 무선네트워크 접속방법을 소개한다. **iwctl** 명령은 설치후에도 [iwd](https://archlinux.org/packages/community/x86_64/iwd/) 패키지 설치를 통해 사용할 수 있다.<br>
#### iwctl 실행
```
# iwctl
```
#### 인터페이스 검색
```
[iwd]# device list
[iwd]# station 장치명 scan 
```
#### 네트워크 검색
```
[iwd]# station 장치명 get-networks
```
#### 네트워크 접속
```
[iwd]# station 장치명 connect SSID
```
이때 네트워크 접속 비밀번호가 필요하다면 비밀번호를 묻는 프롬프트가 표시된다.<br><br>

### 유선 네트워크
유선 네트워크의 경우 **ip** 명령을 통해 어렵지 않게 설정할 수 있다.

#### 인터페이스 확인
```
# ip link
```
#### IP 설정
```
# ip addr add 192.168.50.5 dev eth0
```
#### 게이트웨이 설정
```
# ip route add 192.168.50.0/24 via 192.168.50.1 dev eth0
```
#### 인터페이스 활성화
```
# ip link set eth0 up
```
<br><br>

## 파티션 설정
### 파티션 구성
UEFI 부트 방식이라면 ESP(EFI System Partition)이 별도로 필요하고 BIOS 방식이라면 그냥 필요한 파티션만 구성하면 된다.<br>
설치미디어에서는 **fdisk**, **cfdisk**, **gdisk**를 사용할 수 있다. 자신에게 편한 파티션 편집툴을 선택하여 파티션을 구성한다.<br>
*참고로, ESP 파티션은 512MB 정도만 할당하여도 충분하다.*<br><br>
여기서는 다음과 같이 파티션을 구성하도록 하겠다.

|파티션|파일시스템|용량|
|-|-|-|
|/dev/sda1|EFI System Partition|512MB|
|/dev/sda2|Linux Swap|4GB|
|/dev/sda3|Linux File System|256GB|

### 포멧
**(UEFI 부트)** ESP는 반드시 FAT32로 포멧을 해야 한다.
```
# mkfs.vfat -F32 /dev/sda1
```
root 파티션을 ext4 파일시스템으로 포멧한다.
```
# mkfs.ext4 -j /dev/sda3
```
스왑파티션은 다음과 같이 포멧한다.
```
# mkswap /dev/sda2
```
### 마운트
먼저 스왑파티션을 활성화 한다.
```
# swapon /dev/sda2
```
root 파티션을 마운트한다.
```
# mount /dev/sda3 /mnt
```
**(UEFI 부트)** ESP는 root 파티션의 /boot 디렉터리에 마운트 한다.
```
# mkdir /mnt/boot
# mount /dev/sda1 /mnt/boot
```
<br><br>

## 기본시스템 설치
### 시간설정
ntp서버를 이용하여 OS와 시스템 시간을 동기화한다.
```
# timedatectl set-ntp true

# hwclock --systohc --utc
```
### mirrorlist
기본적으로 현재위치에서 가장 빠른 미러사이트를 추천해주기는 하지만, 그래도 대한민국의 미러사이트로 설정을 덮어준다. (그게 더 빠르다)

```
# vim /etc/pacman.d/mirrorlist
```
```
Server = http://mirror.premi.st/archlinux/$repo/os/$arch
Server = http://mirror.anigil.com/archlinux/$repo/os/$arch
Server = http://ftp.harukasan.org/archlinux/$repo/os/$arch
Server = http://ftp.lanet.kr/pub/archlinux/$repo/os/$arch
```
### pacstrap
위의 과정을 모두 마쳤다면 이제 실제 기본 시스템을 설치할 차례다. **pacstrap** 명령을 이용하여 기본 시스템을 설치한다.
```
# pacstrap /mnt base linux linux-firmware
```
*/mnt*뒤에 나열해 준것은 실제 패키지나 패키지그룹을 의미한다. 필요에 따라 base-devel이나 vim과 같이 추가적으로 필요한 것이 있다면 이때 같이 설치해주는 것이 좋다.<br>
그리고 네트워크 연결 관리를 위해 [NetworkManager](https://archlinux.org/packages/extra/x86_64/networkmanager/)를 사용하고자 한다면 이때 같이 설치해주도록 한다.
### genfstab
기본 시스템 설치가 완료되었다면 이제 현재 마운트한 파티션구조를 바탕으로 fstab 파일을 생성한다.
```
# genfstab -U /mnt >> /mnt/etc/fstab
```
<br><br>

## 기본시스템으로 진입
다음 명령을 통해 설치된 시스템으로 진입한다.
```
# arch-chroot /mnt
```
### 비밀번호 설정
먼저 root 계정의 비밀번호를 설정해준다.
```
# passwd
```
### 로케일 정보 생성
시스템에서 사용할 로케일 정보를 생성하기 위한 파일을 설정한다.
```
# vim /etc/locale.gen
```
```
en_US ISO-8859-1
ko_KR.UTF-8 UTF-8
```
이제 로케일 정보를 생성한다.
```
# locale-gen
```
### 로케일 설정
기본으로 사용할 로케일을 설정한다.
```
# echo LANG=en_US > /etc/locale.conf
```
### 호스트명 설정
사용할 호스트명을 설정한다.
```
# echo archlinux > /etc/hostname
```
### 시간대 설정
```
# ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```
### 관리자 계정 추가
관리자 계정으로 사용할 계정을 추가한다.
```
# useradd -m -g users -G wheel -s /bin/bash 사용자명
```
비밀번호를 설정한다.
```
passwd 사용자명
```
### sudo 설정
관리자 그룹(wheel)에 대한 sudo 시용 설정을 한다.
```
# EDITOR=vim visudo
```
```
%wheel ALL=(ALL) ALL            # 이 부분을 찾아 코멘트 제거
```
### 부트매니저 설치
기본적으로 [grub](https://archlinux.org/packages/core/x86_64/grub/)와 systemd-boot를 사용할 수 있는데, 여기서는 grub를 설치하는 것으로 하겠다.<br>
먼저 **grub** 패키지를 설치한다.
```
# pacman -S grub
```
이때, UEFI 방식이라면 [efibootmgr](https://archlinux.org/packages/core/x86_64/efibootmgr/) 패키지를 같이 설치해준다.
```
# pacman -S grub efibootmgr
```
실제 **grub** 부트로더를 시스템에 설치한다.
 - BIOS 부트 방식
```
# grub-install --target=i386-pc /dev/sda
```
 - UEFI 부트 방식
```
# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader=Archlinux --recheck
```

그리고 **grub** 설정파일을 생성해 준다.
```
# grub-mkconfig -o /boot/grub/grub.cfg
```
### 새 시스템으로 재부팅
이제 모든 설치가 완료되었다.<br>
만약 **NetworkManager**와 같이 부트타임에 시작해야 할 데몬이 있다면 지금 설정을 해주도록 한다.
```
# systemctl enable NetworkManager.service
```
기본시스템에서 빠져나와 설치작업을 하며 마운트 했던 모든 파티션을 언마운트하고 재부팅 명령을 내린다.
```
# exit

# umount -lR /mnt

# reboot
```
<br><br>

## Archlinux 시작
이제 설치가 모두 완료되고 새로운 시스템으로 부팅까지 마쳤다.<br>
지금은 OS만 설치된 상황이고 입맛에 맞게끔 GUI를 설치하던지 CLI모드를 구성하면 된다.<br>
여기서는 GNOME 환경을 설치하는 것으로 마치겠다.
```
$ sudo pacman -S xorg-xwayland gnome gnome-extra

 ...

$ sudo systemctl enable --now gdm
```