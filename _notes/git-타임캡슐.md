---
title: git-타임캡슐
date: 2022-08-08
tags: git github coding yalco
---

# Git-타임캡슐?

---
### git add & commit
- git 상태확인: `git status`

- git 타임캡슐(staging area)에 staging 하기: `git add "file_name"` or `git add .`

- git 타임캡슐의 스냅샷 찍기:
	- `git commit` : `vi` 가 열리면서 comment 적을 수 있음
	- `git commit -m "your_comment"`: 터미널에서 한줄 comment 달기

- git 스냅샷 히스토리:
	- `git log`

```zsh
#output
# "j": 아래로 스크롤
# "k": 위로 스크롤
# ":q": 나가기
commit a6bddd1d806b4db51f4568419296ac394f9665ab (HEAD -> main)
Author: neuralprobe <jhshin1026@gmail.com>
Date:   Tue Aug 9 07:04:01 2022 +0900

	First commit
```

---
### 코드 변경 example

```zsh
# lions.yaml 지우고,
# tigers.yaml 수정하고,
# leopard.yaml 생성하면,
git status

# output
On branch main
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    lions.yaml
        modified:   tigers.yaml

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        leopard.yaml
```

- 변경 내역 비교해 보기!

```zsh
git diff
# "j": 아래로 스크롤
# "k": 위로 스크롤
# ":q": 나가기

#output
diff --git a/lions.yaml b/lions.yaml
deleted file mode 100644
index 416a903..0000000
--- a/lions.yaml
+++ /dev/null
@@ -1,8 +0,0 @@
-team: Lions
-
-manager: Mary
-
-members:
-- Thomas
-- Karen
-- Margaret
\ No newline at end of file
diff --git a/tigers.yaml b/tigers.yaml
index ebd5d76..480f6dc 100644
--- a/tigers.yaml
+++ b/tigers.yaml
@@ -1,6 +1,6 @@
 team: Tigers
 
-manager: John
+manager: Donald
 
 members:
 - Linda
~
~
(END)
```

- 추가된 파일 (untracked file) 없을 경우엔, `add` 와 `commit` 한꺼번에 할 수 있음

```zsh
git commit -am "your_comment"
```

---
#### Reference
- [Yalco's github lecture 2-1](https://www.yalco.kr/@git-github/2-1/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

#### Extra Internal-Links
- 이전강의: [[git-gitignore]]
- 다음강의: [[git-과거로돌아가기]]
