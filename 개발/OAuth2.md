# OAuth2

OAuth(Open Authorization) : 표준 access delegation. 제한된 resource 에 대한 access 를 보장하기 위한 solution 으로 특정 기간 내에 이름/패스워드를 공유하지 않아도 가능하게 함

## 용어

* client : 보호된 resource 에 접근할 수 있는 application
* resource : API-endpoints. Client 는 OAuth protocol 로 보호된 resource 들을 갖고 있음
* authorization servre : client 가 인증하고 client 의 권한을 보장해주는 token 을 갖기 위해 요청하는 end-user
* end-user : resource 에 접근할 수 있는 무언가. 사람/machine-to-machine 도 가능

## Use case

![alt text](<스크린샷 2025-06-01 오후 4.33.06.png>)

1. Client 가 API 에 접근하고 싶음
2. Client 가 Authorization server 에 요청, Authorization server 가 end-user 한테 권한을 요청
3. Authroization server 가 end-user 에 의해 보장된 token 을 생성함
4. Client 가 token 취득
5. Client 가 취득한 token 으로 API 에 접근

**프로토콜은 Application(client) 이 end-user 와 직접 통신하는 것을 방지하기 위함.**

Authorization server 는 end-user 와 보안적으로 안전하게 통신하도록 최적화됨

API 를 요청할 때 token 을 포함해서 보내고 응답으로 data 나 HTTP status 401(Unauthorized) 를 받게 됨

## JWT 

인증된 권한은 토큰의 형태를 띄게 됨(토큰 자체가 인증의 결과). 

token : 내부구조를 알기 어려운 string 등 다양한 형태로 가능

JWT 예시

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

`.` 가 token 을 세 부분으로 나눔 (모두 base64 인코딩된 JSON)

* the header
* the payload
* the signature

### The header

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### The payload

eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ

사용자 세부사항

```
{
 "sub": "1234567890",
 "name": "Yoojin",
 "iat": 1516239022
}
```

### The signature

Token 의 signautre.

아무나 token 을 읽고 생성할 수 있기 때문에 API 가 받는 token 이 변조되지 않았음을 확인하기 위함

## Session 관리

* Token 은 특정 client에 한해 특정 기간 동안만 유효
* Authorization server 는 token 을 발급
* Authorization server 는 같은 end-user 에 대해 여러 client 에 여러 개의 token 을 발급할 수 있음
* Authorization server 에는 활성화된 user-session 이 있을 것. 사용자가 이전에 이미 인증을 했다면 인증 절차를 다시 거치지 않고 token 이 다시 발급됨

사용자가 로그아웃을 성공적으로 했을 경우

* 발급된 token 을 취소함
* Authorization server 에 있는 user-ssion 을 종료함

* 참고
    * https://medium.com/web-security/understanding-oauth2-a50f29f0fbf7
    * https://openid.net/specs/openid-connect-core-1_0.html