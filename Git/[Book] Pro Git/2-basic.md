# 2. Git의 기초

🖍 2장의 목표

- 저장소를 만들고 설정하는 방법
- 파일을 추적(Track)하거나 추적을 그만두는 방법
- 변경 내용을 Stage하고 커밋하는 방법
- 파일, 파일패턴을 무시하도록 Git을 설정하는 방법
- 실수를 쉽고 빠르게 만회하는 방법
- 프로젝트 히스토리를 조회하고 커밋을 비교하는 방법
- 리모트 저장소에 Push하고 Pull하는 방법


## [2.1 Git 저장소 만들기](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC%EB%9E%80%3F)

다음 두가지 중 하나의 방법으로 Git 저장소를 쓰기 시작한다.

**`$ git init`** 
- 아직 버전관리를 하지 않는 로컬 디렉토리로 이동 후 위 명령어를 실행하면 새로운 Git 저장소가 만들어짐.
- 이 방법을 사용하면 `.git`이라는 하위 디렉토리가 만들어진다.   
🤭 만약 `.git` 디렉토리가 보이지 않는다면 위 명령어를 실행한 디렉토리를 Finder로 열고 `⌘ + Shift + .`을 이용해 숨겨진 파일 보기를 설정한다.
- 아래와 같은 폴더와 파일을 확인할 수 있다. 해당 폴더 및 파일들에 대한 설명은 [Git의 내부](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EB%82%B4%EB%B6%80-Plumbing-%EB%AA%85%EB%A0%B9%EA%B3%BC-Porcelain-%EB%AA%85%EB%A0%B9#ch10-git-internals)에서 확인할 수 있다.   
![git initial skeleton](./images/2-basic-skeleton.png)
- 이 명령으로는 아직 어떤 파일도 관리하지 않은 상태이다. git add와 commit 명령어를 이용해 파일을 Staging area에 올리고 스냅샷을 커밋할 수 있다.

**`$ git clone <url>`**
- 다른 어딘가로부터 Git 저장소를 복제하는 방법이다.
- 프로젝트의 히스토리를 모두 가져온 후 자동으로 가장 최신버전을 checkout해둔다.
- 만약 Alamofire라는 디렉토리를 새롭게 만들고 그 안에 Alamofire의 소스코드를 clone하고 싶다면 아래와 같이 해주면됨!   
`$ git clone https://github.com/Alamofire/Alamofire`
- 생성되는 디렉토리 이름을 'Alamofire-test'로 바꾸고 싶다면...   
`$ git clone https://github.com/Alamofire/Alamofire Alamofire-test`





## [2.2 수정하고 저장소에 저장하기](https://git-scm.com/book/ko/v2/Git%EC%9D%98-%EA%B8%B0%EC%B4%88-%EC%88%98%EC%A0%95%ED%95%98%EA%B3%A0-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0)
### 파일의 라이프사이클
- 워킹 디렉토리(내가 작업하고 있는, Xcode 좌측에 보여지는 그 디렉토리임!)의 모든 파일은 크게 Tracked(관리대상임)와 Untracked(관리대상이 아님)으로 나뉜다.
- 📒  Tracked : 추적   
    - Tracked 파일은 <u>이미 스냅샷에 포함되어 있던</u> 파일이다. 즉, **Git이 이미 알고 있는 파일**이라는 의미다.
    - Tracked 파일은 또 Unmodified, Modified, Staged(커밋으로 저장소에 기록할 예정인) 상태 중 하나의 상태를 가진다.
- 그 외 나머지 파일들은 Untracked 파일이다. 워킹 디렉토리에 있는 파일 중 스냅샷에도, Staging Area에도 포함되지 않은 파일이다. (git이 이미 알고 있는 파일도 아니고, 커밋으로 기록할 파일도 아님을 의미)
![git에서 파일의 라이프사이클](https://git-scm.com/book/en/v2/images/lifecycle.png)

### 파일의 상태 확인하기
- 파일의 상태를 확인하기 위해서 `$ git status` 명령어를 사용한다. 아래 그림과 같은 내용을 확인할 수 있다.   
여기서 "Changes to be committed"가 Staged 상태에 있는 것이고, Untracked에 있는 "README.md"는 방금 추가한 파일임.
![git status를 통해 확인한 파일의 상태](./images/2-basic-status.png)
- 파일 상태를 짤막하게 확인할 수도 있는데 이때는 -s 혹은 --short를 함께 붙여준다. `$ git status -s` 혹은 `$ git status --short`   
![git status를 짤막하게 확인하기](./images/2-basic-status-short.png)   
-`??`는 아직 추적하지 않는 파일 앞에 붙는다.   
-`A`는 새롭게 추가해서 staged 상태로 추가된 파일 앞에 붙는다.   
-`M`은 커밋된 파일 중 수정된 파일 앞에 붙는다. test.swift의 경우 수정 후 staged에 올라간 상태에서 다시 수정을 진행해서 두개가 붙은거임.

### 파일을 새로 추적하기
- Modified 상태의 파일을 Stage하기   
"test.swift"파일은 이미 git에 의해 추적되고 있는 파일이다. 해당 파일을 수정하고 `$ git status`를 통해 확인해보면 "Changes not staged for commit"에 파일이 나오는 것을 알 수 있다.   
*(직역하면, 변경사항이 commit을 위해 준비되지 않음! 정도임.)*
![tracked 상태인 파일을 수정하고 상태 확인하기](./images/2-basic-staged-modified.png)

- `$ git add`를 통해 수정한 파일을 Staged 상태로 만들 수 있다.   
`$ git add`는 파일을 새로 추적할 때, 수정한 파일을 Staged 상태로 만들때, Merge할 때 충돌난 상태의 파일을 Resolve 상태로 만들때도 사용한다.   
- `add`의 의미는 프로젝트에 파일을 추가한다기보다는 **다음 커밋에 파일을 추가한다**고 받아들이는게 좋다.

- `$ git add ./test.swift` 명령어를 통해 수정한 test.swift 파일을 staged 상태로 만들었다. 여기서 다시 한번 test.swift 파일을 수정하면 어떻게 될까?   
![staged이면서 unstaged 상태인 파일](./images/2-basic-staged-and-unstaged.png)
🤭 *Staged이면서 동시에 Unstaged하다 !*   
이 상태에서 commit을 하게 되면 현재 워킹 디렉토리에 수정된 최신 버전이 commit되는게 아니라 staged에 올라가있는 버전이 commit되게 된다.   
따라서 staged 이후 다시 수정됐다면 가장 최신 버전을 add해줘야 한다.


### 파일 무시하기
- 어떤 파일은 Git이 관리할 필요가 없다. 그런 파일을 무시하고 싶다면 `.gitignore`파일을 만들고 그 안에 무시할 파일의 패턴을 적으면 된다.
- 위 캡처 이미지들을 보면 `.DS_Store`가 Untracked 상태로 남아있는 걸 볼 수 있음. [이 파일은 Desktop Service Store의 약자로 macOS에서 아이콘 위치나 배경이미지 선택과 같은 폴더의 사용자 지정 속성을 저장하는 파일](https://iteastory.com/201)인데 이건 프로젝트와 직접적으로 연관 없기 때문에 굳이 git이 관리할 필요가 없다. 이런 파일들이 무시해도 되는 파일들임.
```Bash
$ cd {git파일경로}
$ vi .gitignore   # gitignore파일에 데이터 입력하고 새롭게 생성함.
#---- 아래와 같이 입력 후 저장함.
#.DS_Store
$ git status      # 상태 확인해보면 더이상 DS_Store가 untracked에도 나오지 않는 것을 볼 수 있음.
```
- .gitignore 작성 규칙은 아래와 같다.
    - 아무것도 없는 라인, `#`으로 시작하는 라인은 무시한다.
    - 표준 Glob 패턴을 사용한다. 이는 프로젝트 전체에 적용된다.
    - 슬래시(`/`)로 **시작**하면 하위 디렉토리에 적용되지 않는다.
    - 디렉토리는 슬래시(`/`)를 끝에 사용하는 것으로 표현한다.
    - 느낌표(`!`)로 시작하는 패턴의 파일은 **무시하지 않는다.**

아래 내용은 다시 읽어봐야함~~   
*Resolve 상태?*    
*표준 Glob패턴? .gitignore작성 규칙 테스트해보기*   
*Glob 패턴은 정규표현식을 단순하게 만든 것으로 생각하면 되고 보통 쉘에서 많이 사용한다. 애스터리스크(*)는 문자가 하나도 없거나 하나 이상을 의미하고, [abc] 는 중괄호 안에 있는 문자 중 하나를 의미한다(그러니까 이 경우에는 a, b, c). 물음표(?)는 문자 하나를 말하고, [0-9] 처럼 중괄호 안의 캐릭터 사이에 하이픈(-)을 사용하면 그 캐릭터 사이에 있는 문자 하나를 말한다. 애스터리스크 2개를 사용하여 디렉토리 안의 디렉토리 까지 지정할 수 있다. a/**/z 패턴은 a/z, a/b/z, a/b/c/z 디렉토리에 사용할 수 있다.*

### Staged와 Unstaged 상태의 변경 내용을 보기
- 파일의 어떤 라인이 추가됐고 삭제됐는지를 보고 싶다면 `$ git diff`명령을 사용한다.
![git diff 결과](./images/2-basic-git-diff.png)
- **워킹 디렉토리에 있는 것(Unstaged 상태)과 Staging Area에 있는 것을 비교**한다. 그래서 수정하고 아직 stage하지 않은 것을 보여준다.
- 만약 Staging Area에 올린 파일의 변경 부분을 보고 싶다면 `$ git diff --staged` 혹은 `$ git diff --cached`옵션을 사용한다. **저장소에 커밋한 것과 Staging Area에 있는 것을 비교**한다.
![git diff --staged 결과](./images/2-basic-git-diff-staged.png)
*첫번째 이미지와 비교했을 때 index 및 수정된 내용이 다른 걸 볼 수 있다.*
- `$ git diff`명령은 수정 내용 전체를 보여주는게 아니라 Unstaged 상태인 것들만 보여준다!
- `$ git difftool --tool-help` 명령을 통해 diff 비교에 도움을 주는 툴을 볼 수 있다.

### 변경사항 커밋하기
- `$ git commit`을 통해 Staged 상태에 있는 파일들을 커밋한다. (단, Unstaged 상태의 파일은 커밋되지 않음)
- Staging area를 거치지 않고 다이렉트로 Tracked 상태의 파일을 커밋할 수 있는 방법으로는 `$ git commit -a`를 사용하는 것이다.(git이 자동으로 Staging area에 파일들을 넣어줌)    
🖐 필요하지 않은 파일도 commit될 수 있으므로 주의해서 사용해야 함.

### 파일 삭제하기(여기서붙~~)
- **git에서 파일을 제거하려면** `$ git rm` 명령으로 **Tracked 상태의 파일을 삭제**한 후(정확하게는 Staging Area에서 삭제하는 것) 커밋해야 한다. 이 명령은 워킹 디렉토리에 있는 파일도 삭제하기 때문에 실제로 파일도 지워진다.
- git 명령을 사용하지 않고 단순히 워킹 디렉토리에서 파일을 삭제 후 `$ git status` 명령으로 상태를 확인하면 Git은 현재 "Changes not staged for commit" (즉, Unstaged 상태)라고 표시해준다.

## [2.3 커밋 히스토리 조회하기]()


## [2.4 되돌리기]()


## [2.5 리모트 저장소]()


## [2.6 태그]()


## [2.7 Git Alias]()