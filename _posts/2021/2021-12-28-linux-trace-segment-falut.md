---
layout: single
title:  "[Linux] 디버그 없이 세그먼트폴트 발생시 코드 추적"
date: 2021-12-28 14:52:13 +0900
categories:
 - linux
tag: 
 - linux
 - segfalut
 - segment
---
## 다음과 같이 오류발생!
~~~
prog_sendftp[31932]: segfault at 00000000ffc32000 rip 0000000008061d70 rsp 00000000ffc2fe00 error 6
~~~

## 세그먼트폴트 발생 위치 추적
해당 프로세스를 실행시켜 (혹은 실행중인 프로세스에서) /proc/[PID]/maps 에서 세그먼트폴트가 발생한 주소값(위 메시지의 rip부분)이 어느 모듈에 위치하는지 검색
~~~
# ps -ef | grep prog_sendftp
root     15647 12647  0 09:27 pts/0    00:00:00 grep prog_sendftp
akn      24079     1  0 Feb01 ?        00:01:59 ./prog_sendftp empas/empas.cfg
~~~
~~~
# cat /proc/24079/maps
00142000-00157000 r-xp 00000000 68:02 5767845                            /lib/libnsl-2.5.so
00159000-0015b000 rw-p 00159000 00:00 0 
00384000-00385000 rw-p 00011000 68:02 5767942                            /lib/libz.so.1.2.3
00b37000-00b52000 r-xp 00000000 68:02 5767871                            /lib/ld-2.5.so
00b56000-00cac000 r-xp 00000000 68:02 5767181                            /lib/libc-2.5.so
00caf000-00cb2000 rw-p 00caf000 00:00 0 
08048000-08069000 r-xp 00000000 68:05 3315279                            /app/send/oftp/prog_sendftp
08069000-0806a000 rw-p 00021000 68:05 3315279                            /app/send/oftp/prog_sendftp
^^^^^^^^^^^^^^^^^                                                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^
0806a000-0806d000 rw-p 0806a000 00:00 0 
082e1000-08335000 rw-p 082e1000 00:00 0                                  [heap]
f7ea8000-f7eb2000 r-xp 00000000 68:02 5767204                            /lib/libnss_files-2.5.so
~~~

## 해당 모듈의 오브젝트 덤프 얻기
~~~
# cd /app/send/oftp
# objdump -d prog_sendftp > prog_sendftp.dump
# cat prog_sendftp.dump
08061d03 <FTP_CommandReceive>:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
 8061d65:       3c 0a                   cmp    $0xa,%al
 8061d67:       74 47                   je     8061db0 <FTP_CommandReceive+0xad>
 8061d69:       0f b6 45 fb             movzbl 0xfffffffb(%ebp),%eax
 8061d6d:       8b 55 fc                mov    0xfffffffc(%ebp),%edx
 8061d70:       88 02                   mov    %al,(%edx)
 ^^^^^^^
 8061d72:       83 45 fc 01             addl   $0x1,0xfffffffc(%ebp)
 8061d76:       a1 00 97 06 08          mov    0x8069700,%eax
 8061d7b:       83 f8 01                cmp    $0x1,%eax
 8061d7e:       75 ba                   jne    8061d3a <FTP_CommandReceive+0x37>
 8061d80:       8b 45 0c                mov    0xc(%ebp),%eax
 8061d83:       89 44 24 08             mov    %eax,0x8(%esp)
 8061d87:       c7 44 24 04 04 64 06    movl   $0x8066404,0x4(%esp)
~~~

## 위 정보를 가지고 대충 소스에서 때려잡음