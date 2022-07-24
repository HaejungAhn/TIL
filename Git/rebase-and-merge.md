Github에서 만든 pull request(이하 PR)를 merge하는 방법은 총 3가지가 있다. 하나씩 알아보고, rebase에 대해서도 간략히 보겠음.

### <b>feature 브랜치의 모든 커밋을 유지하면서 merge</b>
- PR 시 기본이 되는 merge 방식
- `--no-ff` option 사용 -> 3-way merge를 진행하며, merge commit이 만들어진다.
- repo에 대한 write 권한이 있어야 함.

### <b>squash and merge</b>
- 📒 squash 🅅눌러 찌그러뜨리다, 밀어넣다, 찌부러지다.
- feature 브랜치의 모든 커밋을 한개의 단일 커밋으로 찌그러뜨린 후 base 브랜치에 merge한다.
- `fast-forward` option 사용
    - 🤔 만약 공통 조상 커밋이 존재하지 않을 경우에는 어떻게 되나?
- 이런 권한이 필요해요!
    - repo에 대한 write 권한
    - repo가 squash merging을 허용해야 함.
#### <b>🤷‍♀️ 도대체 왜 커밋을 찌그러뜨리는 거에요?</b> 
- git 커밋 히스토리를 간소하게 만들기 위해. 중요하지 않은 것은 간소화하고 중요한 것에 집중할 수 있도록 하는 것이다.
- 어떤 feature에 대한 작업을 진행 중일 때 생성한 모든 커밋들이 반드시 git 히스토리에 유지되어야 하는 것은 아니다. 예를 들면 Chore한 것들, 오타를 수정하거나 백그라운드 색상을 변경하는 등의 커밋들은 없어도 히스토리를 파악하는데 큰 영향을 미치지 않는다.
- 따라서 base 브랜치에 merge할 때 이런 커밋들을 하나로 찌그러뜨려 커밋함으로써 git 히스토리를 명확하게 만들 수 있다.
#### <b>✋ 사용 시 주의사항</b> 
- 만약 feature 브랜치의 PR이 merge된 이후에도 계속 해당 브랜치에서 작업을 진행할 예정이라면 squash를 사용하지 않는 것이 좋다.
- PR을 생성할 때 github은 feature 브랜치와 base 브랜치가 모두 가지고 있는 가장 최근 커밋(공통 조상 커밋)을 식별한다.(이걸 기준으로 merge를 진행?)
- squash merging을 하게 되면 github은 base 브랜치에 새로운 커밋을 만드는데 이 커밋은 공통 조상커밋 이후부터 feature 브랜치에 작업된 변경사항을 모두 포함하는 commit이다. 
- 이렇게 만들어진 커밋은 base 브랜치에만 있기 때문에(feautre 브랜치에는 없음) PR을 새롭게 생성할 때 두 브랜치의 공통 조상 커밋은 이전 PR과 동일해진다.(즉, 변경되지 않음.)
- 만약 squash 이후 feature 브랜치에서 계속 작업을 진행, PR을 생성하게 되면 이때 생성되는 커밋은 squash merging을 한 내역을 포함한, 공통 조상커밋 이후의 모든 변경사항을 포함하게 된다.
- conflict가 발생하지 않는다면 다행이지만 보통 이런 작업 흐름은 conflict가 발생할 가능성이 훨씬 크다.
    - 🤔 squash merge를 했을 때 commit은 어떻게 보여지는 걸까? fast-forward 옵션을 이용한다는데 만약 base 브랜치가 서로 다르면(공통 조상 커밋이 없다면) 어떻게 되는거지?
        

### <b>rebase and merge</b>
- 우선 이 내용을 이해하려면 rebase에 대해 알아야 한다.
    - git에서는 한 브랜치를 다른 브랜치로 합치는 방법이 두가지가 있는데 `merge`와 `rebase`가 그것이다.
    - 공통 조상 커밋을 가진 A, B 브랜치를 합칠 때 A를 Patch로 만들고 이를 다시 B에 적용시킴으로써 두 브랜치를 합칠 수 있는데 이런 방식을 `Rebase`라고 한다. 과정은 다음과 같다.
        1. 공통 조상 커밋으로 이동 
        2. 공통 조상 커밋부터 현재 체크아웃한 브랜치(A)가 가리키는 커밋까지 diff를 차례로 만들어 어딘가에 임시 저장
        3. Rebase할 브랜치(A)가 합칠 브랜치(B)가 가리키는 커밋을 가리키게 함
        4. 2번에서 저장해두었던 변경사항을 차례로 적용
        5. 새로운 커밋이 만들어지며 A 브랜치는 이 커밋을 가리키게 됨.
        5. A 브랜치와 B 브랜치를 fast-forward로 merge시킴.
    - A 브랜치의 base가 변경되는 작업이 발생하기 때문에 "re"base라는 단어를 사용하는 것 같다.
    - merge보다 조금 더 깨끗한 히스토리를 만든다. rebase하던, merge하던 최종 결과물은 같다. 커밋 히스토리가 다를 뿐.
    
- feature 브랜치의 모든 커밋이 merge commit 없이 base 브랜치에 추가된다.
- 프로젝트의 커밋 히스토리를 linear하게 만든다는 점에서 fast-forward merge와 닮았다. 그러나 rebasing은 base branch에 새로운 커밋 히스토리를 재작성함으로써 linear하게 히스토리를 만든다는 점이 다르다.
- git이 제공하는 rebase와 github의 rebase는 동작방식이 조금 다르다. committer 정보를 업데이트하냐 아니냐의 차이.
- 이런 권한이 필요해요!
    - repo에 대한 write 권한
    - repo가 rebase merging을 허용해야 함.


### TO-DO
- 한번 더 읽어보고 좀 더 잘 이해하고 기억에 남을 수 있도록 테스트를 진행해본다.



### <b>브랜치의 다양한 이름들</b>
- 문서를 읽다보니 head 브랜치, feature 브랜치, base 브랜치, topic 브랜치 등 여러 브랜치 이름들이 나왔다.
- 간략하게 정리해둔다.
    - [`head`브랜치](https://docs.github.com/en/get-started/quickstart/github-glossary#head-branch) : 변경사항이 base branch로 merge되는 브랜치. compare branch라고도 함.
    - [`base`브랜치](https://docs.github.com/en/get-started/quickstart/github-glossary#base-branch) : 변경사항이 적용되는 브랜치.
    - [`topic`브랜치](https://docs.github.com/en/get-started/quickstart/github-glossary#topic-branch) : 개발자가 개념적인 development line을 구분하는데 사용하는 일반적인 git branch. feature branch라고도 함.


---
#### <b>📚참고 사이트</b>
- [About pull request merges, Github Doc](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges)