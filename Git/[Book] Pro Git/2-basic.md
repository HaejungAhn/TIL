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
- 이 명령으로는 아직 어떤 파일도 관리하지 않은 상태이다. git add와 commit 명령어를 이용해 파일을 Staging area에 올리고 커밋할 수 있다.

**`$ git clone <url>`**
- 다른 어딘가로부터 Git 저장소를 하는 복제하는 방법이다.
- 프로젝트의 히스토리를 모두 가져온 후 자동으로 가장 최신버전을 checkout해둔다.
- 만약 Alamofire라는 디렉토리를 새롭게 만들고 그 안에 Alamofire의 소스코드를 clone하고 싶다면 아래와 같이 해주면됨!   
`$ git clone https://github.com/Alamofire/Alamofire`
- 생성되는 디렉토리 이름을 'Alamofire-test'로 바꾸고 싶다면...   
`$ git clone https://github.com/Alamofire/Alamofire Alamofire-test`





## [2.2 수정하고 저장소에 저장하기]()


## [2.3 커밋 히스토리 조회하기]()


## [2.4 되돌리기]()


## [2.5 리모트 저장소]()


## [2.6 태그]()


## [2.7 Git Alias]()