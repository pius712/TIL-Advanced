# Git
## Config

git에서 커밋시에 사용되는 유저의 email, name에 대한 설정.
프로젝트마다 다른 이름으로 사용하고 싶다면 `--global` 옵션을 빼고 해당 프로젝트의 config를 정해주면 된다.


```bash
git config [--global] user.email [변경할 이메일]
git config [--global] user.name [변경할 이름]

git config --list // 깃 설정에 대한 리스트드
```

#Git