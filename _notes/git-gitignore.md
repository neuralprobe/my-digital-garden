---
title: git-gitignore
date: 2022-08-08
tags: git gitHub coding yalco
---

# Git-Ignore 사용법

---
### Why `.gitignore`?
- git으로 관리할 필요 없는 애들: package file 이나 result file 들
- git으로 관리하며 안되는 애들: **security** 때문에 공유하면 안되는 파일들 !!!
```YAML
#secrets.yaml
id: admin
pw: 1234abcd
```
- 이런 보안관련 파일이 있을 때, `.gitingore` 만들어서 ...
```zsh
#.gitingore
secrets.yaml
```
- `git status`를 쳐보면, `secrets.yaml` 정보가 사라짐.

---
### `.gitignore` 형식
- [https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore) 참조

```zsh
# 이렇게 #를 사용해서 주석 

# 모든 file.c 
file.c 

# 최상위 폴더의 file.c 
/file.c 

# 모든 .c 확장자 파일 
*.c 

# .c 확장자지만 무시하지 않을 파일
!not_ignore_this.c 

# logs란 이름의 파일 또는 폴더와 그 내용들 
logs 

# logs란 이름의 폴더와 그 내용들 
logs/ 

#logs 폴더 바로 안의 debug.log와 .c 파일들 
logs/debug.log 
logs/*.c 

# logs 폴더 바로 안, 또는 그 안의 다른 폴더(들) 안의 debug.log 
logs/**/debug.log
```


---
#### Reference
- [Yalco's github lecture 1-6](https://www.yalco.kr/@git-github/1-6/)
- [Yalco's gihub lecture from inflearn](https://www.inflearn.com/course/%EC%A0%9C%EB%8C%80%EB%A1%9C-%ED%8C%8C%EB%8A%94-%EA%B9%83/dashboard)

#### Extra Internal-Links
- 이전강의: [[git-기본설정]]
- 다음강의: [[git-타임캡슐]]