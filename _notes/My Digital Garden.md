---
title: My Digital Garden
date: 2022-08-29
tags: 제텔카스텐 메모 두번째뇌 자기계발 DigitalGarden index
---

# My Digital Garden

---

# Notebook-Scalability 문제

- 연구 경력 / 업무 경력 / 학습 기간이 길어지다 보면, 쌓여가는 아이디어와 지식을 어떻게 관리하는게 좋을까 고민하게 된다. 
- 특히, 요즘의 연구 분야인 deep learning accelerator는 다양한 분야와 연관이 있어 아이디어/지식을 어떻게 정리해야할지 헷갈릴 때가 많다. 
- 연관 분야를 좀 적어보자면 ==computer architecture==, ==memory system==, ==neural network==, ==machine learning==, ==linear algebra==, ==computer graphics==, ==HW-SW interface==, ==compiler==, ==deep learning frameworks (tensorflow, pytorch ...)==, ==graph processing==, ==vision application==, ==natural language processing==, ==programming languages (python, C++ ...)==, ==algorithm and data structure==, ==git==, ==SaaS==, ==object-oriented programming==, ==design patterns==, ==cloud computing==, ==software architecture==, ==IT market trend==, ==ASIC flow==, ==FPGA==, ==operating system==, ==schedule optimization==  등등이 있는데, 
- 하나의 업무나 아이디어가 여러 분야에 걸쳐지는 경우가 많아서, 컴퓨터의 파일 시스템이나 도서관의 서지 체계같이 ==계층적 자료 정리 방법== 따라 자료를 정리하다 보면, ==notebook-scalability== 문제를 겪게 된다.
- ==Notebook-scalability== 문제는, 아이디어나 지식 혹은 경험을 담은 노트를 모으다가 노트의 양이 어떤 임계점을 넘어서면, 더 이상 체계적으로 노트를 정리하기 힘들어지는 현상을 말한다. 
	- 박사과정을 시작할 때나 특정 연구를 시작할 때, 자료를 어떤 방식으로 수집하고 정리할지 체계적으로 계획하고 정리하다가,
	- 일이 바빠지거나, 자료 수집 속도가 자료 정리 속도를 넘어서 이미 자료 체계가 망가졌거나, 예상의 범주를 넘어서는 노트가 발생해 관리가 힘들어지기 시작하면,
	- 손을 놓고 노트를 작성한 시간 순에 따라 보관하는 순간이 찾아온다. 
	- 개인적으로 경험상 특정 조직에 속하고 나서 2년 정도, 특정 연구를 시작하고 3개월 정도면 이런 임계점을 경험했던 것 같다.

![[My-Digital-Garden-00.png]]

--- 

# Zettelkasten 과 Digital Garden

