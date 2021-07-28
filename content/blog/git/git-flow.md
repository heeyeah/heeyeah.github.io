---
title: "git flow"
date: '2021-07-27 12:00:00'
category: git
draft: true
---

### git flow 테스트 시나리오

```
main
  \_test
    \_develop
      \_feat1
      \_feat2
```

1. feat1 from develop 생성
2. feat2 from develop 생성
3. feat1 to develop MR
4. feat3 from develop 생성
5. from develop to test MR
6. tagging v1.0.0 from test (원래는 main에서 따야 함! #7에서)
7. from test to main MR

---
8. hotfix from main 생성
9. hotfix to main MR
10. tagging v1.0.0-hotfix from main
