2022-03-16

---
이미지 픽커를 사용하면서 PHImageManager와 PHCacheImageManager에 대해 공부를 하지 않음.

- 무엇이며 어디에 쓰는가
- 어떤 특징을 가지고 있나
- 두 인스턴스 간 차이는 무엇인가

---

들어가기에 앞서 `PhotoKit` 에 대해 먼저 살펴보겠음. 해당 프레임워크 안에 있는 것이기 때문에!

## What is PhotoKit?

![[https://developer.apple.com/documentation/photokit](https://developer.apple.com/documentation/photokit)](https://docs-assets.developer.apple.com/published/3459c84072/a27985f6-1ee8-4116-98c7-9199674be2df.png)

[https://developer.apple.com/documentation/photokit](https://developer.apple.com/documentation/photokit)

- Photos 앱에 의해 관리되는 이미지나 비디오 asset(iCloud Photos, Live Photos 포함)을 활용할 수 있음.
- iOS, macOS에서 PhotoKit은 Photos app용 photo-editing 확장을 만드는데 도움이 되는 클래스를 제공한다. 또한 iOS, macOS, tvOS에서 **Photos app에 의해 관리되어지는 사진이나 영상 asset에 직접 접근**도 제공한다.
- PhotoKit을 사용하면 디스플레이나 재생을 위해 **asset을 가져오거나 캐싱**할 수 있고 이미지나 비디오 **컨텐츠를 수정**할 수 있으며 앨범이나 Moments, 공유 앨범과 같은 asset의 **컬렉션(앨범)도 관리**할 수 있다.

## What is `PHImageManager`?

`What` 

미리보기 썸네일 및 asset data의 검색이나 생성을 쉽게 도와주는 객체

`When` 

PHAsset(only metadata)을 이용해 풀사이즈 photo asset 또는 썸네일 이미지를 가져오거나, 비디오 asset을 재생, export, 조작하기 위해 AVFoundation 객체를 검색할 때 사용할 수 있다.

- 이미지나 비디오 데이터 가져오기
    
    1) `PHAsset` 을 이용해야함.(메타데이타만 가지고 있으며 immutable하다.)
    
    2) singleton임. default()를 이용한다.
    
    3) [Requesting Images](https://developer.apple.com/documentation/photokit/phimagemanager#1656241) 또는 [Requesting Video Objects](https://developer.apple.com/documentation/photokit/phimagemanager#1656266)에 리스팅된 메소드를 이용해서 asset의 이미지나 비디오 데이터를 가져온다.
    
- ImageManager는 **해당 인스턴스가 제공하는 이미지나 데이터 asset을 캐싱**하기 때문에 유사한 매개변수로 동일한 asset을 요청하는 것은 더 빠른 결과를 리턴한다.
- 만약 **많은 asset에 대한 이미지 데이터를 한꺼번에 로드해야한다면,** 곧 필요할 것으로 예상되는 이미지를 로드함으로써 **캐시를 “preheat(예열)”하기위해 PHCachingImageManager를 사용**해라. 만약 photo asset 썸네일로 collection view를 만들경우, 현재 스크롤 포지션보다 먼저(ahead of the current scroll position) 이미지를 캐시할 수 있다.

## What is `PHCachingImageManager`?

`what`  

- 미리보기 썸네일의 검색이나 생성을 쉽게 도와주는 객체로 많은 양의 asset을 일괄적으로 사전로딩(preloading)하는데 최적화 되어 있다.
- `PHImageManager` 를 서브클래싱한 것.

`when`

- 많은 양의 asset을 다룰때 빠른 퍼포먼스를 위해 caching 이미지 매니저는 나중에 개별 이미지를 요청했을때 발생하는 딜레이를 제거하기 위해 백그라운드에서 asset 이미지를 준비한다.

- caching 이미지 매니저 사용하기
    1. 해당 인스턴스를 생성
    2. PHAsset을 이용
    3. 2번의 asset에 해당하는 이미지를 준비하기 위해 `startCachingImages(for:targetSize:contentMode:op tions:)` 메소드를 호출한다.
    4. 각 asset의 이미지가 필요한 경우 `requestImage` 메소드를 활용한다. 이때 3번의 메소드를 호출하면서 사용한 파라미터를 동일하게 사용해야함.
    
- 만약 요청한 이미지가 이미 준비되어 있다면 이를 즉시 반환한다. 그러나 준비되지 않을경우 Photos는 온디맨드로 이미지를 준비하고 나중을 위해 이를 캐싱해둔다.

## 두 object간 차이

- 쓰임새가 다르다. 만약 대량의 asset에 대한 이미지를 가져와야 하는 경우라면 caching 이미지 매니저를 사용해야한다. 

- 지정된 사이즈로 asset의 이미지를 요청하거나 video asset을 사용하기 위해 AVFoundation 객체를 요청해야하는 경우라면 PHImageManager를 사용.

## 주요 메소드

`requestImage(for:targetSize:contentMode:options:resultHandler:)`

1. 해당 메소드가 호출되면, Photos는 asset의 이미지를 로드하거나 생성한다. (지정한 사이즈에 맞게) 
2. resultHandler 블럭을 호출한다. 요청한 이미지를 제공하기 위해. 

- 요청을 빠르게 수행하기 위해 Photos는 타겟사이즈보다 약간 더 큰 사이즈의 이미지를 제공할 수 있다(이미 캐싱되었거나 더 효율적으로 생성될 수 있기 때문)
- 기본적으로는 비동기 작업이지만, 만약 백그라운드에서 이를 호출한다면 동기적으로 처리할 수도 있다.
- 비동기 요청을 위해 Photos는 result handler를 한번 이상 호출할 수도 있다. Photos는 먼저 고품질 이미지를 준비하는 동안 일시적으로 디스플레이하기에 적합한 저품질 이미지를 제공하기 위해 블럭을 호출한다. (만약 저품질 이미지를 즉시 사용할 수 있다면, 첫번째 블럭 호출은 method가 리턴되기 직전에 이뤄질 것이다.) 고품질 이미지가 준비되면, 이를 제공하기 위해 Photos는 블럭을 다시 한번 호출한다. 만약 이미지 매니저가 이미 고품질 이미지를 캐싱하고 있다면, Photos는 핸들러를 딱 한번 호출할 것이다. 핸들러의 info 파라미터에 있는 PHImageResultIsDegradedKey는 Photos가 일시적으로 사용할 저품질 이미지를 제공했다는 것을 나타낸다.
- 이 메소드는 이미지와 비디오 asset에 사용될 수 있는데, video asset에 사용할 경우 썸네일 이미지 또는 포스터 프레임을 제공한다.

`requestAVAsset(forVideo:options:resultHandler:)`

- Video asset의 컨텐츠와 상태를 나타내는 AVFoundation 객체를 비동기로 로드되도록 요청한다.
- asset에 포함된 오디오, 비디오 트랙을 이용해야할 경우 이를 활용해라. 만약 재생만 사용할거라면, `[requestPlayerItem(forVideo:options:resultHandler:)](https://developer.apple.com/documentation/photokit/phimagemanager/1616958-requestplayeritem)`
를 호출해라. asset 데이터를 export할 예정이라면 `[requestExportSession(forVideo:options:exportPreset:resultHandler:)](https://developer.apple.com/documentation/photokit/phimagemanager/1616981-requestexportsession)`
를 활용해라.

## 옵션

이미지 매니저를 이용해 이미지나 영상, 라이브 포토 등을 가져올 때 다양한 옵션을 설정할 수 있다. 

### PHImageRequestOptions

이름 그대로 이미지(still image)를 요청할 때 사용할 수 있는 옵션.

- isSynchronous - Photos가 이미지 요청을 동기적으로 처리할지 여부를 결정. false라면 requestImage() 메소드가 바로 리턴된다. deleiveryMode 프로퍼티에 따라 Photos는 해당 메소드의 resultHandler를 메소드가 반환되기 전, 혹은 나중에, 혹은 둘다(메소드 반환되기 전 & 나중에)에 호출할 수 있다. true라면 이미지가 준비되거나 에러가 발생할때까지 스레드를 호출한다. Photos는 딱 한번 result handler를 호출한다. 백그라운드 스레드에서만 동기 요청을 수행해야한다!
- version - PHImageRequestOptionsVersion타입. adjustment가 있거나 없는 이미지 asset을 요청할 때 사용됨.
    
    current : 가장 최신 버전 / unadjusted : adjustment가 없는 이미지 asset 요청 / original : 이미지 asset의 고화질 원본 버전을 요청.
    
- deliveryMode - PHImageRequestOptionsDeliveryMode 타입. 이미지 퀄리티와 delivery 우선순위를 설정함.
    
    opportunistic : 이미지 퀄리티와 응답속도 간 발란스를 Photos가 자동으로 유지함 / highQualityFormat : Photos는 얼마의 시간이 걸리던 상관없이 가장 고품질의 이미지를 제공함 / fastFormat : 이미지 퀄리티를 희생시키더라도 가장 빠르게 로딩되도록 함.
    
- resizeMode - PHImageRequestOptionsResizeMode타입. 요청한 이미지의 크기를 조정하는 방법을 설정함. 타겟 사이즈에 이미지가 어떻게 fit되야하는지. 기본값은 fast임. requestsImage 메소드의 targetSize 파라미터를 지정해야 한다.
    
    none : Photos는 이미지 리사이즈 안함 / fast : 효율적으로 리사이즈함(엇비슷하거나 조금 더 크게 리사이징) / exact: target size에 정확히 일치하게끔 리사이징함.
    
- normalizedCropRect - 원본 이미지의 크롭된 버전을 요청하기 위해 CGRect를 지정. {0.0, 0.0}은 좌측 상단이고, {1.0, 1.0}은 우측하단이다. 만약 해당 프로퍼티를 지정한다면, 드시 resizeMode를 exact로 설정해야한다.
- isNetworkAccessAllowed - Photos가 iCloud에서 이미지를 다운로드 받을 수 있음을 설정함. true라면 local device에 이미지가 없을 때 iCloud에서 이미지를 다운받음. 다운로드 진행상황을 알기 위해서는 progressHandler 프로퍼티를 사용해 Photos가 이미지를 다운로드하는 동안 주기적으로 호출하는 block을 제공해야한다. false라면 이미지가 로컬 디바이스에 없을 경우 PHImageResultIsInCloudKey 값이 다운로드 받을 수 없음을 나타낸다.
- progressHandler - Photos가 이미지를 다운로드하는 동안 주기적으로 호출하는 block.

### PHVideoRequestOptions

- version - PHVideoRequestOptionsVersion타입
current : 비디오 asset의 최근 버전을 가져온다. 모든 변경들을 반영한. / original : adjustment가 없는 원본을 가져온다.
- deliveryMode - PHVideoRequestOptionsDeliveryMode 타입
automatic : 요청 및 현재 조건에 따라 제공할 비디오 데이터 품질을 자동으로 결정함 / highQualityFormat / mediumQulityFormat : 더 높은 품질의 버전이 로컬로 캐시되지 않는 한 중간 품질의 비디오를 제공한다. / fastFormat
- isNetworkAccessAllowed - PHImageRequestOptions와 동일