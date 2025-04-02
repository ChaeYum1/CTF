# 🐊 SwampCTF 2025 Writeup - [Beginner Web]

**Category:** [Web]  
**Points:** [100]  
**Tags:** `CTF`, `[Web]`, `swampctf` , `Decryption` 

---
## 📚 Table of Contents
- [🔍 Recon](#-recon)
- [🎯 The Goal](#-the-goal)
- [💉 Exploit](#-exploit)
- [🍪 Info Leakage or Gain](#-info-leakage-or-gain)
- [👑 Accessing the Flag](#-accessing-the-flag)
- [🧠 Conclusion](#-conclusion)

---
## 🔍 Recon

![[Pasted image 20250403001111.png]]

- 문제 웹 페이지가 제공됨
- `part1`, `part2`, `part3` 으로 나뉘어 flag값을 조합하는 것으로 보임

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [ ] 소스코드 분석 후 정보 획득

---

## 💉 Exploit
### 🛠️ Step 1. part 1 페이지 구조 분석

```html
<!doctype html>
<html lang="en" data-beasties-container>
  <!--Part 1 of the flag: w3b_"-->
  <head>
    <meta charset="utf-8">
    <title>Is It Tuesday?</title>
    <base href="/">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
    <link rel="icon" type="image/x-icon" href="favicon.ico">
    <link rel="manifest" href="/site.webmanifest">
    <link rel="stylesheet" href="styles-5INURTSO.css">
  </head>
  <body>
    <app-root></app-root>
    <script src="polyfills-FFHMD2TL.js" type="module"></script>
    <script src="main-34VY7I6V.js" type="module"></script>
  </body>
  <!--<a href="https://www.flaticon.com/free-icons/tuesday" title="tuesday icons">Tuesday icons created by Rakib Hassan Rahim - Flaticon</a>-->
</html>
```

- part 1의 flag 값이 `w3b_` 확인
- `polyfills-FFHMD2TL.js`, `main-34VY7I6V.js` 페이지 분석 시도

### 🛠️ Step 2. `main-34VY7I6V.js` 페이지 분석

- `polyfills-FFHMD2TL.js`는 중요정보 존재하지 않음

![[Pasted image 20250403001221.png]]

소스코드 확인 결과
```js
let n = "flagPart2_3",
    r = "U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8",
    o = "U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU";
```

- `n = "flagPart2_3"` 이라는 키로 r, o가 암호화된 AES 문자열임 확인

### 🛠️ Step 3. 복호화 시도

```text
- `"U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8"` → `**br0w53r5_4r3_**`
    
- `"U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU"` → `**c0mpl1c473d**`
```

- 조합하면: `w3b_br0w53r5_4r3_c0mpl1c473d`
- 최종 flag: `swampCTF{w3b_br0w53r5_4r3_c0mpl1c473d}`

#### 🛠️ 사용한 도구
  - [ ] irame
  - [ ] curl
---

## 🍪 Info Leakage or Gain

- JS 내부의 AES 암호 문자열 → 키를 통해 복호화 가능
- 프론트엔드 빌드 결과물에서 민감 정보가 남는 경우 주의 필요

---

## 👑 Accessing the Flag

```
swampCTF{w3b_br0w53r5_4r3_c0mpl1c473d}
```

---

## 🧠 Conclusion

- `JS` 코드 내부에 포함된 민감 정보는 쉽게 노출될 수 있음
- 프론트 빌드 시 보안 키는 반드시 서버에서만 유지되어야 함
- `CryptoJS` 등 브라우저 복호화는 역분석에 매우 취약함

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_
