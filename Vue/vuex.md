# Vuex

* Vue의 상태관리르 위한 패턴이자 라이브러리
* 상태관리?!
  * 컴포넌트기반으로 분리된 많은 객체들이 한 페이지에서 조립됨 -> 컴포넌트간의 데이터 통신을 한 곳에서 관리하기 쉽게 구조화
  * 기존 문제점
    * 상위에서 하위로 데이터전달시 거쳐야할 컴포넌트가 너무나 많음
    * event bus를 활용할 수 있으나, 상하위 관계가 아닌 컴포넌트 간 통신 시 관리X
  * 해결책 => 모든 데이터통신을 한 곳에서 관리
    * 사이클: Component -> (dispatch) -> Action -> (commit) -> Mutations -> (mutate) -> State -> **(render)** 
    * 위 사이클이 반복됨
* 통신 방법
```javascript
// parent

<div id="app">
  Parent counter : {{ this.$store.state.counter }} <br>
  <button @click="addCounter">+</button>
  <button @click="subCounter">-</button>

  <!-- 기존 코드 -->
  <!-- <child v-bind:passedCounter="counter"></child> -->
  <child></child>
</div>

import Child from './Child.vue'

export default {
  // 기존 코드
  // data () {
  //   return {
  //     counter: 0
  //   }
  // },
  methods: {
    addCounter() {
      this.$store.state.counter++;
    },
    subCounter() {
      this.$store.state.counter--;
    }
  },
  components: {
    'child': Child
  }
}

// child
<div>
  <hr>
  Child counter : {{ this.$store.state.counter }} <br>
  <button>+</button>
  <button>-</button>
</div>
export default {
  // 기존 코드
  // props: ['passedCounter']
}
```
* 기타 알아둘 개념
  * Getters, Mutations, Actions


## 유용/참고 링크
* https://joshua1988.github.io/web-development/vuejs/vuex-start/
* https://joshua1988.github.io/web-development/vuejs/vuex-getters-mutations/
* https://joshua1988.github.io/web-development/vuejs/vuex-actions-modules/
