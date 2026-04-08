---
layout: post
title: "TypeError: Cannot read property 'map' of undefined 에러 분석 및 해결법"
date: 2026-04-07 18:31:00 +0900
categories: [PPT Auto-generation]
tags: [error, debugging, react]
author: Hayeon
description: "JavaScript/React에서 가장 흔한 에러 중 하나인 'Cannot read property map of undefined' 원인과 해결법을 알아봅니다."
---

## 에러 개요

```
TypeError: Cannot read property 'map' of undefined
```

JavaScript/React 개발 중 가장 흔하게 만나는 에러 중 하나입니다. `undefined` 값에 대해 `.map()` 메서드를 호출하려 할 때 발생합니다.

## 원인 분석

`.map()`은 배열(Array) 프로토타입 메서드입니다. 이 에러는 **배열이어야 할 변수가 `undefined` 상태**일 때 발생합니다.

### 대표적인 발생 상황

**1. API 응답 데이터가 아직 로드되지 않은 경우**

```javascript
const [users, setUsers] = useState(); // 초기값이 undefined

return (
  <ul>
    {users.map(user => <li>{user.name}</li>)}  {/* TypeError 발생! */}
  </ul>
);
```

**2. API 응답 구조가 예상과 다른 경우**

```javascript
// 예상: { data: [1, 2, 3] }
// 실제: { data: null } 또는 { result: [1, 2, 3] }

const items = response.data.items; // undefined
items.map(item => item.name);       // TypeError 발생!
```

**3. props가 전달되지 않은 경우**

```javascript
function UserList({ users }) {
  return users.map(user => <li>{user.name}</li>); // users가 undefined면 에러
}

// 부모 컴포넌트에서 users를 전달하지 않으면 발생
<UserList />
```

**4. 객체 내부의 중첩 배열 접근 시**

```javascript
const order = { id: 1 }; // items 프로퍼티가 없음
order.items.map(item => item.name); // TypeError 발생!
```

## 해결 방법

### 1. 초기값을 빈 배열로 설정

```javascript
const [users, setUsers] = useState([]); // 빈 배열로 초기화
```

### 2. 옵셔널 체이닝 (Optional Chaining) 사용

```javascript
users?.map(user => <li>{user.name}</li>)
```

`users`가 `undefined`이면 `.map()`을 호출하지 않고 `undefined`를 반환합니다.

### 3. 기본값 설정 (Default Parameter)

```javascript
function UserList({ users = [] }) {
  return users.map(user => <li>{user.name}</li>);
}
```

### 4. 조건부 렌더링

```javascript
{users && users.map(user => <li>{user.name}</li>)}
```

또는

```javascript
{Array.isArray(users) && users.map(user => <li>{user.name}</li>)}
```

### 5. API 응답 안전하게 처리

```javascript
const fetchUsers = async () => {
  const response = await fetch('/api/users');
  const data = await response.json();
  setUsers(data.users || []);  // fallback으로 빈 배열
};
```

## 디버깅 팁

1. **에러 스택 트레이스 확인**: `.map()`을 호출하는 정확한 위치를 파악합니다.
2. **`console.log`로 값 확인**: `.map()` 호출 직전에 해당 변수를 로그로 출력합니다.
3. **TypeScript 사용 고려**: 타입 시스템이 컴파일 타임에 `undefined` 가능성을 경고해줍니다.

## 핵심 정리

| 방법 | 적용 시점 |
|------|----------|
| `useState([])` | 상태 초기화 시 |
| `?.map()` | 간결한 null 체크가 필요할 때 |
| `Array.isArray()` | 타입을 엄격하게 확인할 때 |
| 기본 파라미터 `= []` | 함수/컴포넌트 props 정의 시 |
| `\|\| []` fallback | API 응답 처리 시 |

이 에러는 **방어적 코딩**으로 대부분 예방할 수 있습니다. 배열을 다룰 때는 항상 "이 값이 `undefined`일 수 있는가?"를 먼저 고려하는 습관을 들이면 좋습니다.