- Notebook-scalability로 여태 고생해온 덕에, 노트 기법이나 자료 정리 기법에 평소에도 관심을 갖고 더 좋은 방법이 있는지 찾아보곤 한다.
- Evernote, Tistory, Brunch, Github-page, Notion, MS-Word, iOS-note, MS-oneNote, facebook, instagram 등등 여러 방법을 활용해 문서를 정리하거나 글을 작성해 봤지만, 여태 마음에 드는 자료의 수집/정리/공유를 위한 딱 마음에 드는 플랫폼을 여태 찾지 못했다.
- 그러다가 지난 달, [작가의 방](https://www.youtube.com/c/%EC%9E%91%EA%B0%80%EC%9D%98%EB%B0%A9) 유튜브 채널의 [옵시디언 기초 강좌](https://youtube.com/playlist?list=PLy4SLsxzyLUUJlu0L-_U7c1jy_bqvPMR6) 플레이리스트를 발견하고, 영상 속에서 소개한 ==제텔카스텐== (Zettelkasten, 메모상자의 독일어) 에 관심을 갖게 되었고, [[제텔카스텐-글쓰는 인간을 위한 두번째 뇌]] 라는 책도 접하고, 이제는 실행 단계에 접어들게 되었다. 

![[제텔카스텐-글쓰는-인간을-위한-두번째-뇌-01.jpeg]]
<center>Niklas Luhmann's index cards</center>

- 실행 단계에서 한 가지 해결해야 할 문제가 있었는데, 로컬 노트 플랫폼인 ==옵시디언==은 좋은 점이 너무도 많지만, 로컬 플랫폼인 만큼 글을 웹에 공유하는 솔루션은 제공하지 않아서, 옵시디언의 노트를 웹에 효율적으로 공유할 수 있는 방법을 찾아야 했다. 
- 그렇게 찾게 된 또 다른 키워드, ==Digital Garden==을 접하게 되었다.
- 이는 웹에 자신만의 지식 체계를 지속적으로 업로드하여 자신의 디저털화 된 ==두번째 뇌==를 타인들과 공유하고 소통하며 이를 지속적으로 발전시켜 나가는 것을 모토로 하는 일종의 지식 관리 운동이다. 
	- 각 개인이 자신의 ==digital garden==을 운영하고, 이것이 개인과 커뮤니티의 지적 발전으로 이어지도록 하는 것이 그 목표라고 할 수 있는데, 개인적으로 일종의 ==distributed wikipedia movement==라고 볼 수 있겠다.
- 자신의 ==Digital garden==을 관리하기 위해선 아래 세 가지 요소들이 필요하다.
	1.  문서간 상호참조를 지원하는 마크다운 노트 앱 .
	2. 노트 앱을 정적 웹사이트로 변환시켜 줄 수 있는 툴
	3. 정적 웹사이트를 배포해줄 수 있는 서버
- 나는 기존에 github-page를 써서 익숙해진 ==Jekyll==을 정적 웹사이트 변환 툴로 쓰기로 정했고,  결론적으로 ==Obsidian== $\rightarrow$ ==Jekyll==  $\rightarrow$ ==Netlify== workflow를 구현하기로 정했다.

![[My-Digital-Garden-02.png]]
<center>Obsidian 사용 예시. 문서간 상호참조의 Graph representation을 지원한다</center>

- 감사하게도, [Max's digital garden template using Jekyll](https://maximevaillancourt.com/blog/setting-up-your-own-digital-garden-with-jekyll) 페이지와  [Mike's Obsidian Jekyll Workflow](https://refinedmind.co/obsidian-jekyll-workflow) 페이지에서 정확히 ==Obsidian== $\rightarrow$ ==Jekyll==  $\rightarrow$ ==Netlify== workflow를 구현하는 방법을 자세히 소개해 두어서 이를 바탕으로 나만의 ==digital garden==을 만들 수 있었다.
- 이에 더해 몇 가지 기능과, 몇 가지 노력을 더했다.
	1. Obsidian과 Jekyll을 잘 이어붙이기 위해서 ==asset== 폴더의 웹 주소를 조정했고, 
	2. 정적 웹사이트에 태그 페이지나, timeline 페이지를 더해 잊혀진 노트가 없도록 인덱싱 해주었고,
	3. Jekyll에서 사용할 ==Mathjax== 를  [Jekyll Github 블로그에 MathJax로 수학식 표시하기](https://mkkim85.github.io/blog-apply-mathjax-to-jekyll-and-github-pages/) 를 보며 연습을 좀 했고,
	4. [웹 헤엄치기](https://wikidocs.net/book/4884?fbclid=IwAR0jxTPZ2ccqREK4mFCH1TafECwFJeq21tWY1cNeWSearV0NFkXXIKZaBzo) 를 보며 ==html==, ==css==, ==Jekyll==의 기초 개념을 공부해서 웹사이트의 미관을 조금 손 보았고,
	5. 노트의 안정적인 백업을 위해 ==iCloud==에 연동시켰고,
	6. ==fleeting note 는 아이폰으로!, permanent note는 맥북으로!== 라는 물리적 workflow도 정립을 했고,
	7. [블로그 사이트](https://jonghoon.blog/)도 간만에 정리해서, 제텔카스텐 워크플로우를 따라 글을 작성하면 올릴 수 있도록 준비를 해두었고,
	8. [netlify](https://www.netlify.com/)에 가서, ==digital garden==을 위한 전용 서브도메인을 등록했고,
		- Main-domain: [jonghoon.blog](https://jonghoon.blog), 
		- Sub-domain: [digital-garden.jonghoon.blog](https://digital-garden.jonghoon.blog)
	9. 댓글과 좋아요를 달 수 있는 Disqus add-on을 추가했고,
	10. git-ignore 를 이용해 보안이 필요한 메모는 netlify upload에서 제외시키고,
	11. 만나는 사람마다 이런게 있다고 수다를 떨면서 내 생각을 가다듬었고, 
		- <span style="color:grey">크게 관심없는 친구한테도 괜한 수다를 떨어 죄송 ... </span>
	12. [[제텔카스텐-글쓰는 인간을 위한 두번째 뇌]] 책을 2독 하면서 ==노트를 기반으로 한 사고 방식==에 익숙해 지기 위해 정신무장을 했고,
	13. 여러 노트 방식을 사용한 후기를 보며, 최적의 방법을 찾으려 탐색 중..

---

# 왜 하필 Digital "Garden"인가?
- 정원은 정원사가 (거의) 매일 들러 돌봐주어야 한다. 나무와 풀에 물을 줘야하고, 관상용 식물의 나뭇잎이나 가지를 쳐줘야 하고, 가끔은 힘을 잔뜩 주어 새로운 길을 닦고 확장 공사를 해야할 수도 있다.
- ==제2의 두뇌== 역할을 하게 될 노트 시스템도 Gardening과 마찬가지로 하루 하루 부단히 돌봐주어야 하는 녀석이다. (아래에 설명한 제텔카스텐 워크플로우는 [[제텔카스텐-글쓰는 인간을 위한 두번째 뇌|여기]] 참고)
	1. 새로운 임시노트를 매일 만들고, 
	2. 혹은 새로운 임시노트 혹은 서지노트를 만들기 위해, 부단히 텍스트를 읽어나가야 하고,
	3. 지저분한 임시노트를 영구노트로 변환해 미래에 재사용가능한 형태로 완성시켜야 하고, 
	4. 그렇게 만들어진 영구노트들간의 상호 연계성을 촘촘하게 만들어 가야하고,
	5. 태그도 잘 관리해서 상호링크로 부족한, 지식의 계층적 체계를 유지해 줘야하고,
	6. 여러 노트들의 링크를 한데 모은 인덱스 노트도 가끔 만들어 줘서, 각 노트의 접근성을 확보해야하고,
	7. 그렇게 모은 노트들이 하나의 스토리로 이어질 수 있도록, 잘 관찰해 줘야 하며,
	8. 그렇게 ==발생==한 주제들을 엮어 하나의 완성된 ==초고==로 엮어야 한다. 
- 어떻게 보면 번거로울 수 있는 일이지만, 습관화해서 몸에 베이게 만들면 못 할 일은 아닌 것 같다. 오히려, 이렇게 평소에 노트를 정리해두지 않고 Deadline이 닥쳐서야 글감을 모으고 글을 쓰는 고통에 비하면, 미미한 투자라고 할 수 있을 것 같다.

![[My-Digital-Garden-01.png]]
<p align="right"> MS TECH | WIKIMEDIA, PIXABAY</p>

---

# 결론?

- 시간이 지나 풍성해진 digital garden을 보며 새로운 생각들과 통찰을 얻을 미래를 생각하면 하루 한 두개의 메모 쯤 거뜬히 적어낼 수 있지 않을까.
- 오랜 세월 고통 받아온 Notebook-scalability 문제를 이번엔 해결할 수 있을 것 같아 성실히 gardening 해보려고 한다. 화이팅.
- 1년 정도 지나서 성공후기를 남길 수 있을면 좋을듯 :)

---

# Reference

- [**제텔카스텐** *글 쓰는 인간을 위한 두 번째 뇌*](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788993784701)
- [**하우 투 제텔카스텐(How to Zettel-Kasten)** 옵시디언 기반 두 번째 뇌 만들기](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788993784725&orderClick=LAG&Kc=)
- [옵시디언 기초 강좌](https://youtube.com/playlist?list=PLy4SLsxzyLUUJlu0L-_U7c1jy_bqvPMR6) 플레이리스트 by [작가의 방](https://www.youtube.com/c/%EC%9E%91%EA%B0%80%EC%9D%98%EB%B0%A9)님 채널
- [Max's digital garden template using Jekyll](https://maximevaillancourt.com/blog/setting-up-your-own-digital-garden-with-jekyll) 
- [Mike's Obsidian Jekyll Workflow](https://refinedmind.co/obsidian-jekyll-workflow) 
- [[제텔카스텐-글쓰는 인간을 위한 두번째 뇌]]
- [[Git Tutorial]]
- [[My Markdown Syntax]]
