---
title: git hard reset
date: 2021-02-21 21:48:33
categories:
- OS
- Linux
thumbnail: /images/centoslogo.png
---
가끔 돌아올수없는 길을 건넜을 때 그냥 돌려버리고 싶을 때....만 활용하자


```
$ git log

돌아가기 원하는 시점의 commit 복사

$ git reset --hard  {commit ID}

$ git push -f origin master
```


#### ! [remote rejected] master -> master (pre-receive hook declined) 발생시
gitlab에서 소스 프로젝트->세팅->protected branches에 developer can push라는 체크박스가 있는데 체크 후 unprotect 버튼 클릭 후 재시도
