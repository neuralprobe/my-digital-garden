---
title: git) 과거로돌아가기
date: 2022-08-14
tags: git gitHub coding yalco
---

# 과거로 돌아가는 2가지 방법

---

### Reset vs Revert:
- **Reset:**
	- 과거 특정 시점으로 돌아가면서, 현재와 복귀시점 사이의 히스토리를 지우는 방법.

```zsh
git log

# 복귀하려는 commit의 hash 복사

git reset --hard {commit hash}

```

- **Revert:**
	- 과거 특정 시점으로부터 현재까지 코드의 변화를 '반대로' 수행하는 방법. 
	- 히스토리가 보존됨.
	- 특별히 .... 특정 시점의 commit 만 (그 전과 그 후에 한 변화는 그대로 유지하면서!!) 취소하고 싶을 때 유용함.
	- 여러 사람이 같이 일할 때 꼭 필요!

```zsh
git log

# 복귀하려는 commit의 hash 복사

git revert {commit hash}

# 출력되는 commit message 에서 :wq 눌러서 자장하고 나오기

# (Case1)
# revert 하려는 커밋(파일a.yaml 생성)이 그 이후의 커밋(파일a.yaml에 코드 추가)에 영향을 줄 경우
git revert {commit hash}
git rm a.yaml
git revert --continue

# (Case2)
# revert 뿐 아니라 다른 변화도 더 주고나서 커밋하고 싶으면,
git revert --no-commit {commit hash}


```


---
**Reference**
- [Yalco's github lecture 2-2](https://www.yalco.kr/@git-github/2-2/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

**Related Internal-Links**
- 이전강의: [[git) 타임캡슐]]
- 다음강의: [[git) 브랜치 관리]]
