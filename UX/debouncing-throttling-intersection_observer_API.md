# Debouncing, Throttling, 그리고 IntersectionObserverAPI

## 어쩌다가..?
* 비동기로 스크롤목록을 그려주는 UI를 개발하면서 필요해서

## 간단정리
* Debouncing / 여러 번 호출 ⇒ 마지막호출 기준 특정시간 딜레이 후 한 번만 처리
  * 활용예: 검색어 입력할 때, 타자를 칠 때마다 검색요청을 하지 않고 마지막 이벤트 기준으로 검색결과를 요청함
* Throttling (먼저 호출 ⇒ 일정시간동안 호출무시)
  * 활용예: 슬라이드를 동작시킬 때, animation 되느 동안에는 또 다른 슬라이딩 이벤트(prev, next)를 발생시키지 않도록 함
* IntersectionObserverAPI
  * 새로 나왔다고 해서 공부 필요! 스크롤 이벤트에서 활용?

## 봐야 할 자료
* [너는 나를 본다: 지연 방법, 레이지 로드와 IntersectionObserver의 동작 · codepink/codepink.github.com Wiki · GitHub](https://github.com/codepink/codepink.github.com/wiki/%EB%84%88%EB%8A%94-%EB%82%98%EB%A5%BC-%EB%B3%B8%EB%8B%A4:-%EC%A7%80%EC%97%B0-%EB%B0%A9%EB%B2%95,-%EB%A0%88%EC%9D%B4%EC%A7%80-%EB%A1%9C%EB%93%9C%EC%99%80-IntersectionObserver%EC%9D%98-%EB%8F%99%EC%9E%91)
* [Intersection Observer API - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
* [레진 기술 블로그 - IntersectionObserver를 이용한 이미지 동적 로딩 기능 개선](https://tech.lezhin.com/2017/07/13/intersectionobserver-overview)
* [Intersection Observer API의 사용법과 활용방법 · Yoon’s devlog](http://blog.hyeyoonjung.com/2019/01/09/intersectionobserver-tutorial/)
