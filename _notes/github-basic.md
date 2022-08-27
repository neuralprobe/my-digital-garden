---
title: github-basic
date: 2022-08-16
tags: git github coding yalco
---

## Github 시작하기

---

- 게정 등록하고 로그인!
- Personal access token 만들기
	- `Settings` $\rightarrow$  `Developer Settings`  $\rightarrow$ `Personal access tokens` $\rightarrow$ `Generate new token`
	- [Updating credentials from the macOS Keychain](https://docs.github.com/en/get-started/getting-started-with-git/updating-credentials-from-the-macos-keychain)
		-   `Keychain Access` 앱 실행
		-   `github`의 `인터넷 암호` 항목 선택
		-   사용자명(`계정` 칸)과 토큰(`암호 보기` 누른 뒤 오른쪽 칸) 붙여넣기
- 새로운 repository 만들기, 협업 멤버 초대하기 등등...

---

## Github 원격 repository 사용하기

```zsh
# origin: 원격 저장소 이름. 다른걸로 바꿔도 된다.
git remote add origin {원격 저장소 주소}

# 브랜치 이름을 main으로 바꾸기
git branch -M main

# origin 이란 이름의 원격 저장소의 main 브랜치로 푸쉬하고, 
# git push 만 해도 같은 설정으로 푸쉬하도록 설정.
git push -u origin main

# remote 목록
git remote

# remote 지우기
git remote remove {원격 저장소 이름}

# git 저장소 다운 받기
git clone {원격 저장소 주소}
```

---

## Push and Pull

```zsh
# local => remote
git push

# remote => local
git pull
```

- remote와 local의 코드가 둘 다 바뀌었을 때?

```zsh
# 양 쪽 다 바뀌었을 땐 push 안됨
git push

# merge 방식으로 합치기
git pull --no-rebase

# rebase 방식으로 합치기
git pull --rebase

# 그 다음에
git push
```

- local의 내용 강제로 push 하기

```zsh
git push --force
```

---

## 원격 브랜치 관리
```zsh
# 로컬 브랜치만 보기
git branch

# 로컬, 원격 브랜치 모두 보기
git branch --all
```

```zsh
# 로컬에서 브랜치 만들어서 원격에 올리고 싶을 때,
git push -u origin from-local

# 반대로 원격에서 브랜치 만들어서 로컬로 가져올 때는,
# 일단 확인
git fetch
# 원격 브랜치 가져오고, 새 브랜치로 스위치 
git switch -t origin/from-remote

# 원격 브랜치 지우고 싶으면,
git push (원격 이름) --delete (원격의 브랜치명)
```




---
#### Reference
- [Yalco's github lecture 4-1](https://www.yalco.kr/@git-github/4-1/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

#### Extra Internal-Links
- 이전강의: [[git-브랜치관리]]
