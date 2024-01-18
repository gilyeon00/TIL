1. **Access Token**
    - Access Token은 사용자가 시스템에 인증 된 후 사용자 세션을 관리하는 데 사용되는 작은 정보 조각입니다. 이 토큰은 사용자가 서버에 특정 리소스에 접근하려고 요청할 때 함께 전송되어, 서버가 이 토큰을 검증함으로써 사용자의 인증 상태를 확인합니다.
    - Access Token은 주로 짧은 만료 기간을 가지는데, 이는 만약 토큰이 탈취당하더라도 공격자가 그 토큰을 오랜 시간 동안 이용할 수 없게 하기 위함입니다.
2. **Refresh Token**
    - Refresh Token은 Access Token이 만료되었을 때, 새로운 Access Token을 발급받는 데 사용되는 토큰입니다.
    - Refresh Token은 일반적으로 Access Token보다 긴 만료 기간을 가지며, 이는 사용자가 지속적으로 애플리케이션에 로그인 상태를 유지할 수 있게 하기 위함입니다.
    - Refresh Token은 사용자가 매번 로그인을 다시 할 필요 없이, 새로운 Access Token을 생성하고 받는 데 사용됩니다.


# JWT 로그인 과정

1. 사용자가 ID와 Password로 로그인을 요청합니다.
2. 서버는 사용자의 인증 정보를 확인하고, 인증이 성공하면 JWT Access Token과 Refresh Token을 생성합니다.
3. 생성된 Access Token과 Refresh Token은 클라이언트에게 전달됩니다. 이때 Access Token은 클라이언트의 HTTP 헤더, 쿠키, 로컬 스토리지 등에 저장될 수 있으며, 이후 사용자가 서버에 요청을 보낼 때마다 이 Access Token을 함께 보냅니다.
4. 서버는 클라이언트로부터 받은 Access Token을 검증하고, 검증이 성공하면 요청한 리소스에 대한 접근을 허용합니다.
5. Access Token이 만료되면, 클라이언트는 Refresh Token을 이용해 새로운 Access Token을 요청합니다. 서버는 Refresh Token을 검증하고, 검증이 성공하면 새로운 Access Token을 생성해 클라이언트에게 전달합니다.
6. 만약 Refresh Token이 만료되면, 사용자는 다시 로그인을 해야 새로운 Access Token과 Refresh Token을 받을 수 있습니다.

   이렇게 Access Token과 Refresh Token을 이용하면, 보안을 유지하면서도 사용자에게 좋은 사용 경험을 제공할 수 있습니다. Access Token은 짧은 만료 시간으로 인해 보안성을, Refresh Token은 긴 만료 시간으로 인해 사용자 편의성을 보장합니다.

   ## **refreshToken** 메서드

   ```java
   public TokenDto refreshToken(String refreshToken) {
       if (validateToken(refreshToken)) {
         // refreshToken이 유효하다면 새로운 accessToken을 생성
         Authentication authentication = getAuthentication(refreshToken);
         return generateTokenDto(authentication);
       } else {
         // refreshToken이 유효하지 않다면 refreshToken을 제거
         String username = getUsername(refreshToken);
         refreshTokenService.deleteRefreshToken(username);
         throw new RuntimeException("Refresh token is not valid.");
       }
     }
   ```

   1. 사용자가 처음 로그인할 때, 서버는 Access Token과 Refresh Token을 모두 발급하여 클라이언트에게 전달합니다. 클라이언트는 이를 적절하게 저장해둡니다.
   2. 사용자가 보호된 자원에 접근하려 할 때, 클라이언트는 Access Token을 헤더에 포함하여 요청을 보냅니다.
   3. Access Token이 만료되었다는 응답을 받았을 때, 클라이언트는 Refresh Token을 사용하여 새로운 Access Token을 요청합니다. 이 때 **`refreshToken`** 메서드가 호출됩니다.

   **`refreshToken`** 메서드는 별도의 요청에 대한 핸들러나 서비스 로직 내에서 호출되어야 합니다. 클라이언트가 Refresh Token을 통해 새로운 Access Token을 요청할 때 이 메서드가 호출되어야 하며, 이때 Refresh Token의 유효성을 검사하고 필요한 작업(새로운 Access Token 발급, 유효하지 않은 Refresh Token 삭제 등)을 수행합니다.

   ### **validateToken** 메서드에서 **refreshToken 메서드를 호출해야할까?**

   <aside>
   💡 사용자가 로그인을 이미 하고 나서, access token이 만료 됐을때 refresh 토큰을 검증하고 재생성하든지 제거하든지 하는거잖아
   그러면 access token이 만료 됐을때 refreshToken를 호출하는게 아닐까?

   </aside>

   → **validateToken** 메서드는 Access Token의 유효성을 검사하는 것에 초점을 맞추고 있기 때문에, 여기서 바로 **`refreshToken`** 메서드를 호출하는 것은 적절하지 않다.

   클라이언트가 새로운 Access Token을 요청하는 API 엔드포인트를 따로 설정하고, 이 엔드포인트에 대한 요청을 처리하는 컨트롤러 메서드에서 **`refreshToken`** 메서드를 호출해야 합니다. 이 컨트롤러 메서드는 클라이언트로부터 Refresh Token을 받아 **`refreshToken`** 메서드에 전달하고, 이 메서드는 Refresh Token의 유효성을 검사한 후 새로운 Access Token을 생성하거나 유효하지 않은 Refresh Token을 삭제하는 등의 작업을 수행합니다.

   그러므로, 사용자가 로그인 후 Access Token이 만료되었을 때 **`refreshToken`** 메서드를 호출하려면, 클라이언트는 Access Token이 만료되었음을 알게 될 때마다 새로운 Access Token을 요청하는 API 엔드포인트에 요청을 보내야 합니다.

   다시 말해, 클라이언트가 Access Token의 만료를 감지하면 (예를 들어, 서버로부터 401 Unauthorized 응답을 받으면), 클라이언트는 저장해둔 Refresh Token을 사용하여 새로운 Access Token을 요청하는 API 엔드포인트에 요청을 보내는 것입니다. 이 때 서버는 **`refreshToken`** 메서드를 호출하여 이 요청을 처리하게 됩니다.
    
   ---

   ## getAuthentication 메서드

   ```java
   // JWT 토큰에서 인증 정보 조회
     public Authentication getAuthentication(String accessToken) {
       Claims claims = parseClaims(accessToken);
    
       if (claims.get(AUTHORITIES_KEY) == null) {
         throw new RuntimeException("권한 정보가 없는 토큰입니다.");
       }
    
       Collection<? extends GrantedAuthority> authorities =
           Arrays.stream(claims.get(AUTHORITIES_KEY).toString().split(","))
               .map(SimpleGrantedAuthority::new)
               .collect(Collectors.toList());
       UserDetails principal = new User(claims.getSubject(), "", authorities);
    
       return new UsernamePasswordAuthenticationToken(principal, "", authorities);
     }
   ```

   이 메서드는 주어진 토큰(대게 Access Token)이 유효하다고 가정하고,
   해당 토큰에서 인증 정보를 추출하여 **`Authentication`** 객체를 생성합니다.
   이 메서드가 반환하는 **`Authentication`** 객체는 보통 **`SecurityContextHolder`**에 저장되어, 이후에 사용자 인증이 필요한 작업을 처리하는 동안 참조합니다.

   [Refresh Token 도입기(F12)](https://medium.com/@uk960214/refresh-token-도입기-f12-dd79de9fb0f0)