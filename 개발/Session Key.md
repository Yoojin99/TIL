# Session Key

Session Key : **한 communication session 내에서 모든 메세지들을 encrypting 하는데 사용되는 일회용 대칭 키**

* 특징
    * 비대칭 키 : encryption / decryption 에 같은 키를 사용함
    * 일회용 : 하나의 세션에서만 사용됨.

* 세션 키를 사용하는 이유
    * 특정 키로 암호화한 데이터가 많을수록 암호 해독 공격이 더 쉬워짐. 특정 키를 사용해서 처리된 데이터의 양을 제한하면 이런 공격을 어렵게 할 수 있음. (Session key 는 하나의 session 을 위한 일회용 key 이기 때문에 session 이 끝나면 discard 됨.)
    * 비대칭 암호화는 모든 usecase 에서 사용하기에는 속도가 느리고 모든 secret key 알고리즘은 key 가 안전하게 배포되는 것을 요구함. 대칭 알고리즘을 위한 secret key 를 비대칭 알고리즘을 사용해서 암호화하면 성능 문제를 일부 해결할 수 있음. TLS, PGP 에서 사용.
        * 비대칭 암호화 : 느림
        * 대칭 암호화 : 빠르지만 key 배포시 보안 이슈 있음
        * hybrid 방식 : 대칭 session key 를 전송하기 위해 비대칭 암호화 사용

## Session

Session : **사용자 - web server 간 통신**

e.g. 브라우저 - 웹 서버간 request-response. 사용자가 웹 서버에 page 요청시 시작하고 브라우저가 서버가 요청한 모든 packet 정보들을 보내면(e.g. 사용자가 로그인 함) 완료됨

## Session Key 사용 flow

![alt text](<스크린샷 2025-06-01 오후 4.14.51.png>)

* Alice 가 메세지를 전송 : 메세지를 random으로 생성된 session key 로 encrypt, timestamp 함
* 서버가 메세지를 받은 : session key 로 decrypt 해서 Alice 의 이름과 메세지의 timestamp 를 확인 후 유효한 경우 메세지를 인증함


* 참고
    * https://en.wikipedia.org/wiki/Session_key
    * https://www.techtarget.com/searchsecurity/definition/session-key
    * https://www.cloudflare.com/ko-kr/learning/ssl/what-is-a-session-key/