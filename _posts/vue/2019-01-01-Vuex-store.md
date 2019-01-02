---
layout: media
title: "Vuex Store"
comments: true
category: [ tech, vue ]
image:
  teaser: post_thumbnail/vuex_store.png
---

Store of Vuex

Store는 Vuex의 중심적 역할을 담당합니다.  
애플리케이션이 어떤 **'상태'** 인지를 알고 있는 **'저장소'** 라고 생각하면 쉽습니다.  

```js
let count = 0;
```

위처럼 선언된 전역객체와 뭐가 다른가 생각할 수 있는데요,  
공식문서에서는  

- Vue component와 반응형이다. 즉 컴포넌트 업데이트가 자동으로 이루어진다.  
- 저장소의 상태를 직접 변경할 수 없고, commit이라는 액션을 통해서만 가능하다.  

라고 차이점을 소개하고 있습니다.  

가장 간단한 Vuex 코드를 작성해볼까요?

```js
import Vue from 'Vue'
import Vuex from 'Vuex'

Vue.use(Vuex) // Vue야 Vuex도움좀 받을게

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    countIncrement (state) {
      state.count++
    }
  }
})
```

count라는 상태를 저장하는 Vuex store가 완성됐습니다.  
Vue component에서 아래와 같이 접근 및 변경할 수 있습니다.


```js
store.commit('countIncrement')
console.log(store.state.count)
```

**countIncrement** 라는 commit을 통해 state.count의 값을 변경하는 것을 볼 수 있습니다. 전역변수를 직접 변경하는 것 보다 명시적이고, 코드를 읽을 때 상태변화를 더 잘 지켜볼 수 있습니다.  

Vuex store 객체는 다섯가지 파라미터를 전달받아 만들어지는데요,

- State
- Getters
- Mutations
- Actions
- Modules

다음 포스트에서 하나씩 알아보도록 하겠습니다.

Vuex와 관련된 모든 내용은  
[Vuex 공식 가이드](https://vuex.vuejs.org/kr/guide/)에서 확인할 수 있습니다.  
