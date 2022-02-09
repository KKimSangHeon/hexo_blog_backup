---
title: Vue 네아로(네이버아이디)로 로그인 기능 구현
date: 2021-01-04 10:42:27
categories:
- CS
- 오픈소스,기술
---
vue를 활용한 naver 아이디로 로그인 기능 구현

### 플로우
1. front에서 code를 얻고
2. code를 서버로 던지고
3. 서버에서 code를 활용하여 토큰을 얻고
4. 얻은 토큰을 통해 사용자 정보를 조회함

### 문제사항
위 1번과정 중 네이버 아이디로 로그인 버튼을 클릭시 axios get함수를 활용하여 코드를 얻으려 하니 CORS 에러가 발생
```login#:1 Access to XMLHttpRequest at 'https://nid.naver.com/oauth2.0/authorize?response_type=code&client_id=XXXXXXXXXXXX&redirect_uri=http%3A%2F%2F127.0.0.1%3A8080%2FnaverLogin&state=78577e6c-3f10-4146-910c-3fd4e684dfb5' from origin 'http://127.0.0.1:8080' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.

```

카카오 아이디로 로그인의 경우 해당 에러가 발생하지 않았는데... 네이버 서버에서 헤더에 CORS관련 세팅을 안해줘서 그렇다고 생각한다.

### 해결방법
해당방법이 적합한 방법인지는 확실하지 않으나 여러 사이트들의 네이버아이디로 로그인에 대해 살펴보니 해당방법으로 처리한것을 확인할 수 있었음.

#### 해결방법상세

아래의 주소로 직접 접속하면 리다이렉트도 정상적으로 되고, 코드값도 쿼리파람을 통해 확인할 수 있었음
```
https://nid.naver.com/oauth2.0/authorize?response_type=code&client_id=XXXXXXXXXXXX&redirect_uri=http%3A%2F%2F127.0.0.1%3A8080%2FnaverLogin&state=78577e6c-3f10-4146-910c-3fd4e684dfb5
```

그래서 네이버 아이디로 로그인 버튼 클릭시 axios를 활용하지 않고 위 주소로 페이지 이동을 하도록 처리함.
```
window.location.href = authUri
```

리다이렉트 된 곳에선 쿼리파람을 통해 얻은 code, state를 서버로 보내도록 함
이후 서버에선 `https://nid.naver.com/oauth2.0/token`을 호출하여 토큰을 얻고 `https://openapi.naver.com/v1/nid/me` 에 접속해서 사용자 데이터를 얻어옴.

http get 함수로만 구현되어있는게 약간 독특했던 기억..
