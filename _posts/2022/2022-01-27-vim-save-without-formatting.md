---
title:  "[tip/vim] 현재 파일 저장시 coc prettier 예외 처리하기"
date: 2022-01-27 15:54:32 +0900
category:
 - tip
tag: 
 - vim
 - formatting
---
vim에 coc prettier 플러그인을 이용하여 저장시 자동 포멧팅을 설정했을때, 현재 파일만 제외하고 싶을때 다음과 같이 저장한다.
```
:noa w
```
