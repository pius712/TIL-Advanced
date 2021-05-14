# Oauth / kakao

## Oauth 2.0 Flow

### Implicit grant flow

1. 유저가 버튼을 클릭 -> 로그인 화면으로 리다이렉트
2. Auth Service Provider가 유저의 정보를 기입 받고, token을 발행
3. Provider가 앱으로 토큰을 query parameter와 같은 방식으로 돌려줌
4. 앱이 이 토큰을 갖고, Provider에 access 해서 정보 가져옴

### 



## 일반적인 Oauth 플로우
1. 서버로 인증 코드 요청
2. 클라이언트로 인증 코드 전달
3. 인증 코드를 서버로 전달
4. 토큰 발급 후 클라이언트로 토큰 전달
5. 토큰을 http header에 넣어서 api 호출
6. 호출에 대한 응답

## 플로우 (카카오)
위의 Oauth 플로우 + 카카오 로그인을 통한 서비스 회원가입 ?

1. 카카오 계정으로 로그인
2. 계정과 서비스를 연결
3. 사용자의 정보를 통해 회원 가입


### 서버로 인증 코드 요청


리퀘스트 과정은 실제로 rest로 직접 하지 않고, 카카오 SDK를 통해서 리액트에서 실행한다.

[Kakao Developers](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code)

```bash
GET /oauth/authorize?client_id={REST_API_KEY}&redirect_uri={REDIRECT_URI}&response_type=code
```

Request Parameter (query string)

- client_id : REST API key
- redirect_uri: 인증 코드가 리다이렉트될 uri
- response_type

위와 같이 요청을 하게 되면,  Response로 아래의 정보가 들어온다.

- code : 인증 코드
- state
- error: 실패시 에러 코드
- error_description : 실패시 메시지

우선 리액트에서 일반적인 카카오 로그인 화면을 띄우기 위해서는, https://developers.kakao.com/docs/latest/ko/kakaologin/js 여기서 SDK를 설치해야한다.

그리고 버튼 클릭시 JavaScript SDK의 `Kakao.Auth.authorize()` 함수로 요청을 하고, 사용자가 로그인을 마치면 ?

-> redirect url (서버의 주소) 로 해당 인증코드를 보내준다.


### 인증 코드로 토큰 발급 받기

위 과정을 통해 서버에서 인증코드를 받고, 서버에서 토큰 발급을 요청한다.

[Kakao Developers](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-token)
```bash
POST /oauth/token HTTP/1.1
Host: kauth.kakao.com
Content-type: application/x-www-form-urlencoded;charset=utf-8
```

| Name | Type  | Description  | Required |

- grant_type |  String  | authorization_code로 고정   |O
- client_id | String | 앱 생성 시 발급받은 REST API  | O
- redirect_uri | String | 인가 코드가 리다이렉트된 URI  | O
- code  | String 인가 코드 받기 요청으로 얻은 인가 코드 |  O
- client_secret | String | 토큰 발급 시, 보안을 강화하기 위해 추가 확인하는 코드
  [내 애플리케이션] > [보안]에서 설정 가능
  ON 상태인 경우 필수 설정해야 함  | X

위에 대한 Response 로 엑세스 토큰과 리프레시 토큰을 받게 된다.

발급받은 엑세스 토큰을 통해서 `사용자 정보 가져오기` 을 사용할 수 있다.

음.. 여기서 하나의 사이클에서 수행되어야하나??
아니면 받은  엑세스 토큰이랑 리프레시 토큰을 데이터 베이스에 저장해야하나..?

-> 만약에 클라이언트(리액트)에서 토큰이 필요하다면, kakao sdk를 통해서 받을 수 있다.




### 다시 로그인 ?

브라우저를 껐다가 다시 서비스에 들어오는 경우는 어떻게 되나?

로그인시 동의 화면 없이 바로 인증코드 발급.

토큰 발급 후 -> 디비에 없으면 저장 / 있으면 Jwt 토큰


토큰으로 인증 후 카카오 계정을 통해 자체 서비스의 데이터베이스에 회원으로 등록하는 과정은 자체적으로 구현해야 한다.


#로그인