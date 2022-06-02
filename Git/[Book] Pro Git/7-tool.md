# 7. Git 도구
- 하나씩 보면 좋겠지만 시간관계 상 해당 챕터에서는 "Stashing과 Cleaning, Reset 명확히 알고 가기"를 먼저 보고 다음으로 넘어가도록 하겠다.

## [7.3 Stashing과 Cleaning](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-Stashing%EA%B3%BC-Cleaning)
### Stash
- 📒  stash : 숨기는 장소, 몰래 챙겨두다
- `git stash`가 해결하는 문제   
어떤 프로젝트의 한 부분을 작업하고 있는 중이었다. 근데 급한 요청으로 잠시 브랜치를 변경해야 할 필요가 있는 경우, 아직 미완성된 작업을 커밋하기는 껄끄러울 것이다. 이때 커밋하지 않고 나중에 다시 돌아와서 작업할 수 있도록 해주는게 `git stash`다.

- `git stash`명령을 사용하면 워킹 디렉토리에서 수정한 파일들만 저장한다.
- stash는 Modified이면서 Tracked 상태인 파일과 Staging Area에 있는 파일들을 보관해두는 장소다.
- git은 stash를 적용할 때 staged 상태(`git add`를 통해 staging area에 올라간 상태)였던 파일을 자동으로 다시 Staged 상태로 만들어주지 않는다. 그래서 `git stash apply`명령을 실행할 때 `--index` 옵션을 주어 Staged 상태까지 적용한다.
    ```Bash
    # test.md와 test2.md 파일을 수정하고, test.md 파일만 staged 상태로 만든다.
    $ git status
    ```   
    <img src="./images/7-git-stash-01.png">   

    ```Bash
    # git stash를 진행하고 status를 확인해본다.
    $ git stash
    Saved working directory and index state WIP on TEST-1: 0331192 Merge branch 'M02' into M01

    # working directory가 깨-끗.
    $ git status
    ```

    ```Bash
    # git stash apply를 해주면 staged 상태가 적용되지 않은 것을 볼 수 있다.   
    # staged 상태도 적용하고 싶다면 --index 옵션을 붙여줘야 한다.
    $ git stash apply
    ```   
    <img src="./images/7-git-stash-02.png">   

- `git stash apply`는 단순히 Stash를 적용하는 것 뿐이다. Stash는 여전히 스택에 남아있음.
    ```Bash
    $ git stash list
    stash@{0}: WIP on TEST-1: 0331192 Merge branch 'M02' into M01
    stash@{1}: WIP on TEST-1: 0331192 Merge branch 'M02' into M01
    stash@{2}: WIP on TEST-1: 0331192 Merge branch 'M02' into M01
    ```
- Stash를 삭제하고 싶다면 `git stash drop` 명령어를 사용하여 해당 stash를 제거한다.
    ```Bash
    $ git stash drop stash@{0}
    ```

### 그 외 옵션
- `git stash --include-untracked` 혹은 `git stash -u`   
기본적으로 추적 중인 파일만 저장하는데 위 옵션을 추가해주면 추적 중인 파일과 추적하지 않는 파일도 저장할 수 있다.
- `git stash --patch`   
모든 수정사항을 저장하지 않는다. 대신, 대화형 프롬프트가 뜨면서 변경된 데이터 중 저장할 것과 저장하지 않을 것을 지정할 수 있다.
- `git stash branch <브랜치명>`   
stash할 당시의 커밋을 체크아웃한 후 새로운 브랜치를 만들고 여기에 stash된 것을 적용함.

### 워킹 디렉토리 청소하기
- 작업하고 있던 파일을 Stash 하지 않고 단순히 그 파일들을 치워버리고 싶을 때가 있다.   
- `git clean`명령이 그 일을 한다. (단, untracked file에 대해서만)
- 이 명령은 신중하게 사용해야 한다. 이걸 사용하면 워킹 디렉토리 안에 있는 "추적하고 있지 않은" 모든 파일이 지워지기 때문이다. 한번 지워진 파일은 절대 돌아오지 못한다.😢
- `git stash --all` 명령을 이용하면 지우는 건 똑같지만, 먼저 모든 파일을 Stash하므로 좀 더 안전하다.   
    -> 실제로 해보니 untracked 파일이 모두 삭제된 것을 볼 수 있었다. 또한 수정 사항들이 Stash에 저장된 것도 확인함.

- `git clean -f -d` 명령을 사용하면 추적중이지 않은 모든 정보를 워킹 디렉토리에서 지울 수 있다. 하위 디렉토리까지 지워버린다고 함. 이 명령으로 어떤 일이 일어날지 미리 보고 싶다면 `git clean -d -n`(-n 옵션 추가)을 사용하면 됨.
- 기본적으로 `git clean`은 추적중이지 않은 파일만 지운다. `.gitignore`에 명시돼서 무시되는 파일은 지우지 않지만, 무시된 파일까지 지우려면 `-x`옵션도 추가해준다.