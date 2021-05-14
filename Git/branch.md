# Branch
깃에서 커밋을 하게 되면,
1. 깃은 루트 디렉터리를 트리 객체만든다.
2. 루트 디렉터리의 하위 디렉터리들의 트리 객체를 만들고 연결한다.
3. 커밋 객체를 만들고, 해당 객체에 루트 트리 객체에 대한 포인터를 저장하고, 메타 데이터에 대한 객체를 만들어서 저장한다.

즉, 커밋 객체에는 해당 커밋에 대한 메타 데이터(author, commiter)와 루트 디렉터리 트리 객체를 가리키는 포인터를 갖는다.


## Cheat Sheet
### branch 목록 보기

`git branch`


### branch 생성과 동시에 checkout

`git checkout -b branchname`

### git branch 삭제

`git branch -d branchname`


## 브랜치?
브랜치는 `커밋`에 대한 포인터라고 할 수 있다.

만약 해당 브랜치에서 또 다른 브랜치를 생성한다고 가정했을때, 해당 브랜치에서 unstaged, staged 된 상태의 파일들과는 관련이 없다.

작업 중인 상태에서, modified, untracked 등의 파일이 있을때,
`git checkout -b anotherbranche` 를 하고

`git add . `
`git commit -m "another branch commit"` 을 진행하게 되면 해당 파일은 another 브랜치에 커밋됨과 동시에, 기존에 브랜치에서는 사라진다.



## Merge

### fast-forward merge
만약 운영 환경에서 문제가 생겨서 핫픽스가 필요해서, "hotfix" 브랜치를 만들어서 수정을 했다.


[image:71AC0753-B26B-4F96-A3E8-5E23590819BE-942-0000C664912F691E/스크린샷 2021-02-08 오후 9.13.33.png]

마스터 브랜치에서 `git merge hot fix` 를 하면  `fast-forword`라고 한다.
이 경우에는 `hotfix` 브랜치가 단지, master 브랜치의 upstream에 있기 때문에, `fast-forward merge` 라고 부른다.

[image:C83A65DB-FB0E-413C-A2E2-A7482AC75894-942-0000C68B38B2058A/스크린샷 2021-02-08 오후 9.16.20.png]


###  3 way merge

만약 merge의 대상이 되는 branch가 hot fix 브랜치와 달리 master branch의 upstream에 있는 것이 아니라면, `3-way Merge`를 진행하게 된다.
[image:8DE224F0-E337-4A16-A20D-D0188E35CCAE-942-0000C715B7420343/스크린샷 2021-02-08 오후 9.26.16.png]


[image:81FB1824-5895-4279-8E64-F0577439F2B3-942-0000C71240D4FDB8/스크린샷 2021-02-08 오후 9.26.01.png]


3-way Merge 는 두 브랜치의 공통 조상을 찾고,  이들을 합친 다음 새로운 커밋을 만들어서 합쳐질 브랜치의 커밋을 새롭게 만들어진 커밋으로 옮겨간다.

만약 두개의 브랜치가 같은 곳을 수정했다면, 충돌이 나서 자동으로 Merge가 이루어지지 않는다.

이 경우 `git status` 명령어를 넣으면 충돌이 난 지점을
`both modified : 파일명` 으로 알려준다.  파일을 찾아가서 수정해주면 된다.

- Head : Head가 가리키는 곳. 즉, 머지를 진행하는 브랜치.
-
```javascript
<<<<<<< HEAD:index:js
const a = 1;
=========
const a = 'hello';
>>>>>> issue:index.js
```


## 리모트 브랜치

리모트 Refs : 리모트 저장소에 있는 포인터.

일반적으로는 리모트 트래킹 브랜치라는 것을 통해서 로컬에 저장을 한다. 로컬에서 리모트에 있는 저장소와 연결이 되면, 해당 브랜치에 대한 커밋을 가리키고 있게 되는 것이다.

리모트 트래킹 브랜치는 `origin/develop` 이라는 리모트 브랜치를 추적하는 `develop`브랜치이다.


리모트 브랜치의 커밋 히스토리와 로컬 브랜치의 커밋 히스토리는 독립적이다.  그렇기 때문에, 누군가 작업을 해서 remote의 브랜치에 push를 해도 로컬의 해당 브랜치는 영향을 받지 않는다.


로컬에서 master 브랜치의 (공통의 특정 커밋 지점에서)  작업을 시작해서 히스토리를  쌓는다고 가정하자.  다른 누군가가 해당 공통의 커밋 지점에서 작업을 하고 push를 한다.

누군가 push한 데이터를 `git fetch origin` 명령을 통해서  fetch를 해오면, 로컬 트래킹 브랜치(`origin/master`)는  다른 누군가가 push 한 커밋을 따라간다. 하지만 나의 로컬 `master` 브랜치는 이와는 별개로 커밋 히스토리를 생성해 나간다.


누군가 `issue` 라는 브랜치를 만들어 push했다고 가정하자. 이를 로컬로 받고 싶으면 어떻게 해야할까?

우선 `git fetch origin`을 실행한다.
위의 명령어를 실행하면  origin에 있는 데이터들을 받아오게 된다. 하지만 이 명령어를 작성하면 리모트 트래킹 브랜치 (origin/issue)라는 브랜치가 생기지만, 이를 직접 접근해서 수정하거나 하는 작업을 할 수는 없다.

따라서 `git checkout -b issue origin/issue` 명령어를 실행해서 해당 리모트 브랜치와 같은 브랜치를 로컬에 만들어 주어야 한다.












#Git