---
title:  "[git] Git 원격 저장소 변경하기"
date: 2022-01-04 10:32:47 +0900
category:
 - git
tag: 
 - git
---
## 소스 관리
개발을 하다보면 소스 관리에 대한 문제가 생긴다. 여러사람들과 함께 개발을 할 경우엔 말할 것도 없고, 단독으로 개발을 하는 경우에도 실험적인 코드는 어떻게 관리할지, 릴리즈되는 소스들은 어떻게 관리할지에 대한 고민이 있을수 밖에 없다. 이때 우리는 보통 소스관리시스템을 이용하기 마련이다.<br>
그 옛날 unix계열의 시스템에는 sccs가 있었고, 그리고 [cvs](https://git-scm.com/), [subversion](https://subversion.apache.org)가 있었다. 그리고 윈도에는 [ms visual sourcesafe](https://ko.wikipedia.org/wiki/%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%86%8C%ED%94%84%ED%8A%B8_%EB%B9%84%EC%A3%BC%EC%96%BC_%EC%86%8C%EC%8A%A4%EC%84%B8%EC%9D%B4%ED%94%84)가 있었고, 현재는 [azure devops server](https://azure.microsoft.com/ko-kr/services/devops/server/)로 통합된 [team foundation server](https://ko.wikipedia.org/wiki/%ED%8C%80_%ED%8C%8C%EC%9A%B4%EB%8D%B0%EC%9D%B4%EC%85%98_%EC%84%9C%EB%B2%84)이 있었다.<br>
그리고 지금 현재는 unix계열이나 윈도 등 너나할것 없이 git으로 대동단결하고 있다.(azure devops server도 git을 지원함)<br><br>

## 원격 저장소
본론으로 들어가서 git을 다양한 방법으로 사용할 수 있다. 단순히 내부에 서버에 git을 설치하여 저장소를 구축할 수 있고, [gitlab](https://gitlab.com)이나 [github](https://github.com), azure devops와 같이 외부저장소를 이용할 수도 있다.<br>
이때 각 저장소 서비스에서 제공하는 기능의 차이 등 다양한 이유로 인해 저장소를 옮겨야 할 경우가 발생하기도 하는데... 원격저장소간 이동은 단순히 import/export 기능을 이용하여 이전을 하면 되고, 로컬PC에 받아둔 저장소에 대해서는 다음 명령을 통해 간단히 원격저장소URL을 변경할 수 있다.

### 현재 설정된 원격 저장소 확인
```
$ git remote -v
origin  https://github.com/kyiimn/myproject.git (fetch)
origin  https://github.com/kyiimn/myproject.git (push)
```

### 원격 저장소 URL 변경
```
$ git remote set-url origin https://gitlab.com/kyiimn/myproject.git

$ git remote -v
origin  https://gitlab.com/kyiimn/myproject.git (fetch)
origin  https://gitlab.com/kyiimn/myproject.git (push)
```

<br>
이렇게 하여 간단히 원격 저장소 URL을 변경할 수 있다.
