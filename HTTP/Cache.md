# Cache
프로젝트 배포 과정에서 캐시 관련 이슈가 생겼었는데, 매끄럽게 대응하기가 어려웠어서 이번 기회에 좀 더 자세히 알아보고자 공부하게 됨.  

---

**키워드 모아놓기**
* location.href = url vs location.replace(url)
* redirect
* 301 moved permanently
* .htaccess
* nuxt, useRedirectCookie
* 리소스 별 캐시정책 및 nginx 설정
  * ![리소스별 캐시설정](https://user-images.githubusercontent.com/23192677/55290593-0b75f300-5410-11e9-9111-5f58290c5b85.png)

**참고자료**
* [HTTP 캐싱](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=ko)
* [Internet Explorer에서 캐싱을 방지하는 방법](https://support.microsoft.com/ko-kr/help/234067/how-to-prevent-caching-in-internet-explorer)
* [HTTP caching](https://developer.mozilla.org/ko/docs/Web/HTTP/Caching)
* [캐시가 동작하는 아주 구체적인 원리](https://parksb.github.io/article/29.html)
* 책: HTTP 완벽가이드
* 코드: [Server Configs 샘플](https://github.com/h5bp/server-configs) // nginx 설정 참고해서 프로젝트 코드랑 비교해보기~

---
HTTP 책  
  
#
리다이렉션 상태코드 (300-399)
* 클라이언트 요청 리소스에 대해 => 다른 위치 사용하라고 알려주거나, 다른 대안 응답을 제공
* 리소스가 옮겨졌을 경우 / 301 (Moved Permanently)
  * 응답) Location: 'http://www......' 헤더를 선택적으로 보내줄 수 있음. 301일때 서버에서.
* 리소스에 대한 로컬복사본을 아직도 유효(서버에 있는 리소스와 같은지(최신인지), 다른지(수정되었는지))하게 사용해도 되는지 묻는 경우 / 304 (Not Modified)
  * 요청) If-Modified-Since: Fri, Oct ... 헤더 전송 / 00년 00월 이후에 수정된 문서일 경우만 가져와..!
  * 응답) 304 / 변경된거 없음! 응답코드만 내려줄테니 로컬복사본 사용해..!
 

#
캐시관련 헤더
* Cache-Control
  * no-store
  * no-cache
  * must-revalidate
  * max-age
* Pragma (하위호환성? 삭제될예정이라고?)
* Expires
* ETag
* 상태
  * cache hit : 대응하는 로컬사본 있음 / 사용 / 서버요청 X
  * cache miss : 대응하는 로컬사본 없음 / -  / 서버요청 O
  * cache revalidate : 대응하는 로컬사본 있음 / 서버에 신선도 검사 후, 사용 (신선도 검사에 대한 HTTP 규칙이 있다고 함) / If-Modified-Since 요청으로 서버에서 체크, 신선하면 304로 응답코드
    * 신선하지 않다면 (부적중), 평범한 200 으로 서버데이터를 보내줌
    * 서버객체가 삭제되었다면, 404 보내고, 로컬사본 삭제됨
  * 적중과 부적중의 구별
    * 두 경우 모두 응답코드는 200 (본문을 가지고 있다)
    * 이때 Date 헤더를 이용 => 응답의 Date 헤더값을 현재시각과 비교하여, 응답 생성일이 더 오래되었다면 캐시를 사용한 응답임을 알 수 있음.


----
구글문서  
  
  
#
확인할 사항
* 각 서버 응답이 올바른 HTTP 헤더 지시문을 제공하여
* 브라우저가 응답을 캐시할 지점과 기간을 지시하는가

#
응답헤더로 캐시를 사용하는 방법
```
GET /file
200 OK
Content-Length: 1024
Cache-Control: max-age=120
ETag: "x234dff"
```
* 1024 바이트 응답을 반환 / 클라이언트에 최대 120초 동안 캐시하도록 지시 / 응답 만료후 리소스가 수정되었는지 확인하는데 사용할 수 있는 유효성검사 토큰('x234dff') 제공
* 120초가 지났고 + 동일한 리소스에 대해 새 요청을 실행하려 할 경우?
   * 우선 브라우저는 로컬 캐시를 확인하고 이전 응답을 찾음
   * 응답이 만료되었음을 확인
   * 바로 전체 새 응답을 가져오지 않고, 만료된 캐시의 유효성 검사 (ETag 헤더의 토큰값(디지털 지문) 활용 / 재활용해서 쓸 수 있나?)를 함
      * 유효성 검사를 위해, 클라이언트가 요청헤더 내에 `If-None-Match: 'x234dff'` 라고 ETag의 토큰값을 자동으로 제공  `==> 그렇다면 기존 ETag값도 따로 저장하고 있다는 거넹?` 
      * 토큰이 변경되지 않은 경우: 서버에서 '304 Not Modified' 응답 반환 (리소스 변경 없으니 재활용해서 써랏, 유효기간도 120초 추가하구 / 응답을 다시 다운로드할 필요가 없으므로 시간과 대역폭이 절약됨)
      * 토큰이 변경된 경우: '200 OK' 내리면서 새 리소스 응답해줌
* 브라우저가 알아서 (자동으로) 토큰이 이전에 지정되었는지 탐지하고, 요청에 토큰을 추가해주고, 수신된 응답에 따라 필요하면 캐시 타임스탬프 업데이트까지 해줌
* 우리는 서버가 ETag 토큰을 제공하는지 확인하기만 하자~

# 
Cache-Control
* 
* 

#
캐시된 응답 무효화 및 업데이트
* 
* 

#
캐싱 체크리스트
* 
* 
* 
* 
* 






