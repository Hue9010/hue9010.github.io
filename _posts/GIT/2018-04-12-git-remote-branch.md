---
layout: post
title:  "원격 저장소의 브랜치 사용하기"
tags: [GIT]
categories: GIT
---

-t 옵션 사용하기  
==============  

```bash
git checkout -t 브랜치
git checkout -t origin/develop
```

`-t` 옵션은 원격 저장소의 branch를 로컬에 동일한 이름의 branch를 생성하면서 해당 branch로 checkout을 합니다.  

위에서는 `origin`으로 예시를 보여줬지만 추가로 등록한 `upstream`이나 다른 원격 저장소의 브랜치 또한 같은 방법으로 사용이 가능합니다.  

혹시, 자신이 `clone`한 저장소의 어떤 브랜치들이 있어 모르시면  

```bash
git branch -a
```

위의 명령어로 모든 브랜치를 보실 수 있습니다.  
