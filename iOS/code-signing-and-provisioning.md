2022-03-08

---
📚 참고
- [iOS App Distribution & Best Practices, Chapter 4. Code Signing & Provisioning](https://www.kodeco.com/books/ios-app-distribution-best-practices/v1.0/chapters/4-code-signing-provisioning)

아래 문서들은 아직 안봄.
- [App Sandbox](https://developer.apple.com/documentation/security/app_sandbox)
- [What is app signing?](https://help.apple.com/xcode/mac/current/#/dev3a05256b8)
- [code signing](https://help.apple.com/xcode/mac/current/#/devfbe995ebf)
- [siging certificate](https://help.apple.com/xcode/mac/current/#/dev1c7c2c67d)
- [signing & capabilities workflow](https://help.apple.com/xcode/mac/current/#/dev60b6fbbc7)
- [Demystifying iOS app provisioning process](https://www.bounteous.com/insights/2018/08/08/demystifying-ios-app-provisioning-process)

**Keyword**

- certificate
- entitlement
- provisioning profile
- code signing

**History: 왜 code signing이 필요한가**

- Apple의 플랫폼은 모두 1970년대 개발된 Unix 버전을 기반으로 한다.
- Unix에서 프로그램은 사용자와 동일한 액세스 및 권한을 가진다. 1970년대에는 이게 적절했던 이유가, 그때는 천공카드를 이용해 자신의 프로그램을 코딩하고 로드해야 했기 때문이다. (프로그램 작성자 = 사용자)
    
    하지만 최근에는 3rd-party 프로그램(내가 작성하지 않은)이 많아졌기 때문에 위의 가정은 타당하지 않다.
    
- 따라서 보안상의 이유로 프로그램은 사용자와 동일한 접근 권한을 가져서는 안된다.
- 이런 보안상의 이슈를 해결하기 위해 Apple은 `App Sandbox` 라고 불리는 시스템을 고안해냈다.
    - 사용자의 권한을 3rd-party 앱으로부터 분리함. 앱과 운영체제의 리소스를 엄격히 분리함으로써 모든 것에 대한 권한을 요구한다.

**App Sandbox**

- “sandbox”의 의미
    
    📒 나무나 플라스틱으로 만들어진 공간에 모래를 담아 아이들이 놀 수 있게 하는 공간.
    
    - 기본적으로 sandbox 앱은 파일시스템에서 앱에 특화된 부분과 앱 자체에서 생성하고 저장하는 데이터에만 액세스 할 수 있다.
- sandbox의 외부에는, 호스트 디바이스가 앱에서 필요로 할 수 있는 마이크 또는 카메라 등의 리소스를 가지고 있다. 그러나 보안상의 이유로 운영체제는 이 제한된 리소스를 철저하게 지키고 있다.
    - 이를 사용하기 위해서, **앱에서 ID를 확인하고(to validate its identity) OS 또는 사용자에게 권한을 요청**해야 한다.

- 권한(Permission)이 필요한 영역은 크게 아래와 같이 나뉘게 된다.
*기술문서에서는 permission 대신 entitlements, capabilities, resource 등의 용어가 주로 사용된다.
    - Hardware: 카메라, 마이크, 센서 등
    - Network access: inbound, outbound 트래픽
    - Data from other apps: 연락처, 캘린더, 이메일 등
    - User files: 파일시스템 또는 파일 앱의 파일들(사진, 동영상 등을 말하는걸까?)
    - Special functionality: Push notifications, 홈킷 액세스 등
    

- App Sandbox가 동작하는 (러프한) 다이어그램은 아래와 같다.
    
    ![https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/ebbab3000302b4bfc79995ce00b321f94a80bcce5a5d706c46daceea2421bf23/original.jpg](https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/ebbab3000302b4bfc79995ce00b321f94a80bcce5a5d706c46daceea2421bf23/original.jpg)
    
    - Policy : 앱의 entitlement를 기반으로 권한을 부여하거나 block시킨다.

**Outside the sandbox**

위 이미지의 좌측 컬럼(파란색)에 있는 것들이 해당됨.

**Runtime policy system**

위 이미지에서 “POLICY”라고 적혀있는 부분.

- 런타임 시, 샌드박스 앱은 앱이 실행되지 않는 동안 제한된 작업을 시도할 수 있다.(백그라운드 위치 업데이트를 받는 등의)
    
    policy 시스템은 이런 작업을 가로채고 여러 확인을 거쳐 작업을 허용하거나 허용하지 않는다.
    
- 백그라운드 위치 업데이트를 예로 들면, 이 작업을 위해서는 아래 3가지가 필요하다.
    - 위치 데이터를 받는다는 것에 유저가 명백히 동의해야 함.
    - info.plist에 왜 이 데이터가 필요한지 명시
    - background mode 위치 entitlement
- 런타임 policy system은 위 사항을 모두 확인하여 앱이 작업을 계속 진행하도록 할지 말지를 결정한다.
- 앱 샌드박스와 관련한 더 자세한 사항은 [이 문서](https://developer.apple.com/documentation/security/app_sandbox)를 참고!

**Inside App Sandbox**

위 이미지에서 점선으로 표시된 부분이다.

- 각 샌드박스는 제한 없이 접근 가능한 자신만의 파일 시스템을 가지고 있다.(노란색 영역 안에 들어있는 폴더이미지)
- 샌드박스의 중심에는 앱 바이너리를 포함하고 있는 “locked area”가 있다. 이건 잠겨있는데 왜냐하면 Xcode가 `codesign` 을 이용해 디지털 서명을 했기 때문이다. 무언가를 code-signing 하고나면, 확인하기 쉬운 code seal을 깨지 않고는 어떠한 변경도 불가능하다.
- provisioning profile은 앱 바이너리에 포함되는 파일이다.(따라서 locked area 안에 위치해있다) 앱이 누구인지, 실행할 수 있는 위치가 어디이며 원하는 작업이 무엇인지 자세히 설명하는 공동 선언문이라고 생각할 수 있다.
- code signing과 관련된 더 자세한 사항은 [이 문서](https://developer.apple.com/library/archive/documentation/Security/Conceptual/CodeSigningGuide/AboutCS/AboutCS.html)를 참고!

**Working backward**

- 앱 샌드박스는 앱이 제한된 작업을 완료하기 전에 몇가지 검사를 수행한다. OS가 질문을 하면 `code-signed provisioning profile`이 여기에 답한다.
    
    ![https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/e14eb7ee27c58d74221ce25dc3dc170bb3fae32badd450edb9b7ae29634a7f15/original.jpg](https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/e14eb7ee27c58d74221ce25dc3dc170bb3fae32badd450edb9b7ae29634a7f15/original.jpg)
    
    OS로부터 받은 질문 중 내가 누구인지, 내 의도는 무엇인지에 대한 답은 있지만 이 답들은 plain 파일에 기록되어 있을 뿐이다. 악의적인 누군가가 이를 덮어쓸 수 있다. 
    
    이를 위해 provisioning profile은 증명서(certificate) 형태로 developer identity를 포함하고 있다. certificate은 public key를 포함하고 있는데 이를 이용해 Apple은 public key에 상응하는 private key로 만들어진 digital signature인지를 검증할 수 있다.
    

**배포 규칙에 따라 provisioning profile의 내용도 조금씩 달라진다.** ad hoc distribution(앱 스토어 외부 배포를 의미)의 경우 최대 100개의 디바이스에서만 앱 설치가 가능한데 이 경우 provisioning profile은 앱을 사용할 수 있는 장치 ID 목록도 포함된다.

> The real source of truth is the digital signature, not the provisioning profile.
> 

**Verifying your identity**

policy 시스템이 code-singed provisioning profile에게 하는 질문 중 하나는 바로 “can I trust you?”이다.

분명하게 앱과 개발자를 식별하기 위해, 프로비저닝 프로파일에 certificate을 임베드해야한다.

(certificate을 찾는 방법에 대한 내용임)

![https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/2a1600221a181317512de53869ce29c425be9af6fa3e1e3b165c19038c3d5077/original.png](https://assets.alexandria.raywenderlich.com/books/d78280ad5745db76eb57cb9ad83b35fe460b6a762b3ccf6dd8341070f8f5e5d1/images/2a1600221a181317512de53869ce29c425be9af6fa3e1e3b165c19038c3d5077/original.png)

**[development certificate / distribution certificate 차이?](https://www.bounteous.com/insights/2018/08/08/demystifying-ios-app-provisioning-process)**

- 오직 배포 인증서만이 앱 스토어에 앱을 배포할 수 있다.

- `Isseud by: Apple Worldwide Developer Relations Certification Authority`
    - 위 인증 기관에서 인증서를 발급했다.
    - 인증기관의 역할: Apple’s WWDR 인증기관은 Apple Developer Program team ID를 공개 키에 연결한다.
- 위 인증서는 X.509 암호화를 기반으로 한다.
    - 이 암호화는 공개기 암호화 방식으로 공개키와 개인키가 쌍을 이뤄 동작한다. 공개키는 다른사람에게 노출하고 개인키(private key)는 비밀로 유지함.
    - 앱 배포라는 컨텍스트에서, Xcode는 개발자의 private key를 이용해 앱 디지털 서명을 진행한다. 그리고 난후, certificate을 통해 공개키를 Apple에게 제출하면 이를 이용해 디지털 서명이 진짜 그 개발자에게서 온 것인지를 확인할 수 있다.
    - Certificate Signing Request(CSR)도 X.509 암호화의 일부이다. Apple에게 내 인증서에 서명해달라는 공식적인 요청방식임.
- Code signing은 유효한 인증서와 private key를 필요로한다.
    - 인증서는 몇가지 이유로 유효하지 않을 수 있다. (유효기간 만료 혹은 인증서가 손상된 경우 apple 또는 개발자가 인증서를 revoke(취소) 시킬 수도 있음)
    - Xcode에서 digital signature를 만들려면, “키체인접근”에 인증서의 private key가 등록되어 있어야 한다.
    - 프로비저닝 프로파일에 있는 인증서가 revoke되거나 만료되면, 전체 프로비저닝 프로파일이 무효화된다.(그럼 앱 동작 안되는건가?)

{이후로는 인증서를 새롭게 생성하는 방법, CSR 파일 생성 및 업로드 하는 방법에 대한 내용이 나옴}

**Adding entitlements**

entitlements는 “what would you like to do?”와 관련된 질문에 답한다.(이 앱이 무엇을 할 수 있는지, 그리고 권한이 있는지를 표현함)

- 모든 샌드박스 앱은 entitlement를 사용한다.
    
    ```swift
    // 설치된 앱의 entitlement를 보는 방법
    codesign -d --entitlements - /Applications/Keynote.app
    
    /*
    Executable=/Applications/Keynote.app/Contents/MacOS/Keynote
    [Dict]
    	[Key] com.apple.accounts.appleaccount.fullaccess
    	[Value]
    		[Bool] true
    	[Key] com.apple.application-identifier
    	[Value]
    		[String] 74J34U3R6X.com.apple.iWork.Keynote
    	[Key] com.apple.developer.ClassKit-environment
    	[Value]
    		[String] production
    	[Key] com.apple.developer.aps-environment
    	[Value]
    		[String] production
    ...생략
    */
    ```
    
    - `codesign`은 Mac에 내장된 디지털 서명 바이너리용 커맨드라인 툴이다. 위 명령어를 이용하면 entitlement의 plist를 볼 수 있다.
    - 빌드를 아카이브할 때 Xcode는 `codesign` 을 이용한다.
    - Entitlements와 관련한 더 자세한 사항은 [이 문서](https://developer.apple.com/documentation/bundleresources/entitlements)를 참조!

**Generating a provisioning profile**

앱 ID, entitlement, certificate을 프로비저닝 프로파일에 하나로 묶어보자.

Xcode는 모든 프로비저닝 파일들을 `~/Library/MobileDevices/Provisioning Profiles` ****경로에 저장한다.

아래 명령어를 이용해 폴더를 오픈한 후 하나를 선택 & 스페이스를 누르면 내용을 자세히 볼 수 있다.

```swift
open ~/Library/MobileDevice/Provisioning\ Profiles
```

**Code signing and distribution**

Build Settings > Signing 항목에는 4가지 빌드 세팅이 있다.

- Code Signing Identity
    - 앱의 code-signing identity는 certificate를 세팅한다.
    - 코드 사이닝을 수동으로 선택하더라도 옵션은 4가지 밖에 안나온다: Apple Development, iOS Developer, Apple Distribution, iOS Distribution
        - 만약 코드 사이닝 automatically 옵션을 선택했다면, Xcode는 아래에서 선택된 프로비저닝 프로파일로부터 certificate을 자동으로 추론할 수 있다.
        - iOS Development, Apple Developement의 차이는 뭐지????
- Code Signing Style
    - Automatic or Manual
- Development Team
    - 빌드 세팅에서 선택한 team은 certificate, 프로비저닝 프로파일에서 선택한 것과 동일해야 한다.
- Provisioning Profile
    - 빌드에 어떤 프로비저닝 프로파일을 포함시킬지를 결정한다.