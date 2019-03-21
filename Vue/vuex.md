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

`Getters, Mutations`
* => 컴포넌트 내에서 store를 다루는 코드들을 감싸서 추상화시켜서 접근할 수 있게 해주는 도구들?
* => 가독성을 높여주는 동시에, 의존성을 낮춰주니 좋다!

Getters
* `this.$store.getters.${getterName}`
* store의 원본 데이터를 가지고 반복되는 일을 거쳐 가공된 데이터로 얻어와야 할 때, store내에서 getter메서드로 추상화하여 호출가능 (네임스페이스가 길거나, 새 데이터를 다시 계산할 때(곱하기, 필터, 맵...) 등등)
* 각 컴포넌트에서 직접 store를 가공한 코드를 반복적으로 쓰지 않아도 됨

```javascript
// store.js
export const store = new Vuex.Store({
  // ...
  getters: {
    getCounter: function (state) {
      return state.counter;
    },
    getDoneTodos: function (state) {
      return this.store.state.todos.filter(todo => todo.done)...
    }
  }
});

// App.vue
computed: {
  parentCounter() {
    this.$store.getters.getCounter;
  }
},
```

 * mapGetters
  * getters를 호출하는 코드부분을 좀 더 직관적으로 읽히게 도와주는 듯 (맵형식으로 호출), 특히 여러 개의 gettter를 가져다 써야할 때 좋겠다.
```javascript
computed: {
  parentCounter() {
    this.$store.getters.getCounter;
  }
},

computed: {
 ...mapGetters({
   parentCounter : 'getCounter', // getCounter 는 Vuex 의 getters 에 선언된 속성 이름
   'getCounter', // 이렇게도 사용가능
 }),
}
```

Mutations


## 유용/참고 링크
* https://joshua1988.github.io/web-development/vuejs/vuex-start/
* https://joshua1988.github.io/web-development/vuejs/vuex-getters-mutations/
* https://joshua1988.github.io/web-development/vuejs/vuex-actions-modules/
