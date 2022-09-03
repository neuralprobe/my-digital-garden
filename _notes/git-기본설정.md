---
title: git-기본설정
date: 2022-08-07
tags: git github coding yalco
---

# Git 설정 & 프로젝트 관리 시작하기

---
### Tip: CLI vs GUI?
- CLI: Command line interface
- GUI: Graphic user interface with SourceTree

---
### Git 최초 설정
- 내 깃 정보를 특정하기. github account 는 별개. 
```zsh
git config --global user.name "your name"
git config --global user.email "your email"
```

- 확인하기 위해서는 개인 정보 빼고 치기.
```zsh
git config --global user.name
git config --global user.email
```

- 기본 브랜치명 정하기.
```zsh
git config --global init.defaultBranch main
```

---
### 프로젝트 생성!  (CLI 버전)
- `Codes/git_practice`  폴더 만들어서 여기 git 프로젝트를 생성해보자.
```zsh
# git initialization: .git folder 생성
git init
# Do you want to change the initial branch to 'main'?
git branch -m main
# Code (lions.yaml, tigers.yaml) 생성/변경 한 다음에,
git status
# 출력:
	On branch main

	No commits yet

	Untracked files:
	  (use "git add <file>..." to include in what will be committed)
	        iterm_theme/
	        lions.yaml
	        tigers.yaml
	
	nothing added to commit but untracked files present \
	(use "git add" to track)
```

- 예시 코드:

```yaml
#tigers.yaml
team: Tigers
manager: John
members:
- Linda
- William
- David

#lions.yaml
team: Lions
manager: Mary
members:
- Thomas
- Karen
- Margaret
```

---
### GUI 버전으로 보기
- `Codes/git_practice`  폴더를  `SourceTree`로 끌어서 구경하기. 

![[얄코깃헙-git설정-00.png]]
- `SourceTree`의 repository 삭제할 때는, 꼭 북마크 지우기를 선택하자. (조심조심...)



---
#### Reference
- [Yalco's github lecture 1-4](https://www.yalco.kr/@git-github/1-4/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

#### Extra Internal-Links
- 이전강의: [[git-맥셋팅]]
- 다음강의: [[git-gitignore]]
