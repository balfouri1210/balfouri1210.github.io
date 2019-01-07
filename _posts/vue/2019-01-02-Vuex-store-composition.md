---
layout: media
title: "Composition of Vuex Store"
comments: true
category: [ tech, vue ]
image:
  teaser: post_thumbnail/vuex_store_composition.png
---

Composition of Vuex Store

Store를 구성하는 다섯가지 요소에 대해 알아봅니다.

```js
const store = new Vuex.Store({
  state: {...},
  getters: {...},
  mutations: {...},
  actions: {...},
  modules: {...}
})
```

**1. State**  

Store는 애플리케이션에서 **원본 저장소** 의 역할을 담당합니다.  
실질적으로 어떤 판단의 근거가 되는 데이터를 담고 있는 곳이죠.  
원본이기 때문에, Singleton으로 작동합니다.

그렇다면 각각의 Vue 컴포넌트에서 state에 어떻게 접근할 수 있을까요?  
최근 대부분의 JS소스가 Module화 되었으므로 가장 간단한 방법은 store객체를 가져오는 것입니다.  

```js
import store from '/store'

// Counter 컴포넌트
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}  
```

하지만 필요한 모든 컴포넌트에서 store를 가져오는 것은 비효율적입니다.  
vue instance에 store를 주입 (injection)시키는 방법으로 쉽게 해결할 수 있습니다.  
(Angularjs의 Dependency injection과 유사합니다)

```js
const app = new Vue({
  el: '#app',

  // 이곳에 한 번만 주입하면 됩니다.
  store,
  ...
})
```

이렇게 하면 모든 하위 컴포넌트에서 store를 사용할 수 있고  
다음과 같이 접근할 수 있습니다.

```js
this.$store.state.count
```

-----

**2. Getters**  

state값에 기반한 계산 결과가 필요할 때가 있습니다.
예를 들어 볼까요?

```js
state: {
  movieList: [
    { name: 'fast & furious', age: 15 },
    { name: 'inside man', age: 19 },
    { name: 'interstellar', age: 'all' },
    { name: 'harry potter', age: 12 }
  ]
}
```

위와 같은 movieList state가 있습니다.  
이를 가져다 쓰는 컴포넌트에서 19세 영화를 제외한 영화가 몇 개인지 알고싶다면?  
아마도 컴포넌트 로직에서 store.state.movieList 에 접근하여 별도의 처리과정을 거칠 것입니다.  

getters는 이러한 상황에서 유용하게 쓰일 수 있습니다.  
state를 원하는 형태로 가공하여 필요한 정보를 제공하는 역할을 하기 때문입니다.  

```js
getters: {
  movieFilter: state => {
    return state.movieList.filter(movie => movie.age !== 19)
  }

  // 첫 번째 인자로 state,
  // 두 번째 인자로 getters를 받습니다. (다른 getter를 호출할 수 있습니다)
}
```

위와 같이 getters만 설정해주면, 복수의 컴포넌트에서 중복되는 로직을 피할 수 있습니다.  
컴포넌트에서는  

```js
let movieCount = store.getters.movieFilter().length
```

위와 같이 getter를 호출하여 더 간단한 로직을 구성할 수 있습니다.

-----

**3. Mutations**

state 값을 변경할 수 있는 유일한 방법입니다.  
이벤트처럼 동작한다고 이해하면 쉬운데요, 'increment' 라는 이벤트(mutation)이 발생하면
해당 핸들러를 호출하는 것입니다.  
그리고 이벤트를 발생시키는 명령어는 **commit** 입니다.

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 상태 변이
      state.count++
    }
  }
})
```

위와 같이 mutation을 등록하고,
컴포넌트에서

```js
store.commit('increment')
```

이렇게 호출합니다.


mutation은 첫 번째 인자로 state를 받고, 두 번째 인자로 payload를 받을 수 있습니다.  
우리는 이 payload에 필요한 파라미터를 넘기면 됩니다.

```js
// Primitive type 부터
store.commit('increment', 10)

