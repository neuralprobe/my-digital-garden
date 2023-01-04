---
title: git) 브랜치 관리
date: 2022-08-16
tags: git gitHub coding yalco
---

# Git 브랜치 관리

---

## 브랜치 만들기

```zsh
# creat branch
git branch {branch name}

# list branch
git branch

# switch to other branch
git switch {branch name}
```

```zsh
# branch 만들고 바로 switch 하기
git switch -c {branch name}
```

```zsh
# branch 지우기
git branch -d {branch name}

# branch 이름 바꾸기 
git branch -m {old branch name} {new branch name}
```

- 브랜치 내역 보기: 소스트리에서 보는게 더 좋음.

```zsh
# 현재 brnach 내역 보기
git log

# 모든 brnach 내역 보기
git log --all --decorate --oneline --graph
```

---

## 브랜치 합치기

- Merge: 두 브랜치의 변화 내용을 모두 반영해서 한 브랜치로 합치기
	- Branch history를 그대로 남길 수 있음
	- Reset 할 수 있음

```zsh
# 목표: add-coach 브랜치를 main 브랜치에 합치기

git switch main

git merge add-coach

# vim 화면 나오면 ":wq" 로 저장하고 나오기 

```

- Rebase: 한 브랜치의 변화 내용을 대상 브랜치로 옮겨 붙이기
	- Branch history를 깔끔하게 관리할 수 있음

```zsh
# 목표: add-coach 브랜치를 main 브랜치에 갖다 붙이기

git switch add-coach

git rebase main

# 여기까지 하면 add-coach 브랜치가 main 브랜치에 달라붙고,
# main 브랜치는 변화가 없다.
# main 브랜치에 반영하는게 목표일 경우, 아래를 추가 실행!

git switch main

git merge add-coach 

# vim 화면 나오면 ":wq" 로 저장하고 나오기 

```


- 협업할 때, 팀원들간에 공유된 commit은 Rebase 안쓰는게 좋음.

---

## 충돌 해결하기

- Merge 충돌
	- merge 할 때, 두 브랜치가 충돌될 경우, 오류 메시지 확인하고, 브랜치 충돌되는 부분을 편집하고 `git add .` +  `git commit -m "xxx"`
	- merge 멈추고 싶으면 `git merge --abort`
- Rebase 충돌
	- rebase 할 때, 두 브랜치가 충돌될 경우, 오류 메시지 확인하고, 브랜치 충돌되는 부분을 편집하고 `git add .` +  `git rebase --continue`
	- rebase 멈추고 싶으면 `git rebase --abort`


---
<a href="https://www.freepik.com/free-vector/set-flexible-people-various-positions_4530344.htm#query=stretching&position=2&from_view=keyword">Image by katemangostar</a> on Freepik
- [Yalco's github lecture 3-1](https://www.yalco.kr/@git-github/3-1/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

**Extra Internal-Links**
- 이전강의: [[git) 과거로돌아가기]]
- 다음강의: [[git) github basic]]
