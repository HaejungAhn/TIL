### git revert
- git revert(참고 : [여기](https://kyounghwan01.github.io/blog/etc/git/git-reset-revert/#revert))
로컬에서 작업할 때는 보통 reset을 많이 사용했는데 revert는 어떤 작업인지 알아봄.
- reset과 비슷하게 이전 커밋으로 돌아가기 위해 revert를 사용한다고 함. 차이가 있다면 github 등 repo에 해당 코드가 반영, 다른 개발자들도 해당 코드를 공유했을 경우 사용할 수 있는게 다른데, 공유가 되었다면(즉 돌아가기 할 코드가 포함된 커밋을 기준으로 풀 등 다른 작업을 했다면) revert를, 공유가 되지 않았다면 reset을 사용할 수 있음.
- reset : 로컬 커밋에 머물렀다면 혹은 나만 해당 브랜치를 사용한다면 reset 사용 가능
    - --hard(변경이력, 코드 모두 삭제) / --mixed(변경 이력 삭제, 코드는 unstaged) / --soft(변경 이력 삭제, 코드는 staged)
- revert : 커밋을 삭제하지 않음. 커밋을 추가하는 방식으로 코드를 되돌린다.
    - --no-commit 옵션을 통해 revert한 것을 바로 커밋하지 않도록 만들 수 있다.