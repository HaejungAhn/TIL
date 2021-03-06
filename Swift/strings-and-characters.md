*โ ๐ย [[Swift Language Guide] Strings and Characters](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html)*

### String

- Swift์ String์ **Character ํ์์ ์ปฌ๋ ์**์ด๋ค.

- **๋น ๋ฅธ ์ ๋์ฝ๋ ํธํ๋ฐฉ๋ฒ์ ์ ๊ณต**ํ๋ค.

- \0์ null๋ฌธ์๋ฅผ ๋ํ๋ธ๋ค.

   \u{n}์ ์ ๋์ฝ๋ ์ค์นผ๋ผ ๊ฐ์ ํํํ๋ค. ์ฌ๊ธฐ์ n์ 1 - 8์๋ฆฌ์ 16์ง์์.

- ๋ฌธ์์ด์ ๊ฐํ์์ด๋ค. ๋ฐ๋ผ์ ํ ๋น๋๊ฑฐ๋ ์ ๋ฌ๋  ๋ ๋ณต์ฌ๊ฐ ๋ฐ์ํ๋ค.(**COW ์ต์ ํ ์ ์ฉ**๋จ)

- String์ Character ๋ฐฐ์ด๋ก ๋ง๋ค ๋ `Array(string)`

  Character๋ฅผ String์ผ๋ก ๋ง๋ค ๋ `String(character array)`

- ๋ฌธ์์ด ์๋ค๋ก #์ ๋ฃ์ผ๋ฉด ํด๋น ๋ฌธ์์ด ์ ์ฒด๋ฅผ String์ผ๋ก ์ธ์ํ๋ค. (๋ด๋ถ์ ์ผ๋ก ๋ฌธ์์ด ๋ณด๊ฐ `\()`์ด ์๋ค ํ๋๋ผ๋ ์ด๋ฅผ ๋ฌธ์ ๊ทธ๋๋ก ์ธ์ํจ) ๋ง์ฝ ํ์ฅ๋ ๊ตฌ๋ถ๊ธฐํธ(#)๋ฅผ ์ฌ์ฉํ๋ ๋ฌธ์์ด ์์์ ๋ฌธ์์ด ๋ณด๊ฐ์ ์ฌ์ฉํ๊ณ  ์ถ๋ค๋ฉด 
`\#()`์ ์ฌ์ฉํ๋ฉด ๋จ.

### ์ ๋์ฝ๋

- ์ ๋์ฝ๋(unicode)

