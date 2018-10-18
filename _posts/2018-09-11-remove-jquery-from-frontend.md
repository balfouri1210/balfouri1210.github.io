---
layout: media
title: "Removing jQuery from frontend"
comments: true
category: [ tech, javascript ]
image:
  teaser: post_thumbnail/logo_jquery.png
---

프론트엔드에서 jQuery 걷어내기

[jQuery](https://jquery.com/)는 가장 사랑받는 javascript library 중 하나입니다.  
사용법이 쉬워서 누구나 금방 배울 수 있고 코드량도 획기적으로 줄일 수 있으며, 예제도 방대하죠.  
지금도 아주 많은 곳에서 쓰이고 있는 JS 라이브러리계의 1인자라고 할 수 있습니다.  

그런데 언어가 발전하고 Angular, React, Vue등의 프레임워크가 생겨나면서
jQuery 혼용의 비효율성에 대해 생각하는 개발자가 많아졌고 저역시 업무를 진행하며 기존 jQuery소스 제거의
필요성을 느낄 때가 있었습니다. **메인 프레임워크와의 호환성 문제, 코드 재사용성에 대한 문제** 때문에 말이죠.
그 결과 지금은 UI 구현을 위해 반드시 필요한 경우를 제외한 모든 jQuery소스를 제거한 상태입니다.

그렇다면 **Github**에서는 jQuery를 어떻게 걷어냈을까요.  
그 과정을 담은 글 [Removing jQuery from frontend](https://githubengineering.com/removing-jquery-from-github-frontend/)을
 번역하여 전달해드리겠습니다.  
<span class="caption">(의역, 생략이 다수 있습니다. 정확한 정보는 원문을 참고해주세요.)</span>  

<hr>

우리는 최근에 GitHub.com의 프론트엔드 코드에서 jQuery의존성을 제거하는 마일스톤을 달성했습니다.  
이 라이브러리 없이도 완전한 개발을 할 수 있도록 변화를 주었고, 그 변화가 완료되었다는 뜻이죠.  
이 글에서는  
_1. jQuery에 의존적으로 시작한 역사_  
_2. 왜 jQuery가 필요없다고 느꼈는지_  
_3. 어떻게 jQuery를 대체하는 다른 라이브러리를 구하지 않고, 기본 브라우저 API만으로 원하는 것을 만들었는지_  
에 대하여 설명할 것입니다.

**- 초기에 jQuery와 함께한 이유**  
Github은 2007년 후반에 jQuery 1.2.1을 도입했습니다.  
그때는 구글 크롬의 첫번째 버전이 출시되기 전이었습니다. CSS 선택자로 DOM을 다루고, 웹 요소의 시각적 스타일을 조작하기 위한 어떠한 표준도 없었습니다. 그리고 다른 API들 처럼, IE가 만든 XMLHttpRequest interface는 브라우저 호환이 되지 않았습니다.

jQuery는 DOM조작과 애니메이션, 'Ajax'요청을 쉽게 만들었고 하나의 브라우저를 대상으로 구현해도 대부분의 다른 브라우저에서 작동했습니다. 이러한 장점들은 당시 작은 규모였던 Github 개발팀이 빠르게 프로토타입을 만들고, 브라우저마다 최적화를 시키지 않고도 새로운 기능을 추가할 수 있도록 해주었습니다.

우리는 언제나 이렇게 유용하고 중요한 라이브러리를 만들고 관리한 존 레식(jQuery 창시자)과 jQuery 개발자들에게 감사할 것입니다.


**- 최근의 웹 표준**  
수년간 Github은 크게 성장했고 점차 자바스크립트 규모와 퀄리티에 대한 책임감을 가지게 되었습니다. 우리가 꾸준히 신경쓰고 있는 것중 하나는 '기술적 빚(technical debt)' 입니다. 의존성 라이브러리인 jQuery의 가치가 떨어질수록 프로젝트의 기술적 빚의 크기는 커집니다.

우리는 jQuery와 모던 웹 브라우저에서 급속하게 진화하는 웹 표준을 비교했고, 다음을 깨달았습니다.

  - $ 선택자는 querySelectorAll()로 쉽게 대체할 수 있다.  
  - CSS 클래스 변경은 Element.classList를 통해 가능하다.  
  - JS보다 CSS로 구현하는 애니메이션이 발전하고 있다.  
  - ajax는 [Fetch 표준](https://fetch.spec.whatwg.org)으로 구현할 수 있다.  
  - JS의 addEventListener는 충분히 안정화되었다.  
  - [Event delegation pattern](https://github.com/dgraham/delegated-events#readme)은 가벼운 라이브러리로 쉽게 캡슐화할 수 있다.  
  - jQuery의 syntactic sugar는 JS의 발전으로 메리트가 없어졌다.  

더욱이, chaining 문법은 우리가 원하는 코딩 방식과 맞지 않았습니다.  
예를 들어  
```javascript
$('.js-widget')  
  .addClass('is-loading')  
  .show()  
```  
위 코드는 쓰기 쉽지만, 우리 기준에서 그렇게 좋은 커뮤니케이션 방식이 아닙니다.  
저자가 하나 이상의 js-widget 요소를 사용할 생각이라면?  
혹은 페이지의 마크업을 업데이트하면서 우연히 js-widget의 클래스를 빼버린다면  
브라우저는 우리에게 뭔가가 잘못되었다고 알려줄까요?  

기본적으로 jQuery는 첫번째 선택자가 DOM의 어떠한 것과도 매칭되지 않으면 조용히 위 코드를 스킵해버립니다.
우리에게 이런 동작은 특징이라기 보다는 버그에 가까웠습니다.

마침내 우리는 빌드할 때 static type 체킹을 위해 [Flow](https://flow.org/)를 사용하기 시작했고
jQuery의 chaining 문법이 static 분석에 그리 좋지 못하다는 결론을 내렸습니다. 거의 모든 jQuery 메소드의 결과가 같은 타입이었기 때문입니다. 우리는 Flow를 단순 대안 이상으로 선택했습니다. 당시에 ```@flow weak``` 모드와 같은 특징이 untyped된 코드베이스에 타입 개념을 점진적이고, 효율적으로 적용시킬 수 있도록 해주었기 때문입니다.

대체로 jQeury와의 이별은 웹표준에 대한 의존도를 높이고, MDN 웹 문서를 우리의 프론트엔드 개발자의 기본 문서로 하고, 미래에 더 탄력적인 코드를 유지하며, 결국 30kB 크기의 의존성 라이브러리를 제거하여 페이지 로드 시간과 JS실행 시간을 단축시킵니다.


**- Slow & Steady**  
우리는 jQuery를 vanilla JS로 대체하는 것에 모든 리소스를 투입하는 대신 조금씩 목표를 향해 나아가기로 했습니다.  

  - jQuery API를 사용하는 비율을 측정할 수 있도록 설정하여 사용량이 늘어나지 않도록 꾸준히 모니터링 했습니다.  
  - 새로운 코드에 jQuery가 사용되는것을 막기 위해 [ESlint-plugin-jquery](https://github.com/dgraham/eslint-plugin-jquery#readme)를 만들었습니다. (코드단에서 jQuery 사용을 시도할 경우 경고 혹은 커밋 금지)  
  - eslint 규칙을 지키지 않는 기존 코드에 대해서는 eslint-disable 규칙을 정하여 주석으로 구분하도록 했습니다.  
  - eslint-disable 규칙을 모르는 개발자를 위해 eslint-disable 규칙이 포함된 pull request에 자동으로 리뷰를 남기는 pull request 봇(bot)을 만들었습니다.  
  - 몇몇 플러그인의 경우 vanilla JS로 대체하는 동안 인터페이스를 동일하게 유지했습니다.  
  - 가능한 빨리 이전 버전의 Internet explorer에 대한 지원을 중단했습니다.  


여기까지 입니다.  
중요한 내용은 대부분 담았지만 불필요하다고 생각되거나 이해가 안가는 부분은 생략했습니다.  
전체 내용을 알고 싶으신 분은 꼭 원본을 확인해주세요.  

다양한 JS 프레임워크가 등장하면서 jQuery를 걷어내야 한다, 혼용하면 안좋다 하는 말을 종종 들어왔지만  
왜 그래야 하는지는 몰랐는데 이번 기회에 자세하게 알 수 있었습니다.  

그럼에도 불구하고 큰 프로젝트가 아니라면, Vanilla JS + jQuery 조합은 아직 매력적이라고 느껴지네요.  
JS의 역사에서 jQuery가 가지는 영향력이 참 큰 것 같습니다.
