## AccessToken과 Refresh Token

### AccessToken과 Refresh Token의 필요성  
JWT 토큰은 클라이언트의 인증·인가 정보를 담고 있는 토큰이다. 서버와 클라이언트는 JWT 토큰을 통해 안전하게 통신하며, 서버는 토큰을 확인해 사용자의 신뢰성과 권한을 검증한 뒤 데이터를 반환한다.  
문제는 이 토큰이 **탈취될 수 있다**는 점이다. 서버는 토큰의 원 주인과 탈취한 사용자를 구분할 수 없기 때문에, 위험을 줄이려면 **유효 기간**을 설정해야 한다.

#### 유효 기간을 어떻게 설정할까?  
- 유효 기간이 **너무 짧으면** 사용자가 로그인을 자주 해야 해 불편하다.  
- 유효 기간이 **너무 길면**  토큰 탈취 시 보안 위험이 커진다.  

따라서 **유효 기간이 서로 다른 두 개의 토큰**을 클라이언트에 전달해 보안과 편의성을 모두 확보한다.

### AccessToken과 Refresh Token  
- **Access Token** : 유효 기간을 짧게 설정  
- **Refresh Token** : Access Token보다 긴 유효 기간을 부여  

일반 API 호출은 Access Token으로 처리하고, Access Token이 만료되면 Refresh Token을 통해 서버로부터 새 Access Token을 발급받는다.

### AccessToken과 Refresh Token 생성·사용 과정  
1. 클라이언트가 아이디·비밀번호로 로그인 요청을 보낸다.  
2. 서버는 정보를 확인한 뒤 Access Token과 Refresh Token을 발급해 반환한다.  
3. 클라이언트는 두 토큰을 로컬에 저장한다.  
4. 이후 모든 API 요청에 Access Token을 헤더에 포함해 전송한다.  
5. 서버가 Access Token의 유효 기간을 확인한다.  
6. 유효하면 요청을 처리하고, 만료되었으면 **401 Unauthorized**로 응답한다.  
7. 클라이언트는 401 응답으로 Access Token이 만료되었음을 인지한다.  
8. 클라이언트는 헤더에 Refresh Token을 담아 서버에 토큰 재발급 요청을 한다.  
9. 서버는 Refresh Token을 검증한 뒤, 새 Access Token을 헤더에 담아 응답한다.  
10. Refresh Token도 만료되었다면 서버는 다시 401을 반환하고, 클라이언트는 재로그인을 통해 토큰을 새롭게 발급 받는다.

### Access Token과 Refresh Token의 효과  
공격자가 Access Token을 탈취하더라도 **짧은 유효 기간**이 지나면 토큰이 만료되므로 다시 탈취를 시도해야 한다.  
JWT는 `Header`, `Payload`, `Signature` 세 부분으로 구성된다. 
토큰의 유효기간의 경우 Payload에 기입하는데, 만약 Payload의 유효 기간을 공격자가 임의로 변경해도 Signature 값은 바뀌지 않는다.
서버가 Signature에서 복호화한 Payload 값과 공격자가 변경한 Payload 값이 일치하지 않는다는 것을 비밀키를 통해 알 수 있고, 이를 통해 서버는 요청한 사용자에게 정보를 내어주지 않아 안전하다.