> *โ ๐ย [์ ๋์ฝ๋๋ ๋ฌด์์ผ๊น? (UTF8๊ณผ EUC-KR ๋น๊ต)](https://norux.me/31)*

์ ๋์ฝ๋๋, **์ซ์์ ๊ธ์, ์ฆ ํค์ ๊ฐ์ด 1:1๋ก ๋งคํ๋ ํํ์ย ์ฝ๋**ย ์ธ๊ฒ์ด๋ค.
๋ค์๋งํด ์์คํค์ฝ๋๋ก 0x41 = A ๋ก ๋งคํ๋ ๊ฒ ์ฒ๋ผ, ์์คํค์ฝ๋๋ก ํํํ  ์ ์๋ ๋ฌธ์๋ค์ ์ ๋์ฝ๋๋ผ๋ ์ด๋ฆ ์๋ ์  ์ธ๊ณ์ ๋ชจ๋  ๋ฌธ์๋ฅผย ํน์  ์ซ์(ํค)์ 1:1๋ก ๋งคํํ ๊ฒ์ด๋ค.

UTF-8, UTF-16 ์ด๋ฐ๊ฒ๋ค์ ๋ชจ๋ ์ธ์ฝ๋ฉ ๋ฐฉ์์ ์๋ฏธํ๋ค. ์ซ์์ ๋งคํ๋ ๊ฒ๋ค์ ์ด๋ป๊ฒ ํํํ  ๊ฒ์ด๋์ ๋ํ ๊ฒ.
> 

- Swift์ ๊ธฐ๋ณธ Stringํ์์ โ์ ๋์ฝ๋ ์ค์นผ๋ผ ๊ฐ(๋ฌธ์๋ฅผ ์ํ ์ ๋ํฌํ 21-bit ์ซ์ ๋๋ modifier)"์ผ๋ก๋ถํฐ ์์ฑ๋๋ค.

- Character ํ์์ ๋ชจ๋  ์ธ์คํด์ค๋ ํ์ฅ๋ ์์ ํด๋ฌ์คํฐ(๋ฌด๋ฆฌ)๋ก ํํ๋๋ค. ์ฆ, **ํ๊ฐ ์ด์์ ์์๊ฐ ๋ชจ์ฌ์ ํ๋์ character๋ฅผ ๊ตฌ์ฑํ๋ค.**

  ์์ : ๋ฌธ์์ฒด๊ณ์์ ์๋ฏธํ ๊ตฌ๋ณํ  ์ ์๋ ๊ฐ์ฅ ์์ ๋จ์

  ex: `รฉ` ๋ ๋๊ฐ์ง ๋ฐฉ์์ผ๋ก ํํ ๊ฐ๋ฅํจ.

โ  U+00E9 (๋จ์ผ ์์ ํ๊ฐ)

โก U+0065์ U+0301 ( ์์๋ฌธ์ e์ ์ก์ผํธ๋ฅผ ๋ํ๋ด๋ โ๋ฅผ ํฉ์ฑ)

  ex: ํ๊ธ์ ํ๊ธฐ๋ ์์ ๊ฐ์ด ๋๊ฐ์ง๋ก ํํ ๊ฐ๋ฅํจ.

โ  U+D55C (โํ" ์ด๋ผ๋ ๋จ์ผ ์์ ํ๊ฐ)

โก U+112์ U+1161์ U+11AB ( โใ", โใ", โใดโ์ด๋ผ๋ ์ฌ๋ฌ ์์์ ํฉ์ฑ)

- **Swift์์ Character๋ ์ฌ๋ฌ๊ฐ์ unicode scalar๋ก ๊ตฌ์ฑํ  ์ ์๊ธฐ ๋๋ฌธ์ ๋ฌธ์ ํ๋ํ๋๋ง๋ค ์ฐจ์งํ๋ ๋ฉ๋ชจ๋ฆฌ ์์ด ๋ค๋ฅผ ์ ์๋ค.**

- ๋ฐ๋ผ์ ๋ฌธ์์ด์ count๋ฅผ ๊ณ์ฐํ  ๋๋ ์ํ๋ฅผ ํ๋ฉด์ ํ๋ํ๋ ๋ฌธ์๋ฅผ ์ธ์ํด์ผํ๋ค.

### ๋ฌธ์์ด ์ ๊ทผ๊ณผ ์์ 

- ๋ฉ์๋, ํ๋กํผํฐ, ์๋ธ์คํฌ๋ฆฝํธ๋ก ๋ฌธ์์ด์ ์ ๊ทผ, ์์ ํ  ์ ์๋ค.

- **๋ฌธ์์ด ์ธ๋ฑ์ค**

String์ ๊ฐ Character ์์น์ ํด๋นํ๋ `String.Index`๋ผ๋ ์ธ๋ฑ์ค ํ์์ ๊ฐ์ง๊ณ  ์๋ค.

๋ฌธ์๋ง๋ค ์ ์ฅํ  ๋ฉ๋ชจ๋ฆฌ์ ์์ด ๋ค๋ฅผ ์ ์์ผ๋ฏ๋ก ํน์  ์์น์ ์๋ Character๋ฅผ ํ์ธํ๊ธฐ ์ํด์๋ ํด๋น String์ ์์ ๋๋ ๋์์ ๊ฐ๊ฐ์ ์ ๋์ฝ๋ ์ค์นผ๋ผ๋ฅผ ๋ฐ๋ณตํ๋ฉฐ ํ์ธํด์ผ ํ๋ค. ๋ฐ๋ผ์ Swift ๋ฌธ์์ด์ ์ ์๊ฐ์ผ๋ก ์ธ๋ฑ์ค๋ฅผ ์์ฑํ  ์ ์๋ค.

- **ํ๋กํผํฐ**

`startIndex` ํ๋กํผํฐ๋ String์ ๊ฐ์ฅ ์ฒซ๋ฒ์งธ Character์ ์ ๊ทผํ  ์ ์๋ค.

`endIndex` ํ๋กํผํฐ๋ String์ ๊ฐ์ฅ ๋ง์ง๋ง Character ๋ฐ๋ก ๋ค์ ์์น๋ฅผ ๋ํ๋ธ๋ค.

๋ฌธ์์ด์ด ๋น์ด์๋ค๋ฉด `startIndex` == `endIndex`

`indices` for-in ๋ฌธ์์ ํ์ฉ ๊ฐ๋ฅํ๋ฉฐ ๊ฐ๋ณ character์ ์ ๊ทผ ๊ฐ๋ฅํจ.

- **๋ฉ์๋**

String์ `index(before:)`์ `index(after:)`๋ฅผ ์ฌ์ฉํ๋ฉด ์ฃผ์ด์ง ์ธ๋ฑ์ค์ ์, ๋ค์ ์ ๊ทผํ  ์ ์๋ค.

String์ RandomAccessCollection ํ๋กํ ์ฝ์ ์ฑํํ์ง ์์๋ค. BidirectionalCollection ํ๋กํ ์ฝ๋ง ์ฑํํ๊ธฐ ๋๋ฌธ์ ์ฃผ์ด์ง ์์๋ฅผ ๊ธฐ์ค์ผ๋ก ์,๋ค๋ง ์ ๊ทผํ  ์ ์๋ค. ๋ฐ๋ผ์ ๋ฉ๋ฆฌ ๋จ์ด์ง Character์ ์ ๊ทผํด์ผ ํ๋ค๋ฉด ์ํ๋ฅผ ๋๋ฉด์ ํ์ธ์ด ํ์ํ๋ค. ์ฃผ์ด์ง ์ธ๋ฑ์ค์์ ๊ฑฐ๋ฆฌ๊ฐ ๋จผ ์ธ๋ฑ์ค์ ์ ๊ทผํ๊ณ  ์ถ๋ค๋ฉด `index(_:offsetBy:)`๋ฅผ ์ด์ฉํด๋ผ

```swift
func index(_ i: String.Index, offsetBy n: String.IndexDistance) -> String.Index
-- i : String์ ์ ํฉํ ์ธ๋ฑ์ค
-- n : i์์ ๊ฑฐ๋ฆฌ, i ๋ค์๋ถํฐ ์์ํด์ n๋ฒ์งธ์ ์์นํ character๊ฐ ๋ฆฌํด๋จ.

let s = "Swift"
let i = s.index(s.startIndex, offsetBy: 4)
print(s[i])
// Prints "t"

Complexity: O(n), where n is the absolute value of n.
```

- **์๋ธ์คํฌ๋ฆฝํธ**

String์ Character์ ์ ๊ทผํ๊ธฐ ์ํด ์๋ธ์คํฌ๋ฆฝํธ ๊ตฌ๋ฌธ์ ์ฌ์ฉํ  ์ ์๊ณ , ์ฌ๊ธฐ์๋ ์์์ ์ธ๊ธํ ๋ฌธ์์ด ์ธ๋ฑ์ค, ํ๋กํผํฐ, ๋ฉ์๋ ๋ฑ์ ํ์ฉํ  ์ ์๋ค.

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u

// String.Index ํ์์ ๋ง๋ค์ด์ ์๋ธ์คํฌ๋ฆฝํธ๋ก ํ์ฉ
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

### ๋ถ๋ถ ๋ฌธ์์ด(Substrings)

์๋ธ์คํฌ๋ฆฝํธ๋ prefix(_:) - ์ฒซ๋ฒ์งธ character ํฌํจ-, suffix(_:) - ๋ง์ง๋ง character ํฌํจ-์ ๊ฐ์ ๋ฉ์๋๋ฅผ ์ฌ์ฉํด ๋ถ๋ถ ๋ฌธ์์ด(๋ฌธ์ ๊ฐ์๊ฐ ์ฌ๋ฌ๊ฐ์ผ๋)์ ์ป์์ ๋ ๊ทธ ๋ฌธ์์ด์ ํ์์ String์ด ์๋ `Substring` ํ์์ด๋ค. String๊ณผ ๊ฑฐ์ ๋๋ถ๋ถ์ ๊ฒ์ด ๋น์ทํ๋ค. ์ฐจ์ด๊ฐ ์๋ค๋ฉด ์ฑ๋ฅ์ต์ ํ๋ฅผ ์ํด **substring์ด ์๋ ๋ฌธ์์ด์ ์ ์ฅ๊ณต๊ฐ ๋๋ ๋ค๋ฅธ substring์ ์ ์ฅํ๋๋ฐ ์ฌ์ฉ๋๋ ๋ฉ๋ชจ๋ฆฌ์ ์ผ๋ถ๋ฅผ ์ฌ์ฌ์ฉ**ํ๋ค๋ ๊ฒ์ด๋ค. ( ๋ฌธ์์ด๋ COW๋ก ์ต์ ํํ๋ฏ๋ก ๋น์ทํ๊ธด ํ์ง๋ง ๋ ๋ฌธ์์ด์ด ๊ฐ์ ๋ฉ๋ชจ๋ฆฌ ๊ณต๊ฐ์ ๊ณต์ ํ๋ฉด ์๋ก ๊ฐ์ด ๊ฐ์ ๊ฒ๊ณผ๋ ์ฐจ์ด๊ฐ ์๋ค )

substring์ ๋ชฉ์ ์ ์์๋ก ๋ฌธ์์ด์ ์ ์ฅํ๋ ๊ฒ์ ์๋ค. ์ด๊ฑธ ๊ณ์ ์ฌ์ฉํ๋ฉด ์๋ณธ ๋ฌธ์์ด์ ๊ณ์ ๊ฐ์ง๊ณ  ์์ด์ผ ํ๊ธฐ ๋๋ฌธ์ ์ฅ๊ธฐ ๋ชฉ์ ์ผ๋ก ์ฌ์ฉํ๋ ๊ฒ์ ๋ชฉ์ ์ ๋ง์ง ์๋ค.

### ๋ฌธ์์ด์ ๋๋ฑ๋น๊ต

์ฌ๋ฌ ์ ๋์ฝ๋ ์ค์นผ๋ผ๋ก ๊ตฌ์ฑ๋์ด ์๋ค ํ๋๋ผ๋ ์๋ฏธ์ ์ผ๋ก ๋์ผํ๋ฉด ๊ฐ๋ค๊ณ  ๋ณธ๋ค.

hasPrefix(:)๋ฅผ ์ฌ์ฉํ๋ฉด ํน์  ๋ฌธ์์ด๋ก ์์๋๋์ง ์๋์ง๋ฅผ ์ฒดํฌํ  ์ ์๋ค. haSuffix(_:)๋ฅผ ์ฌ์ฉํ๋ฉด ํน์  ๋ฌธ์๋ก ๋๋๋์ง ์ฒดํฌ ๊ฐ๋ฅ.

### ๋ฌธ์์ด์ ์ ๋์ฝ๋ ํํ

Swift์ String, Character๋ ์ฌ๋ฌ ์ธ์ฝ๋ฉ ํ์์ ์์ฃผ ์ฝ๊ฒ ์ ๊ณตํ๋ค.

```swift
let dogString = "Dogโผ๐ถ"

let utf8 = dogString.utf8
let utf16 = dogString.utf16
let unicodeScalar = dogString.unicodeScalars // UInt32 ๊ฐ ์์ ํํ๋ ์ค์นผ๋ผ์ 21-bit ๋ฆฌํด
```

---

## **Accessing String Elements**

*โ ๐ย [[Apple Document] String](https://developer.apple.com/documentation/swift/string)*

*โ ๐ย [[Apple Document] Character](https://developer.apple.com/documentation/swift/character)*

- String์ Character ํ์์ด ๋ชจ์ฌ์ง ์ปฌ๋ ์์ด๋ค.
- โ๊น", โโจโ ๋ฑ๊ณผ ๊ฐ์ ๊ฐ **๋ฌธ์๋ค์ ์ฌ์ค** **์ฌ๋ฌ๊ฐ์ unicode scalar ๊ฐ์ผ๋ก ์ด๋ฃจ์ด์ง ๊ฒ**์ด๋ค.

> Because each character in a string can be made up of one or more Unicode scalar values, the number of characters in a string may not match the length of the Unicode scalar value representation or the length of the string in a particular binary representation.

string์ ์ฌ๋ฌ๊ฐ์ character๋ก ์ด๋ฃจ์ด์ ธ์๊ธฐ ๋๋ฌธ์ string์ ๋ฌธ์์ด ๊ธธ์ด๋ ์ ๋์ฝ๋ ์ค์นผ๋ผ ๊ฐ์ ๊ธธ์ด ๋๋ ํน์  ๋ฐ์ด๋๋ฆฌ๋ก ํํ๋ string์ ๊ธธ์ด์ ์ผ์นํ์ง ์์ ๊ฒ์ด๋ค.
> 

```swift
let greeting = "Hello! ๐ฅ"
print("Length: \(greeting.count)")
// Prints "Length: 8"

print("Unicode scalar value count: \(greeting.unicodeScalars.count)")
// Prints "Unicode scalar value count: 8"

print("UTF-8 representation count: \(greeting.utf8.count)")
// Prints "UTF-8 representation count: 11"

// ์ฐ๋ฆฌ์๊ฒ ํ๊ฐ๋ก ์ด๋ฃจ์ด์ง ๋ฌธ์์ด์ character๋ก ๊ตฌ์ฑํ๋ฉด ์ฌ๋ฌ๊ฐ๊ฐ ๋  ์ ์๋ค.
let usFlag: Character = "\u{1F1FA}\u{1F1F8}"
print(usFlag)
// Prints "๐บ๐ธ"
```

### **Substring ํ์**

์๋ `firstName` ์ ํ์์ Substring ํ์์ด๋ค. 

substring ํ์์ **์๋ณธ ๋ฌธ์์ด์ ์ ์ฅ๊ณต๊ฐ์ ๊ณต์ **ํ๋ฉด์ ๋ฌธ์์ด์ ์ผ๋ถ๋ถ์ ํ์ํ๋ ํ์์ด๋ค.

```swift
let name = "Marie Curie"

let firstSpace = name.firstIndex(of: " ") ?? name.endIndex 
let firstName = name[..<firstSpace]
// firstName == "Marie"
```

์๋ ๊ทผ๋ฐ ๋ด๊ฐ ์ดํด๊ฐ ์๊ฐ๋๊ฑด!

String์ด Character์ ๋ชจ์์ด๋ผ๊ณ  ํ์์. ๊ทธ๋ผ ๋ฐฐ์ด ํํ์ผ๊ฑฐ์๋์ผ. ๋ฐฐ์ดํํ๋ฉด 0๋ฒ์งธ ์ธ๋ฑ์ค, 1๋ฒ์งธ ์ธ๋ฑ์ค ์ด๋ ๊ฒ ์ ๊ทผํ  ์ ์์ง์๋?

์๋ ์์ ๋ฅผ ๋ณด๋ฉด ๊ทธ๊ฒ ์๋ผ๋ ์ด์ ๋ฅผ ๋ชํํ ์ ์ ์์.

์ถ๋ ฅ๋ cafe ์์์ ์นด์ดํธ๋ฅผ ์ถ๋ ฅํด๋ณด๋ฉด ๊ธธ์ด๋ 9๋ผ๋ ๊ฑธ ์ฝ๊ฒ ์ ์ ์์.

```swift
let cafe = "Cafe\u{301} du ๐"
print(cafe)
// Prints "Cafรฉ du ๐"
print(cafe.count)
// Prints "9"
print(Array(cafe))
// Prints "["C", "a", "f", "รฉ", " ", "d", "u", " ", "๐"]"
```

String์ ์ ๋์ฝ๋ ์ค์นผ๋ผ์ ๋ชจ์์ด๋ผ๊ณ  ํ์์. ๊ทธ๋ผ ์ ๋์ฝ๋ ์ค์นผ๋ผ๊ฐ ์ด๋ป๊ฒ ๋์ค๋์ง ๋ณด๋๋ก ํ์.

๋ฌธ์์ count์ ๋ฌ๋ฆฌ unicode ์ค์นผ๋ผ์ ์ค์  ๊ฐ์๋ 10๊ฐ์.

```swift
print(cafe.unicodeScalars.count)
// Prints "10"
print(Array(cafe.unicodeScalars))
// Prints "["C", "a", "f", "e", "\u{0301}", " ", "d", "u", " ", "\u{0001F30D}"]"
```

UTF-16์ผ๋ก ์ธ์ฝ๋ฉํ์๋(NSString์ผ๋ก ๋ฌธ์์ด์  ํํํ์ ๋ UTF-16๊ณผ ๋์ผํ๊ฒ ๋์จ๋ค.)

```swift
print(cafe.utf16.count)
// Prints "11"
print(Array(cafe.utf16))
// Prints "[67, 97, 102, 101, 769, 32, 100, 117, 32, 55356, 57101]"

let nscafe = cafe as NSString
print(nscafe.length)
// Prints "11"
print(nscafe.character(at: 3))
// Prints "101"
```

UTF-8์ผ๋ก ์ธ์ฝ๋ฉํ์๋(C API๋ฅผ ์ด์ฉํ์ ๋ UTF-8๋ก ์ธ์ฝ๋ฉ๋จ)

```swift
print(cafe.utf8.count)
// Prints "14"
print(Array(cafe.utf8))
// Prints "[67, 97, 102, 101, 204, 129, 32, 100, 117, 32, 240, 159, 140, 141]"

let cLength = strlen(cafe)
print(cLength)
// Prints "14"
```

์ด๋ค ๋ฐฉ์์ผ๋ก ์ธ์ฝ๋ฉํ๋(์ด๋ค API๋ฅผ ์ฌ์ฉํ๋๋์ ๋ฐ๋ผ ๋ค๋ฆ)์ ๋ฐ๋ผ ๊ฐ์ ๋ฌธ์๋๋ผ๋ ์ปฌ๋ ์์ ๊ฐ์๊ฐ ๋ฌ๋ผ์ง๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. ๊ทธ๋ ๊ธฐ ๋๋ฌธ์ ๋จ์ํ ๋ฐฐ์ด์ ์ธ๋ฑ์ค๋ฅผ ์ด์ฉํด ์ ๊ทผํ  ์๊ฐ ์๋๊ฑฐ์.

๋ฌธ์์ด์ ๊ธธ์ด๋ฅผ ์ธก์ ํ๊ธฐ ์ํด ์ ์ผ ๋จผ์  ํด์ผํ  ๊ฒ์ ๋ชฉ์ ์ด ๋ฌด์์ธ์ง๋ฅผ ์ ํ๋ ๊ฒ์ด๋ค.

๋จ์ง ํ๋ฉด์ ๋์คํ๋ ์ด๋๋ ๊ธธ์ด๋ฅผ ์๊ณ ์ถ์๊ฑด์ง ํน์ ํน์  ์ธ์ฝ๋ฉ์ ์ฌ์ฉํ์ ๋ ๋ฌธ์์ด์ด ์ฌ์ฉํ  ์ ์ฅ๊ณต๊ฐ์ ์์ ์ธก์ ํ๊ณ  ์ถ์๊ฒ์ธ์ง๋ฅผ ๊ฒฐ์ ํด์ผํ๋ค.

๋์คํ๋ ์ด ์์ผ๋ก๋ ๋จ ํ๊ฐ์ ๋ฌธ์์ผ์ง๋ผ๋ ์ด๋ค ์ธ์ฝ๋ฉ์ ํ๋๋์ ๋ฐ๋ผ ๊ทธ ๊ธธ์ด๊ฐ ๋ฌ๋ผ์ง ์ ์๊ธฐ ๋๋ฌธ์ด๋ค. 

๋ง์ฝ โAโ๋ผ๋ ๋ฌธ์์ ๊ธธ์ด๋ฅผ ๋ณด์๋ฉด, ํด๋น ๋ฌธ์์ ์ ๋์ฝ๋ ์ค์นผ๋ผ ๊ฐ์ 65์ธ๋ฐ ์ด๋ UTF-8์์๋, UTF-16์์๋ ๋จ ํ๋์ ๊ณต๊ฐ๋ง์ผ๋ก ์ถฉ๋ถํ ํํํ  ์ ์๋ ๋ฒ์์ด๊ธฐ ๋๋ฌธ์ ๊ธธ์ด๋ 1์ด ๋๋ค.

ํ์ง๋ง \u{1F1F5}์ \u{1F1F7}๋ก ๊ตฌ์ฑ๋ emoji flag๋ฅผ ํํํ๋ ค๊ณ  ํ๋ค๋ฉด UTF-16 ์ด๋ UTF-8 ๋ฑ ํ๊ฐ๋ง์ผ๋ก๋ ํํํ  ์ ์๋ค. ๋๋ฌด ํฌ๊ธฐ๋๋ฌธ์. ๋ฐ๋ผ์ ๊ฐ ์ธ์ฝ๋ฉ ๋ฐฉ์์ผ๋ก ํด๋น ๋ฌธ์์ ๊ธธ์ด๋ฅผ ํํํด๋ณด๋ฉด ์๋์ ๊ฐ์ด ์ธ์ฝ๋ฉ๋ณ๋ก ์์ดํ ๊ฑธ ์ ์  ์๋ค.

```swift
let flag = "๐ต๐ท"
print(flag.count)
// Prints "1"
print(flag.unicodeScalars.count)
// Prints "2"
print(flag.utf16.count)
// Prints "4"
print(flag.utf8.count)
// Prints "8"
```

์ด๋ฐ ๊ณผ์ ์ผ๋ก ์ ์ถํด๋ณผ ๋ string์ count ํ๋กํผํฐ๋ ๋ด๋ถ์ ์ผ๋ก string์ ์ํํ๋ฉด์ count๋ฅผ ๊ตฌํ๊ฒ ๋๋ ๊ฑธ ์ ์ ์๋ค. 

**Accessing String View Elements**

๋ฌธ์์ด์ ๊ฐ๋ณ์์๋ฅผ ์ฐพ๊ธฐ ์ํด์ , ๋ชฉ์ ์ ๋ง๋ ์ ํฉํ view๋ฅผ ์ ํํด์ผํ๋ค.(์ฌ๊ธฐ์ view๋ ์ธ์ฝ๋ฉ ๋ฐฉ์์ ์๋ฏธํ๋ ๊ฒ ๊ฐ๋ค)

String์ COW ์ต์ ํ๊ฐ ์ ์ฉ๋จ. ๋ง์ฝ string๊ณผ ์ธ์ ํ ์ ์ฅ์๊ฐ ๋ชจ๋ ์ฐจ๋ฉด, ์๋ก์ด ๋ฒํผ๊ฐ ํ ๋น๋๊ณ  ๊ธฐ์กด ๋ฐ์ดํฐ๋ค์ด ์๋ก์ด ์ ์ฅ์๋ก ์ด๋ํ๊ฒ ๋๋ค. string buffer๋ Array๋ ๋น์ทํ๊ฒ ๊ธฐํ๊ธ์์ ์ผ๋ก ์ ์ฅ์๊ฐ ๋์ด๋๋ ์ ๋ต์ ์ทจํ๋ค.

โ *๐ย [[velog, @hansangjin96] Swift) String์ ์ subscript[Int]๋ก ์ ๊ทผ์ด ์๋ ๊น?](https://velog.io/@hansangjin96/Swift-String%EC%9D%80-%EC%99%9C-subscriptInt%EB%A1%9C-%EC%A0%91%EA%B7%BC%EC%9D%B4-%EC%95%88%EB%90%A0%EA%B9%8C)*

String์ ๊ฐ์ฅ ์,๋ค ์์๋ง ์ฐธ์กฐํ  ์ ์๋ BidirectionalCollection ํ๋กํ ์ฝ์ ์ค์ํ๋ค. (์์ผ๊น?)

๋ฐ๋ผ์ String.count์ ์๊ฐ๋ณต์ก๋๋ O(n)์ด ๋๋ค. ๋ํ String.Index๋ +, - ์ฐ์ฐ์ ์ง์ํ์ง ์๊ธฐ๋๋ฌธ์ ํ์ฌ ์ธ๋ฑ์ค์์ n๋งํผ ๋จ์ด์ง ๊ธ์๋ฅผ ์ฐธ์กฐํ๊ธฐ ์ํด์๋ O(n)์ ์๊ฐ๋ณต์ก๋๊ฐ ํ์ํจ. ๋ฐ๋ผ์ ์๊ณ ๋ฆฌ์ฆ ๋ฌธ์ ๋ฅผ ํ ๋์ ๊ฐ์ ๊ฒฝ์ฐ Array(๋ฌธ์์ด)์ ํตํด ๋ช์์ ์ผ๋ก [Character] ํํ๋ก ๋ฐํํ๋ ๊ฒ์ด ํจ์จ์ ์ด๋ค.

โ Swift language guide

๋ฌธ์๋ง๋ค ์ ์ฅํ  ๋ฉ๋ชจ๋ฆฌ ์์ด ๋ค๋ฅผ ์ ์์ผ๋ฏ๋ก ํน์  ์์น์ ์๋ `Character` ๋ฅผ ํ์ธํ๋ ค๋ฉด ํด๋น `String` ์ ์์ ๋๋ ๋์์ ๊ฐ ์ ๋์ฝ๋ ์ค์นผ๋ผ๋ฅผ ๋ฐ๋ณตํด์ผ ํฉ๋๋ค. ์ด๋ฌํ ์ด์ ๋ก Swift ๋ฌธ์์ด์ ์ ์๊ฐ์ผ๋ก ์ธ๋ฑ์ค๋ฅผ ์์ฑํ  ์ ์์ต๋๋ค.

string์ endIndex๋ ๋ฌธ์์ด์ โpast the endโ ์์น๋ค. ์ฆ, ๊ฐ์ฅ ๋ง์ง๋ง ์ธ๋ฑ์ค๋ณด๋ค ํ๋ ํฐ ์์น์.

---

[https://jcsoohwancho.github.io/2019-11-19-Swift-String-ํจ์จ์ ์ผ๋ก-์ฐ๊ธฐ/](https://jcsoohwancho.github.io/2019-11-19-Swift-String-%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9C%BC%EB%A1%9C-%EC%93%B0%EA%B8%B0/)

String์ Character๋ก ์ด๋ฃจ์ด์ง ์ปฌ๋ ์์ด๋ค. 

Swift์์ string์ ๊ธฐ๋ณธ์ ์ผ๋ก ๋ค์ํ ๋ทฐ๋ฅผ ์ ๊ณตํ๋ค. ์ฌ๊ธฐ์ view๋ ์ธ์ฝ๋ฉ ๋ฐฉ์์ ์๋ฏธํจ. ๋ฐ๋ผ์ ์ ์ํ์ผ๋ก ์ ๊ทผํ  ๋ ์ด๋ค view๋ฅผ ๊ธฐ์ค์ผ๋ก ์ ๊ทผํด์ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ ธ์ฌ์ง๊ฐ ์ ๋งคํ๊ธฐ ๋๋ฌธ์ ์ ์ํ ์ธ๋ฑ์ค ์ฐธ์กฐ X