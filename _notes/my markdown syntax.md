---
title: my markdown syntax
date: 2022-08-06
tags: markdown jekyll
---

## 기본 마크다운
- 헤더: `#header1 ~ ######header6`

- 리스트: `- 문장`, `1. 문장`

- ToDo: `- [ ] 할 일` , `- [x] 한 일`
> - [ ]  할일
> - [x]  한일

- 취소선: `~~취소할문장~~` 
> ~~취소할문장~~

- 하이라이트: = =하이라이트= =
> ==하이라이트==

- 볼드체: `**볼드체**`

- 이탈릭체: `*이탈릭체*`

- 태그: `#태그`, `#상위태그/중위태그/하위태그`
> #태그예시, #상위태그예시/중위태그예시/하위태그예시

- 줄긋기: `---`~`-------`

----------

## 링크 관련 마크다운

- 백링크: \[\[백링크페이지\]\]

- 블록레퍼런스: 
	- \!\[\[백링크 페이지 전체 미리 보기\]\]
	- \!\[\[백링크 페이지 일부 미리 보기\]\]
	- \[\[백링크 페이지 특정 위치에 링크 걸기\^\]\]
	- \[\[백링크 일부 목차 열기 \# 누르고 고르기\]\]

> ![[my markdown syntax examples]]

- 요약하여 링크걸기: \[\[my markdown syntax examples|링크 페이지를 짧게 설명\]\]
	- [[my markdown syntax examples|링크 페이지를 짧게 설명]]

- 외부링크: `[외부링크이름](외부링크주소)`
> [네이버](https://naver.com)

---

## 임베딩 문법
- 이미지 파일 임베딩: 
	1. 이미지파일을 메모로 끌어 오기. 
	2. `![-[이미지 파일 위치]-]` (No Jekyll support yet)
	3. `![이미지 파일 설명](위치)` (Works in Jekyll!! 이거 쓰자.)

- 이미지 파일 Resizing:
	1. `![-[이미지 파일 위치|이미지 크기]-]` (No Jekyll support yet)
	2. `![이미지 파일 설명|이미지 크기](위치)` (No Jekyll support yet)
	3. `![이미지 파일 설명](/assets/image.jpg){: width="100" height="100"}` (Jekyll에선 되고, Obsidian에선 안됨 ㅠㅠ)
	4. `<img src="/assets/image.jpg" width="100">` (Jekyll에선 되고, Obsidian에선 안됨 ㅠㅠ. Obsidian에서 안되는건, html 경로가 절대경로로 해야 되서 그런거 같음..)


		![hope](../assets/image.jpg)

- 유튜브 임베딩:
	- youtube 영상 $\rightarrow$ share button  $\rightarrow$ embed button  $\rightarrow$ copy  $\rightarrow$ paste in obsidian note 


		<iframe width="560" height="315" src="https://www.youtube.com/embed/EdHGrnuCEo4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


---
#### Reference
- 
#### Extra Internal-Links
- 