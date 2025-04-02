# 🐊 SwampCTF 2025 Writeup - [Editor]

**Category:** [Web]  
**Points:** [150]  
**Tags:** `CTF`, `[Web]`, `swampctf` , `refer 우회` 

---

## 📚 Table of Contents
- [🔍 Recon](#-recon)
- [🎯 The Goal](#-the-goal)
- [💉 Exploit](#-exploit)
  - [🧪 방법 1: HTML 태그를 통한 직접 출력](#-방법-1-html-태그를-통한-직접-출력)
  - [🧪 방법 2: Referer 헤더 우회](#-방법-2-referer-헤더-우회)
- [🍪 Info Leakage or Gain](#-info-leakage-or-gain)
- [👑 Accessing the Flag](#-accessing-the-flag)
- [🧠 Conclusion](#-conclusion)

---

## 🔍 Recon

![Pasted image 20250403001300](./Pasted%20image%2020250403001300.png)

- 문제 웹 페이지와 소스코드 제공됨
- `http://chals.swampctf.com:47821/flag.txt` 에 접근해 플래그를 획득하는 것이 목표로 보임

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [x] HTML 기반 렌더링 우회 탐색
- [x] Referer 기반 요청 우회 시도

---

## 💉 Exploit

### 🧪 방법 1: HTML 태그를 통한 직접 출력

#### 🛠️ Step 1. 페이지 구조 분석

![Pasted image 20250403001330](./Pasted%20image%2020250403001330.png)

- `HTML/CSS` 미리보기 페이지로 구성됨
- 사용자가 입력한 `HTML`을 렌더링하여 바로 보여줌

#### 🛠️ Step 2. Payload 삽입

- `JavaScript`는 차단되어 있으나 `HTML` 태그는 허용됨
- `iframe` 또는 `object` 등을 활용하여 서버 내부 파일 요청 가능
- 사용한 Payload:

```html
<iframe src="/flag.txt"></iframe>
```

📸 ![Pasted image 20250403001401](./Pasted%20image%2020250403001401.png)

👉 내부 요청으로 서버 파일 내용을 직접 확인 가능

---

### 🧪 방법 2: Referer 헤더 우회

#### 🛠️ Step 1. curl로 Referer 조작

```bash
curl http://chals.swampctf.com:47821/flag.txt -H "Referer: http://chals.swampctf.com:47821/"
```

#### 🛠️ Step 2. 결과 확인

📸 ![Pasted image 20250403001448](./Pasted%20image%2020250403001448.png)

👉 `Referer` 값을 동일 출처로 설정하여 `curl`로도 플래그에 접근 성공 🎯

---

#### 🛠️ 사용한 도구
- [x] iframe
- [x] curl

---

## 🍪 Info Leakage or Gain

- JS가 막혀 있어도 HTML 안의 `iframe`, `object`, `embed` 등을 통해 요청 가능
- `Referer Header`를 조작하면 서버 보안 검증을 우회할 수 있음

---

## 👑 Accessing the Flag

```
swampCTF{c55_qu3r135_n07_j5}
```

---

## 🧠 Conclusion

- `HTML` 미리보기 기능만으로도 민감한 파일이 노출될 수 있음
- `Referer` 기반 검증은 쉽게 우회 가능하므로 신뢰할 수 없음
- `JS`를 차단해도 `iframe`이나 `object`로 충분히 공격 가능

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_
