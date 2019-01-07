---
layout: media
title: "Composition of Vuex Store"
comments: true
category: [ tech, vue ]
image:
  teaser: post_thumbnail/vuex_store_composition.png
---

Composition of Vuex Store

Store를 구성하고 있는 다섯가지 요소에 대해 알아봅니다.

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






Vuex와 관련된 모든 내용은  
[Vuex 공식 가이드](https://vuex.vuejs.org/kr/guide/)에서 확인할 수 있습니다.  
