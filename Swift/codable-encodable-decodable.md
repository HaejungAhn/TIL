2022-03-15

---
- Codable은 `Type Alias`다. (당연히 프로토콜인줄)

```swift
typealias Codable = Decodable & Encodable
```

- Encodable, Decodable 프로토콜로 이루어져 있다.
- encoding - 자기 자신을 JSON, plist와 같은 특정 포맷으로 나타내는 것.
    
    decoding - JSON, plist와 같은 특정 포맷을 자기자신의 본래 모습으로 나타내는 것.
    
- 네트워크 연결을 통해 데이터를 보내거나 디스크에 데이터를 저장하거나 API에 데이터를 보낼 때 encoding, decoding이 필요할 수 있다.
- 자동으로 Encode, Decode 하기
    - 가장 쉬운 방법은 이미 Codable을 채택한 타입을 프로퍼티에 사용하는 것이다. - String, Int, Double과 Foundation type인 Date, Data, URL이 포함된다.