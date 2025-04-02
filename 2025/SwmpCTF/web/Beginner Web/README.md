
# 💻 SwampCTF 2025 Writeup - [Beginner Web]

**Category:** [Web]  
**Points:** [100]  
**Tags:** `CTF`, `[Web]`, `swampctf`, `Decryption` 

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

![문제 웹](./Pasted%20image%2020250403001111.png)

- 문제 웹 페이지가 제공됨
- `part1`, `part2`, `part3` 으로 나뉘어 flag값을 조합하는 것으로 보임

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [x] 소스코드 분석 후 정보 획득

---

## 💉 Exploit

### 🛠️ Step 1. part 1 페이지 구조 분석

```html
<!--Part 1 of the flag: w3b_"-->
...
<script src="polyfills-FFHMD2TL.js" type="module"></script>
<script src="main-34VY7I6V.js" type="module"></script>
```

- part 1의 flag 값이 `w3b_`로 명시됨
- JavaScript 번들 파일 분석 진행

---

### 🛠️ Step 2. `main-34VY7I6V.js` 코드 분석

![JS 파일](./Pasted%20image%2020250403001221.png)

```js
let n = "flagPart2_3",
    r = "U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8",
    o = "U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU";
```

- `n = "flagPart2_3"` 라는 키를 사용해 `r`, `o` 를 복호화 가능

---

### 🛠️ Step 3. 복호화 시도

```txt
U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8 → br0w53r5_4r3_
U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU → c0mpl1c473d
```

- 전체 조합: `w3b_br0w53r5_4r3_c0mpl1c473d`
- 최종 flag:  
```txt
swampCTF{w3b_br0w53r5_4r3_c0mpl1c473d}
```

---

#### 🛠️ 사용한 도구
- [x] VS Code
- [x] CryptoJS Decrypt

---

## 🍪 Info Leakage or Gain

- 프론트엔드 JavaScript에 AES 키/데이터 포함됨 → 누구나 복호화 가능
- 빌드 결과물 내 민감 정보 노출 주의 필요

---

## 👑 Accessing the Flag

```
swampCTF{w3b_br0w53r5_4r3_c0mpl1c473d}
```

---

## 🧠 Conclusion

- JS에 키와 데이터를 함께 넣으면 매우 쉽게 역분석 가능함
- 브라우저 기반 복호화는 공격자에게도 쉬운 접근을 허용
- 민감 데이터는 절대 프론트에 포함시키지 말 것

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_
