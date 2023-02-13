## 모바일 보안 관련 추가 내용

웹은 주소가 공개되고, 환경 자체가 쿠키 값 탈취 가능성이 크기 때문에 쿠키보다는 세션을 사용해야 한다. 허나 앱은 정보 탈취 가능성이 낮다.

하지만 중간 탈취 가능성이 적다고 하더라도, 보안을 전혀 적용하지 않을 수는 없다. 브라우저에서 쿠키 값을 이용해서 어뷰징 할 수 있듯이 앱에서는 `리버스 엔지니어링`을 활용해서 API 어뷰징을 할 수 있다.

이런 내용을 막기 위해서 서버와 통신에 있어서 다른 장치가 필요하다. 모바일 앱을 상대로도 서버 쪽에 인증 허가가 필요한 것이다. 이때 앱은 기본적인 브라우저에서의 세션과 같은 내용을 사용하지 않기 때문에, 크게 두 가지 방법을 사용해서 리퀘스트의 유효성을 검증한다.

### 1. 앱의 고유 해쉬값을 서버로 전달

앱이 고유로 가지는 해쉬값을 서버로 전달한다. (apk 안에 `META-INF/CERT.SF` 파일이 존재한다. 여기서 signing 및 앱 버전에 의존하는 고유 해쉬값을 추출 할 수 있다고 한다.)

이 값을 서버 DB에 넣어두고 클라이언트가 요청할 때, 이 값이 같을 때만 리퀘스트를 허가하는 방식으로 보안을 높일 수 있다.

### 2. play integrity

`play intergrity`는 앱 출처가 실제 스토어인지 검증하는 방식이다. 서버와 구글서버가 앱을 사이에 두고 서로 고유 값을 통해서 소통한다.

1. 앱은 서버로 부터 고유 값을 받아온다.
2. 이 값과 함께 play intergrity api를 통해서 인증을 요청한다.
3. 정상적인 앱이라면 이에 해당하는 토큰 값을 보내준다.
4. 이 토큰 값을 다시 서버와 소통할 때 사용한다.
5. 서버는 토큰 값을 구글(플레이)서버로 전달해서 유효성을 검증한다.