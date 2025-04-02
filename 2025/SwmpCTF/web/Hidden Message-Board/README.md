# 🐊 SwampCTF 2025 Writeup - [Hidden Message-Board]

**Category:** [Web]  
**Points:** [50]  
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

![[Pasted image 20250403001512.png]]

- message-board 어딘가에 hidden flag 존재
- React 기반 메시지 보드 앱 확인됨

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [ ] 메시지 보드 구조 분석
- [ ] React 내부 상태 확인

---

## 💉 Exploit

### 🛠️Step 1. 개발자 도구로 페이지 분석

![[Pasted image 20250403001737.png]]

- `App.js` 내부에서 `G1v3M3Th3Fl@g!!!!` 문자열 확인됨
- `<div id="flagstuff">` 요소의 `code` 속성이 해당 값이면 flag를 출력함

### 🛠️Step 2. React 내부 로직 확인

```js
if (printFlagSetup.getAttribute("code") === "G1v3M3Th3Fl@g!!!!") {
  const flag = await getFlag();
  setFlagValue("[flag]: " + flag);
}
```
App() 컴포넌트 내부 분석 시 다음과 같은 로직 확인 가능
- id="flagstuff"인 `<div>`의 code 속성이 정확한 값이면 getFlag() 호출 후 flag값을 불러오고 setFlagValue()로 화면에 표시 가능

하지만 React는 DOM 변화만으로는 상태 업데이트 X
반드시 input 이벤트를 트리거해야 작동함
- `#flagstuff` 에 `code="G1v3M3Th3Fl@g!!!!"`를 추가
- textarea에 입력 이벤트를 강제로 발생시켜 React의 상태 감지를 트리거함
### 🛠️Step 3. Exploit 스크립트
```js
(function () {
  const FLAG_CODE = "G1v3M3Th3Fl@g!!!!";

  const flagDiv = document.getElementById("flagstuff");
  if (flagDiv) {
    flagDiv.setAttribute("code", FLAG_CODE);
    console.log("[+] code attribute set");
  } else {
    console.error("[-] flagstuff not found");
    return;
  }

  const textarea = document.querySelector("textarea");
  if (textarea) {
    const nativeInputValueSetter = Object.getOwnPropertyDescriptor(window.HTMLTextAreaElement.prototype, "value").set;
    nativeInputValueSetter.call(textarea, " ");

    const inputEvent = new Event("input", { bubbles: true });
    textarea.dispatchEvent(inputEvent);

    console.log("[+] triggered real input event");
  } else {
    console.error("[-] textarea not found");
  }
})();
```

**Exploit 스크립트**를 내부 브라우저 콘솔에서 실행
→ React 앱 내부에서 `getFlag()`호출
→ `Flag`획득 가능

![[Pasted image 20250403001855.png]]

### 🔎Unintended Solution

`http://chals.swampctf.com:43333/static/js/bundle.js` 에서 flag 경로 추출 가능
정적 경로: `http://chals.swampctf.com:43333/static/media/flag.d93dd6a8616cc31d36db.txt` URL로 직접 접근 시 flag 확인 가능능

---
## 🍪 Info Leakage or Gain

- React는 Virtual DOM을 사용하기 때문에 단순 DOM 조작으로는 감지되지 않음
- 이벤트 기반 상태 갱신 로직 파악이 중요
- 프론트 JS 분석만으로도 내부 로직 확인 가능

---

## 👑 Accessing the Flag

```
swampCTF{Cr0ss_S1t3_Scr1pt1ng_0r_XSS_c4n_ch4ng3_w3bs1t3s}
```

---

## 🧠 Conclusion

- React는 DOM 조작만으로는 상태 변화 X → **이벤트 조작 필요**
- `setAttribute()` → 무효
- `value 설정 + input 이벤트 트리거` → React가 감지하고 getFlag 실행

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_