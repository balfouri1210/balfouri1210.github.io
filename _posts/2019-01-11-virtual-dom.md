---
layout: media
title: "Virtual DOM"
comments: true
category: [ tech, vue ]
image:
  teaser: post_thumbnail/virtual_dom.png
---

가상 DOM에 대하여

가상 DOM, Virtual DOM, Shadow DOM 같은 용어를 들어본적이 있으신가요?  
DOM은 알겠는데, 가상이라고?  
모호한 개념에 검색하기는 귀찮은분들을 위해 압축하여 설명드리고자 합니다.  

**"브라우저가 할 일을 대신해주는 착한 친구"** 라고 이해하시면 편합니다.  

![My helpful screenshot](/images/vue/what_is_virtual_dom.png)  

SPA특성상 페이지 새로고침 없는 DOM조작이 매우 빈번하게 일어납니다.  
이 때마다 브라우저에게 업데이트를 지시한다면 **Reflow** 와 **Repainting** 이 수없이 반복되고, 당연하게도 성능은 나빠질 것입니다.  

가상 DOM은 Vue, React와 같은 프레임워크가 위와 같이 브라우저에게 업데이트를 요청하기 전에 모든 요청을 받아 기존 DOM을 업데이트 하고, 최후의 DOM을 브라우저에게 전달합니다.  
결과적으로 브라우저 입장에서는  
A - B - C - D - E 의 과정이  
A - E  
로 압축되는 것이죠.  

물론 업데이트 규모가 커지는 것은 사실이지만 조금씩 여러번 바꾸는 것 보다  
한 큐에 왕창 끝내버리는게 더 빠릅니다.  
유저가 보게되는 화면은 E로 항상 동일하므로 기존 방식보다 효율적이라고 할 수 있습니다.  

이번 포스트는 다음 두 블로그를 참고하여 작성했습니다.  
[리액트에 대해서 그 누구도 제대로 설명하기 어려운 것 – 왜 Virtual DOM 인가?](https://velopert.com/3236)  
[가상돔(Virtual DOM)?](https://blog.hanumoka.net/2018/08/15/web-20180815-web-virtual-dom/)  
