---
layout: post
title:  "원격 저장소에 push한 내용을 되돌리기"
tags: [GIT]
categories: GIT
---

원격의 push 내용 되돌리기  
======================  

로컬에서 작업을 끝내고 원격에다가 push까지 하고 난 다음 로컬의 커밋을 다시 이전으로 되돌려야 되는 상황이 발생 하기도 합니다. 로컬이야 간단히 돌릴 수 있지만, 원격에 경우 어떻게 돌려야 할지 난감한 경우가 있습니다. 그런 경우 아래의 명령어를 이용하면 간단히 해결 가능 합니다.  

```bash
git push origin +"branch"
git push origin +master
```
