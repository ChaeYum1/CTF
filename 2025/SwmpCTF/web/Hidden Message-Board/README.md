# 🧃 SwampCTF 2025 Writeup - [Hidden Message-Board]

**Category:** Web  
**Points:** 50  
**Tags:** `CTF`, `Web`, `swampctf`

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

![Pasted image](./Pasted%20image%2020250403001512.png)

- 메시지 보드 페이지가 존재함
- React 기반 SPA 구조로 구성됨

---

## 🎯 The Goal

> 플래그를 얻기 위한 주요 목표 정리

- [x] 메시지 보드 구조 분석
- [x] React 내부 상태 확인 및 조작

---

## 💉 Exploit

### 🛠️ Step 1. 개발자 도구로 페이지 분석

![Pasted image](./Pasted%20image%2020250403001737.png)

- 소스에서 `<div id="flagstuff">` 와 `G1v3M3Th3Fl@g!!!!` 문자열 확인
- 조건에 맞는 `code` 값 설정 시 `getFlag()` 실행되는 구조

### 🛠️ Step 2. React 내부 로직 확인

```js
if (printFlagSetup.getAttribute("code") === "G1v3M3Th3Fl@g!!!!") {
  const flag = await getFlag();
  setFlagValue("[flag]: " + flag);
}
```

- React Virtual DOM 기반이므로 단순 DOM 수정은 무효
- textarea 입력 이벤트로 리렌더링 유도 필요

### 🛠️ Step 3. Exploit 스크립트 실행

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

👉 브라우저 개발자 도구 콘솔에 붙여 넣어 실행하면 flag가 화면에 출력됨 🎉

![Pasted image](./Pasted%20image%2020250403001855.png)

### 🔎 Unintended Solution

- `/static/js/bundle.js` 내부에서 flag 파일 경로 확인 가능
- 직접 접근: [`/static/media/flag.d93dd6a8616cc31d36db.txt`](http://chals.swampctf.com:43333/static/media/flag.d93dd6a8616cc31d36db.txt)

---

## 🍪 Info Leakage or Gain

- React의 Virtual DOM 구조는 DOM 조작만으로 상태 변경되지 않음
- 이벤트 트리거 방식으로 우회 가능
- 프론트 JS 코드에서 민감 로직이 노출된 사례

---

## 👑 Accessing the Flag

```
swampCTF{Cr0ss_S1t3_Scr1pt1ng_0r_XSS_c4n_ch4ng3_w3bs1t3s}
```

---

## 🧠 Conclusion

- React는 상태 기반이므로 단순 DOM 변경은 무시됨
- `setAttribute()`만으로는 무효 → **이벤트 기반 조작 필요**
- 실제 사용자 입력을 흉내내어 상태 변화 유도 가능
- 클라이언트 렌더링 기반에서도 flag 노출 가능성 존재

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_