2022-03-02

---
## dSYM

> *โ[๐ Apple document: building your app to include debugging infromation](https://developer.apple.com/documentation/xcode/building-your-app-to-include-debugging-information)*
Xcode๊ฐ **์์ค์ฝ๋๋ฅผ ๋จธ์ ์ฝ๋๋ก ์ปดํ์ผํ  ๋ ์ฑ ์์ ์๋ symbol ๋ฆฌ์คํธ๋ฅผ ์์ฑ**ํ๋ค. **symbol์๋ ํด๋์ค์ด๋ฆ, ์ ์ญ๋ณ์, ๋ฉ์๋์ ํจ์ ์ด๋ฆ ๋ฑ์ด ํฌํจ**๋๋ค. โ๋๋ฒ๊ทธ ๋น๋โ๋ ์ปดํ์ผ๋ ๋ฐ์ด๋๋ฆฌ ํ์ผ ์์ ๋๋ฒ๊ทธ ์ฌ๋ณผ์ ํฌํจ์ํจ๋ค. ์ด์ ๋ฌ๋ฆฌ โ๋ฆด๋ฆฌ์ฆ ๋น๋"๋ ๋ฐฐํฌํ  ์ฑ์ ์ฌ์ด์ฆ๋ฅผ ์ค์ด๊ธฐ ์ํด `dSYM`(debug symbol file)ํ์ผ์ ๋๋ฒ๊ทธ ์ฌ๋ณผ์ ์์น์ํจ๋ค.
์ด symbol์ ํฌํจํ๊ณ  ์๋ ์ ๋ณด๋ค์ด ์ ์๋ ํ์ผ ๋ฐ ์ค๋ฒํธ์ ํด๋นํ๋ค. ์ด๋ฐ ์ฐ๊ด๊ด๊ณ๊ฐ debug symbol์ ๋ง๋ค๊ณ , Xcode์์ ๋๋ฒ๊ฑฐ๋ฅผ ์ฌ์ฉํ๊ฑฐ๋ crash report์์ ์ค๋ฒํธ๋ฅผ ํฌํจํ๊ฒ๋ ๋ง๋ ๋ค.
> 

*โ ๐ย [[medium] How crashlytics works in iOS? An Overview of dSYM](https://medium.com/naukri-engineering/overview-of-dsym-crashlytics-in-ios-dfd72eae8b58)*

์ฑ์ด ํฌ๋์๋๋ฉด OS๋ ์ฑ์ด ํฌ๋์๋  ๋ ์ด๋ค ์์์ ํ๋์ง์ ๋ํ ์ง๋จ ์ ๋ณด๋ฅผ ๋ชจ์๋ค. ์ง๋จ ์ ๋ณด ์ค ๊ฐ์ฅ ์ค์ํ ํํธ๊ฐ thread backtraces์ธ๋ฐ 16์ง์ ์ฃผ์๋ก ํ์๋๋ค. (unsymbolicated crash report)

**SYMBOLICATION์ด๋?**

์ฑ ํฌ๋์์ ๊ทผ๋ณธ ์์ธ์ ํ์ํ๊ธฐ ์ํด thread backtrace๋ฅผ ํด์ํด์ผํจ! ์ฌ๋์ด ์ฝ์ ์ ์๋ ์ธ์ด๋ก. ์ด ๊ณผ์ ์ symbolication์ด๋ผ๊ณ  ํ๋ค.

**Role of dSYM(Debug SYMbol)**

dSYM์ด ์์ผ๋ฉด ํฌ๋์ ๋ฆฌํฌํธ๋ ๋จ์ง ๊ฐ์ฒด์ ๋ฉ์๋์ ๋ฉ๋ชจ๋ฆฌ ์ฃผ์๋ง์ ๋ณด์ฌ์ค ๋ฟ์ด๋ค. ๋ฐ์ด๋๋ฆฌ ํ์ผ์ symbolication ํ์ง ์์ผ๋ฉด ํฌ๋์ ๋ฆฌํฌํธ๋ฅผ ์ฝ์ ์ ์๋ค.

dSYMํ์ผ์ ๋๋ฒ๊ทธ ์ฌ๋ณผ์ ์ ์ฅํ๋๋ฐ stack-trace๋ฅผ ์ฝ์ ์ ์๋ ํ์์ผ๋ก ๋์ฝ๋ฉํ๊ธฐ ์ํ ๋งคํ์ ๋ณด๊ฐ ํฌํจ๋์ด ์๋ค.

dSYM ํ์ผ์ ๋ชฉ์ ์ ์ถฉ๋ ๋ก๊ทธ์ ์ฌ๋ณผ์ ํน์  ๋ฉ์๋์ ์ด๋ฆ์ผ๋ก ๊ต์ฒดํ์ฌ ์ฝ๊ธฐ ์ฝ๊ณ  ๋๋ฒ๊น์ ๋์์ด ๋๋๋ก ํ๋ ๊ฒ์ด๋ค.

**Key points regarding dSYM**

dSYMํ์ผ์ Debugging Information Format์ด โ`DWARF with dSYM file`โ๋ก ์ค์ ๋ ์ํ์์ ์ฑ์ ์์ค์ฝ๋๊ฐ ์ปดํ์ผ๋  ๋ ๋ง๋ค์ด์ง๋ค.

dSYM ํ์ผ์ ์ฝ๋๊ฐ ๋ณ๊ฒฝ๋ ์ฑ์ ์ปดํ์ผํ  ๋๋ง๋ค ๋ณ๊ฒฝ๋๋ค.

dSYM ํ์ผ์ ํน์  Xcode build์ ์ฐ๊ฒฐ๋์ด ์๋ UUID๋ฅผ ๊ฐ์ง๊ณ  ์๋ค. ๋ฐ๋ผ์ ํฌ๋์๊ฐ ๋ฐ์ํ ๋ฐ์ด๋๋ฆฌ์ UUID๊ฐ Symbolication์ ์ฌ์ฉ๋๋ dSYM์ UUID์ ์ผ์นํ๋ ๊ฒฝ์ฐ์๋ง Symbolication์ด ์๋ํ๋ค.

**How to enable dSYM?**

1. Xcode โ Project Navigator 

2. ํ๊ฒ ๋ฆฌ์คํธ์์ ์ฑ์ด ๋น๋๋๋ ํ๊ฒ์ ์ ํํ๋ค.

3. build setting ํญ์ ์ ํํ๋ค.

4. Build Option ์ค์ ์์ โDebugging Information Formatโ์ `DWARF with dSYM file` ๋ก ์ค์ ํ๋ค.

๊ทผ๋ฐ ์ด๋ ๊ฒ ํญ์ ์ค์ ํด๋๋ฉด ์ปดํ์ผ ์๊ฐ์ด ๊ธธ์ด์ง๋ฏ๋ก ํ์ํ์ง ์์๋๋ DWARF๋ก ์ค์ ํ๋๊ฒ ์ข๋ค.