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

## Getters, Mutations
* => 컴포넌트 내에서 store를 다루는 코드들을 감싸서 추상화시켜서 접근할 수 있게 해주는 도구들?
  * `공부할 포인트: 컴포넌트 내에서 this.$store 로 접근하는 것은 안티 패턴 / vue의 reactivity체계, 상태관리 패턴 에 맞지않음 / 여러개의 컴포넌트에서 같은 state 값을 동시에 제어하면, 어느 컴포넌트에서 호출해서 변경된 건지 추적이 어려움 / 상태변화를 명시적으로 수행함으로써 테스팅, 디버깅, vue의 reactive 성질 준수 혜택을 얻는다.`
* => 가독성을 높여주는 동시에, 의존성을 낮춰주니 좋다!

### Getters
* 코드
  * computed에 등록하여 사용
  * `this.$store.getters.${getterName}`
  * `...mapGetters([ 'getCounter' ])`
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

#### mapGetters
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

### Mutations
* 코드
  * method에 등록하여 사용 ==> `왜 다를까? getter랑`
  * `this.$store.commit('addCounter')` // getter와 다르게 commit을 사용하여 호출해야 함. `추적가능한 상태변화를 위해 프레임워크가 구조화 된 부분이라는데? 알아보기. setter는 getter와 다르게 store의 state값을 변화시켜줘야하니까, 아마도 등록과정을 거쳐서 여러군데서 호출되면 어느 컴포넌트에서 어떤 파라미터로 어떻게 고쳐서 보낼지를 정리하느 과정이 필요해서 그럴거 같음.`
  * `...mapMutations([ 'addCounter' ]) // 
* (getter와 비슷하게) store 데이터 조작을 메서드로 정의하여 호출가능한 **Setter**
* `actions와의 차이점: mutations는 동기적 로직 정의 / actions는 비동기적 로직 정의 ===> 이 부분은 더 알아봐야 할듯`
* `commit`을 이용하여 state 변경
  * component에서 commit -> mutations에서 chage -> vuex의 state 변경

```javascript
// store.js
export const store = new Vuex.Store({
  // ...
  mutations: {
    addCounter: function (state, payload) {
      return state.counter++;
    },
    /*
    // payload가 { value: 10 } 일 경우
    addCounter: function (state, payload) {
      state.counter = payload.value
    }
    */
  }
});

<!-- App.vue -->
<div id="app">
  Parent counter : {{ parentCounter }} <br>
  <button @click="addCounter">+</button>
  <!-- ... -->
</div>

// App.vue
methods: {
  addCounter() {
    // this.$store.state.counter++;
    this.$store.commit('addCounter');
    this.$store.commit('addCounter', 10);
    this.$store.commit('addCounter', {
      value: 10,
      arr: ['a', 'b', 'c']
    });
  }
},

```

### Actions
* Mutations는 순차적 로직을, Actions에는 비순차적 로직 또는 비동기 처리 로직을 선언
* 왜? 이렇게 나눌까? `==> 설명을 봤는데 필요성은 알겠으나 완전히 이해가 안가네..두 개가 어떻게 내부적을 돌아가는지를 좀 더 봐야할듯.?! 아니 알 것 같기도... action에서 비동기 처리후 mutation을 호출하여 해당시점에 동기처리. 이렇게~`
* 어쨌든 actions가 비동기적인 로직을 처리 후, mutation으로 store를 set할 수 밖에 없다능.. context로 접근해서 commit한다.
```javascript
// store.js
export const store = new Vuex.Store({
  // ...
  mutations: {
    addCounter: function (state, payload) {
      return state.counter++;
    }
  },
  actions: { // Actions 등록
    addCounter: function (context) {
      return context.commit('addCounter'); // commit은 mutation메서드 호출방법임
    },
    getServerData: function (context) {
      return axios.get("sample.json").then(function() {
        // ...
      });
    },
    delayFewMinutes: function (context) {
      return setTimeout(function () {
        context.commit('addCounter');
      }, 1000);
    }
  }
});
```

## 참고 자료
* https://joshua1988.github.io/web-development/vuejs/vuex-start/
* https://joshua1988.github.io/web-development/vuejs/vuex-getters-mutations/
* https://joshua1988.github.io/web-development/vuejs/vuex-actions-modules/

