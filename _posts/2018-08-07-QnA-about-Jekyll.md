---
layout: media
title: "QnA about Jekyll"
comments: true
category: [ tech, ETC ]
image:
  teaser: post_thumbnail/jekyll_logo.png
---

Jekyll 초심자가 Jekyll 문외한에게

요즘 개인 블로그 만들기는 참 쉽습니다.  
대형 포털사이트나 구글에서 제공하는 블로그 서비스부터 티스토리, 미디엄같은 블로그 전문 플랫폼까지
선택의 폭도 참 넓죠. 이 글에서는 블로그를 만드는 방법 중 하나인 **Jekyll**에 대해 설명합니다.
구체적인 구현방법은 똑똑하신 분들이 이미 친절하게 설명해 놓으셨기 때문에
Jekyll을 처음 사용할 때 제가 느꼈던 궁금증들을 QnA로 구성하고 관련 설명을 담으려고 해요.

**Q. 그래서 정체가?**  
A. Jekyll이란, 블로그를 만들 수 있는 많은 방법 중 하나입니다.

**Q. 보통 블로그 서비스와 뭐가 달라요?**  
A. Jekyll은 단순 글 작성 뿐만 아니라 강력한 커스터마이징 환경을 제공합니다. 정해진 틀 안에서 글만 쓰는 방식이 아니라 본인이 직접 코드 수준에서 블로그를 만들어나가는 방식이기 때문이죠. 또한 보통 블로그 서비스와 차별화되는 몇 가지 장점도 가지고 있습니다.

**Q. 어떤 장점이요?**  
A.  
- 마크다운 문법을 사용할 수 있습니다.  
- Github를 통해 매우 쉽게 배포할 수 있습니다.  
- 기본 제공되는 다양한 디자인 틀을 사용하여 쉽게 시작할 수 있습니다.  
(물론 네이버 블로그보다는 어렵습니다)

**Q. 그래서 어떻게 써요?**  
A. Jekyll 블로그를 만들기 위해서는 시간을 내서 따로 사용법을 배우셔야 해요. 일반적으로 생각하시는 것처럼 '글쓰기' 버튼을 눌러서 글과 이미지를 입력하고, '확인'을 누르면 업로드되는 시스템이 아니거든요. Jekyll에서 가이드하는대로 폴더와 파일을 직접 생성해야하고, 포맷과 문법도 익혀야 합니다. 귀찮죠. 솔직히 시간적 여유가 없거나, 고도의 커스터마이징이 필요하지 않거나, 개발자가 아닌 분들은 다른 수단을 이용하시는게 낫다고 생각해요.

**Q. 저는 개발자고, 꼭 써야겠어요**  
A. 네, 개발자라면 좋은 선택입니다. 가장 쉽고 빠른 방법을 알려드릴게요.  
[여기](http://themes.jekyllrc.org/)로 가시면 Jekyll에서 제공하는 다양한 블로그 템플릿을 구경할 수 있습니다.

![My helpful screenshot]({{ "/images/1_qna_about_jekyll/template_example.png" }})
- 마음에 드는 것 하나를 고르시고, Homepage로 가세요.


![My helpful screenshot]({{ "/images/1_qna_about_jekyll/screenshot_fork.png" }})
- Fork를 통해 자신의 Github계정으로 해당 템플릿의 소스를 가져옵니다.


![My helpful screenshot]({{ "/images/1_qna_about_jekyll/screenshot_forked.png" }})
- 생성된 Repository의 설정에서 Repository name을 **내 Github 계정명.github.io**로 바꿔주세요.  
저의 경우 examplar -> **balfouri1210.github.io**가 되겠네요.

5 - 10분 후 **https://내 Github 계정명.github.io** URL로 접속하면, 선택한 블로그 템플릿이 배포되어 있는 것을 확인할 수 있습니다. Jekyll블로그를 위한 기본 구조 생성과 배포까지 클릭 몇번으로 완성된거죠. Jekyll 사용법을 익히지 않은 사람이라도 이 단계까지는 매우 쉽게 달성할 수 있습니다.

**Q. 꼭 Github 기반으로 배포해야 하나요?**  
A. 아닙니다. Jekyll을 설치하여 프로젝트 구성 후 본인이 직접 만든 웹서버에 올릴 수도 있고,  
Netlify, Aerobatic 등을 이용할 수도 있죠.  
하지만 공식문서에도 안내되어 있듯이 Github pages를 이용하는 것이 **훨씬** 간편하고 빠릅니다.  
[Jekyll 블로그를 배포하는 방법들](http://jekyllrb-ko.github.io/docs/deployment-methods/)과  
[Github pages로 Jekyll블로그 배포하기](http://jmcglone.com/guides/github-pages/)를 참조하세요.

**Q. 이 다음은요?**  
A. 안타깝게도 이제부터는 공부를 해야 합니다. 페이지는 어떻게 구성하는지, 포스트는 어떻게 쓰는지,  
작성한 포스트를 어떻게 분류하여 각 페이지에 넣을지 말이죠.  
다행히도 엄청난 응용이 필요하거나, 머리를 많이 써야 하는 타입은 아닙니다.  
정해져 있는 틀이 있고, 그 틀을 익혀 잘 닦여진 도로를 따라가기만 하면 되거든요.  

이 포스트에서 자세한 방법을 따로 설명하지는 않지만,  
[Jekyll 공식 한글 문서](http://jekyllrb-ko.github.io/docs/home/)
라는 아주 좋은 학교가 있습니다.  
저도 대부분의 사용법을 이곳에서 익혔고 제작 과정을 카테고리별로 분류하여 친절한 설명을 제공합니다.  
제로베이스에서 Jekyll에 관한 개념을 익히고, 이것저것 테스트 해보면서 첫 테스트 포스트를 작성하기까지 만 하루정도 걸린 것 같네요. (이해력이 빠른 편은 아닙니다)  
공식 문서를 바탕으로 작업하고, 막히는 부분만 구글링하는 방식으로 도전해보세요. 인기있는 툴이기 때문에 이미 많은 자료가 준비되어 있습니다.

넉넉하게 주말 이틀정도 투자하시면, 자신만의 Jekyll블로그를 만들 수 있을 것이라고 확신합니다.  
감사합니다.
