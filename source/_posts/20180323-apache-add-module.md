---
title: 아파치 모듈추가
date: 2018-03-23 20:39:06
categories:
- Etc
tags:
- 아파치 모듈추가
---
### 아파치 모듈 추가
/home/sangheon/apps/apache 이라는 경로에 아파치가 설치되어있다고 가정.
1. `./apxs -n modulename -g` 로 모듈추가. 그럼 현재 위치(bin)에 modulename디렉토리가 생성된다.
2. /home/sangheon/apps/apache/conf 경로로 이동하여 httpd.conf를 열자
3. 약 150라인 정도 다음을 추가하자.
`LoadModule modulename_module modules/mod_modulename.so
<Location /modulename>
  SetHandler modulename
</Location>`
위의 내용은 mod_modulename.c 파일을 참고하여 작성가능
4. /home/sangheon/apps/apache/bin 디렉토리에 이동하여 `./apxs -c -i /home/sangheon/apps/apache/bin/modulename/mod_modulename.c` 를 입력하자. 여기서 특정 라이브러리를 이용할 경우 뒤에 -l라이브러리 이름을 입력하자. curl을 이용할 경우 -lcurl을 입력
5. bin 디렉토리에서`./apachectl restart` 입력

만약 c 파일을 수정했을 경우엔 4번 5번을 다시 실행하면 된다.
---

권한문제(sudo permission)로 서버를 실행시킬 수 없을 때 다음과 같이 입력하자.
`sudo /bin/chown root.sangheon /home/sangheon/apps/apache/bin/httpd
sudo /bin/chmod 4755 /home/sangheon/apps/apache/bin/httpd`
