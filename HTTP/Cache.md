캐시에 대해 자세히 알 필요가 생겨서 찾아보는 중...   
   
### 키워드 모아놓기
* location.href = url vs location.replace(url)
* redirect
* 301 moved permanently
* .htaccess
* nuxt, useRedirectCookie
* 리소스 별 캐시정책 및 nginx 설정
  * ![리소스별 캐시설정](https://user-images.githubusercontent.com/23192677/55290593-0b75f300-5410-11e9-9111-5f58290c5b85.png)


### 리다이렉션 상태코드 (300-399)
* 클라이언트 요청 리소스에 대해 => 다른 위치 사용하라고 알려주거나, 다른 대안 응답을 제공
* 리소스가 옮겨졌을 경우 / 301 (Moved Permanently)
  * 응답) Location: 'http://www......' 헤더를 선택적으로 보내줄 수 있음. 301일때 서버에서.
* 리소스에 대한 로컬복사본을 아직도 유효(서버에 있는 리소스와 같은지(최신인지), 다른지(수정되었는지))하게 사용해도 되는지 묻는 경우 / 304 (Not Modified)
  * 요청) If-Modified-Since: Fri, Oct ... 헤더 전송 / 00년 00월 이후에 수정된 문서일 경우만 가져와..!
  * 응답) 304 / 변경된거 없음! 응답코드만 내려줄테니 로컬복사본 사용해..!
 
### 캐시관련 헤더
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


[참고]
  * https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=ko
  * HTTP 완벽가이드
