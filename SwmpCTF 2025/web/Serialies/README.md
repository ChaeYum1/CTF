# 🐊 SwampCTF 2025 Writeup - [문제 이름]

**Category:** [Web / Pwn / Crypto / ...]  
**Points:** [ex. 214]  
**Tags:** `CTF`, `[Web|Pwn|... 특정 키워드들]`, `swampctf`  

---

## 🔍 Recon

간단한 설명 or 스크린샷 요약

- 문제에서 제공된 링크나 인터페이스 설명
- 눈에 띄는 기능 / 동작 정리
- HTML 주석이나 JS 파일에서 본 단서 등

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [ ] 시스템 정보 수집
- [ ] 취약점 유도 / 우회
- [ ] 세션 탈취 / 플래그 출력

---

## 💉 Exploit

### 🛠️ 방법

어떤 취약점이 있었고 어떻게 유도했는지 설명:

- 사용한 페이로드 예시:

```html
<script>alert(1)</script>
```

- 사용한 툴/사이트:
  - [ ] Burp Suite
  - [ ] curl
  - [ ] artoo.love
  - [ ] custom JS

---

## 🍪 Info Leakage or Gain

- 어떤 정보/세션/쿠키 등을 탈취했는지
- 관리자 권한을 얻는 데 사용한 방식

---

## 👑 Accessing the Flag

플래그에 접근한 방식 (ex. 관리자 페이지 진입, 쿠키 내 포함 등)

```
swampCTF{REPLACE_WITH_ACTUAL_FLAG}
```

---

## 🧠 Conclusion

배운 점, 보안 상 주의할 점, 이 취약점의 영향 요약:

- Stored XSS는 여전히 위험하다
- 입력 필터링은 철저히!
- 관리자 봇이 방문하는 구조 = 위험한 UX

---

_📚 Writeup by [@yourhandle](https://github.com/yourhandle)_