// 객체까지 넘길 수 있습니다.
store.commit('increment', {
  amount: 10
})
```

이때 주의해야 할 점은 mutation을 통해 state에 새 속성을 추가할 때 다음과 같은 규칙이 있다는 것입니다.  

```js
Vue.set(obj, 'newProp', 123) // 혹은
state.obj = { ...state.obj, newProp: 123 }
```

공식문서에서는 다음과 같이 설명하고 있습니다.  
*"Vuex 저장소의 상태는 Vue에 의해 반응하므로, 상태를 변경하면 상태를 관찰하는 Vue 컴포넌트가 자동으로 업데이트됩니다. 이것은 또한 Vuex 변이가 일반 Vue로 작업 할 때 동일한 반응성에 대한 경고를 받을 수 있음을 의미합니다."*


또한 mutation은 **반드시 동기적** 이어야 합니다.  
그 이유는  
1. devtool을 통해 디버깅 할 때 mutation 핸들러 안에서 비동기 호출이 발생하면
해당 호출의 콜백을 추적할 수 없고,  
2. state를 변경하는 두 개 이상의 비동기 호출이 있다고 가정할 때, 어떤 콜백이 먼저 호출될지 장담할 수 없기 때문입니다.  

그렇다면 비동기 처리는 어떻게 할 수 있을까요?  
Vuex에서 mutation과 별개로 제공되는 **Action** 에 대해 알아보겠습니다.


-----


**4. Actions**

action은 mutation과 비슷한 개념이지만,  
**1. state에 직접 접근하지 않고, mutation에 대한 commit을 한다.**  
**2. 비동기 작업을 포함할 수 있다.**  
는 차이점이 있습니다.

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

action 핸들러는 **context** 인자를 받는데요,  
이 context를 통해 context.commit('increment'), context.state, context.getters 처럼 store의 구성요소에 접근할 수 있습니다.  
( context가 store객체 자신은 아닙니다 )

그렇다면 action 핸들러를 호출하기 위해서는 어떻게 해야 할까요?  
mutation 핸들러를 commit 으로 호출했던 것 처럼  
action 핸들러는 dispatch 메소드로 호출합니다.

```js
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

```js
store.dispatch('incrementAsync')
```

위와 같이 **dispatch** 메소드로 action의 'incrementAsync' 핸들러를 호출하고,  
핸들러 안에서 비동기처리를 거친 이후에 **commit** 메소드로 mutation의 'increment' 핸들러를 호출하여  
실제 state 값을 변경하는 구조로 이루어집니다.

그렇다면 컴포넌트 안에서 action의 비동기 호출의 결과를 받아 컨트롤 하려면 어떻게 해야할까요?  
dispatch메소드의 두 특성에 대해 알고 나서 코드를 확인하시면 됩니다.  
1. store의 dispatch 메소드는 Promise를 반환한다.  
2. dispatch 메소드는 action 핸들러에 의해 반환된 Promise 객체를 처리할 수 있다.  

```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

```js
store.dispatch('actionA')
.then(() => {
  ... // success
})
.catch(() => {
  ... // fail
})
```

위와 같은 형태입니다.  
다음과 같이 비동기 콜백 내부에서 다른 액션을 사용할 수도 있습니다.

```js
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

다른 컴포넌트에서 여러 액션(actionA, actionB)들을 호출할 경우, dispatch 메소드에 의해 반환되는 Promise는  
호출된 모든 액션 트리거가 처리된 이후 최종적으로 반환되는 Promise 입니다.  
여기에서는 actionB가 반환하는 actionA의 Promise가 dispatch를 통해 전달될 것입니다.

다음은 마지막 구성요소인 modules 입니다.

-----

**5. Modules**

Vuex 모듈의 핵심은 **비대해진 store의 효율적 관리** 입니다.  
store는 singleton으로 관리되기 때문에 애플리케이션에 쓰이는 다양한 상태들이 하나의 커다란 객체에 포함됩니다.  
자연히 애플리케이션 구조가 커지고, 관리해야 할 상태들이 늘어남에 따라 store는 매우 비대해질 수 있습니다.  

이런 경우를 대비하여 Vuex는 store를 모듈화하여 관리할 수 있도록 지원하고,  

```js
const user = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const transaction = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    user: user,
    transaction: transaction
  }
})

store.state.user // -> user'의 상태
store.state.transaction // -> transaction'의 상태
```

위와 같이 설정할 수 있습니다.
각 모듈은 완벽하게 독립되어 자체 state, getters, mutations, actions를 가질 수 있고 중첩된 modules또한 가질 수 있습니다.

이후 모듈에 대한 자세한 설명은 [공식문서](https://vuex.vuejs.org/kr/guide/modules.html)를 통해 습득하시는 것이 더 정확하고 빠를 것이라고 판단됩니다.  
중첩된 모듈의 context, 접근 방법, 네임 스페이스 등 내용이 조금 많지만
천천히 읽어보시면 이해하는데 크게 어렵지 않은 수준인 것 같습니다.


Vuex와 관련된 모든 내용은  
[Vuex 공식 가이드](https://vuex.vuejs.org/kr/guide/)에서 확인할 수 있습니다.  
