---
layout: post
title:  "fork한 저장소를 원본 저장소와 sync 맞추기"
tags: [GIT]
categories: GIT
---

오픈소스를 발견하고 fork를 한 다음 묵혀두고 있다가 차후 `commit`할 꺼리를 발견해서 작업을 하려고 하는 경우가 있습니다.  

그런데 문제는 3개월 전에 fork한 내 저장소와 현재 해당 오픈소스 저장소와 sync가 맞지 않는 상황일 확률이 높습니다.  

그렇다면 `commit`을 할 사항을 작업 하기 전에 오픈소스의 원본 저장소와 같은 상태로 만들어 놓고 작업을 해야 문제가 발생 하지 않습니다.  

위의 상황은 제가 이 포스팅을 정리하게 된 이유를 예로 들었지만, 꼭 오픈소스에 대한 상황이 아니라도 같은 경우가 발생 할 수 있습니다.  

그럼 이런 경우 해결 하는 방법에 대해 알아 보도록 하겠습니다.  

---

일단 remote 저장소 목록을 확인 해 봅니다.  

> git remote -v  

![git_fork_sync1](/images/git/git_fork_sync1.png)  

일반적이라면 위의 사진처럼 fork해서 생성된 자신의 저장소만 origin으로 등록이 되어 있을겁니다.  

> git remote add upstream 원본 저장소  
> git remote add upstream https://github.com/scouter-project/scouter

![git_fork_sync1](/images/git/git_fork_sync2.png)  

> git fetch upstream  

원본 저장소 `master branch`에 있는 추가된 내용들이 현재 내 로컬의 `upstream/master`로 복사가 됩니다.  

> git checkout master  

내 `master baranch`로 체크아웃  

> git merge upstream/master  

위의 `fetch`를 통해 가져온 원본 저장소의 `master branch`의 내용을 내 `master`로 머지합니다.  

이 과정까지 거치고 나면 로컬에 한해서는 원본 저장소의 `master`와 로컬의 `master`와의 동기화는 성공적으로 끝마치게 됩니다.    

이후 자신의 원격 레파지토리도 갱신을 하기 원하면 기존 작업하던 방식 그대로  

> git push origin  

push를 해주면 됩니다.  

간단히 위의 과정들을 통해 로컬 및 자신의 원격 저장소를 원본 저장소에 맞추어 동기화를 마무리 할 수 있습니다.
