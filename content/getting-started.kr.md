---
title: 'Getting Started'
draft: false
ShowToc: true
---

![bxd](/bxd.gif)

BoxDB는 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)를 위한 Promise 기반의 브라우저 ORM 입니다.

# 개념

[IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)는 비동기 방식으로 동작하고, 빠르고 강력합니다. 하지만 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 를 지원하지 않으며, IndexedDB를 사용하기 위해선 콜백 패턴을 사용해야만 합니다. 이는 레거시 코드처럼 느껴집니다.

이러한 이유로, BoxDB 프로젝트가 시작되었습니다.

그런데 왜 이름이 BoxDB 인가요?

- 박스는 가볍습니다.
- 박스에는 모양이 있습니다.
- 박스에는 무언가를 담을 수 있습니다.
- 박스는 쉽게 가져갈 수 있습니다.

이것이 BoxDB의 시작점이자, 핵심 개념입니다.

Box는 BoxDB의 핵심입니다. Box는 IndexedDB 내의 객체 저장소를 나타대는 추상화된 객체입니다.

Box는 다른 ORM에서 사용되는 `모델`과 동일한 개념입니다.

# 주요 기능

- Promise 기반의 ORM
- 사용자 친화적이고 사용하기 쉽습니다
- 가벼운(< 10kb) IndexedDB 래퍼
- 의존성 없음
- 데이터베이스와 객체 저장소 버전 관리
- 모델(Box)을 통한 데이터 검증과 트랜잭션 제어
- 트랜잭션으로 ACID(원자성, 일관성, 고립성, 지속성) 보장
- TypeScript 지원
- [웹 워커](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)에서 동작

# 브라우저 지원

| ![ie](https://user-images.githubusercontent.com/26512984/121935549-8292ca00-cd83-11eb-885c-9497bc78b104.png)</br>IE | ![edge](https://user-images.githubusercontent.com/26512984/121934559-64789a00-cd82-11eb-9238-4fc21eb835e2.png)</br>Edge | ![firefox](https://user-images.githubusercontent.com/26512984/121934551-62aed680-cd82-11eb-8a33-593af8b5fdbd.png)</br>Firefox | ![chrome](https://user-images.githubusercontent.com/26512984/121934545-604c7c80-cd82-11eb-884d-d9d8dad26e01.png)</br>Chrome | ![safari](https://user-images.githubusercontent.com/26512984/121934539-5dea2280-cd82-11eb-96ed-fbef553ec0e6.png)</br>Safari | ![ios-safari](https://user-images.githubusercontent.com/26512984/121934534-5c205f00-cd82-11eb-846b-cac169df47c7.png)</br>iOS Safari | ![samsung](https://user-images.githubusercontent.com/26512984/121934526-5aef3200-cd82-11eb-981d-835490f7b1b2.png)</br>Samsung | ![opera](https://user-images.githubusercontent.com/26512984/121934519-59256e80-cd82-11eb-9b11-4805c7dd0ba1.png)</br>Opera |
| ------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| 11                                                                                                                  | 12~                                                                                                                     | 10~                                                                                                                           | 23~                                                                                                                         | 10~                                                                                                                         | 10~                                                                                                                                 | 4~                                                                                                                            | 15~                                                                                                                       |

- 여러분의 브라우저에서 기능을 테스트 해보세요. [link](/demo.html).
- `IE11` 테스트 확인해보기 [here](/ie).

# 설치

BoxDB는 [npm](https://www.npmjs.com/bxd) (혹은 [yarn](https://yarnpkg.com/package/bxd))에서 설치 가능합니다.

```bash
yarn add bxd
# 또는
npm install bxd
```

# 기본

## 데이터베이스 준비

만약 데이터베이스가 존재하지 않다면, 새로운 데이터베이스를 생성합니다.

```typescript
import BoxDB from 'bxd';

const db = new BoxDB('database-name', 1); // 데이터베이스 이름, 버전
```

## Box 생성

```typescript
// User Box (객체 저장소 이름: user)
const User = db.create('user', {
  _id: {
    type: BoxDB.Types.STRING,
    key: true,
  },
  name: {
    type: BoxDB.Types.STRING,
    index: true,
  },
  age: BoxDB.Types.NUMBER,
});

// Item Box (객체 저장소 이름: item)
const Item = db.create('item', {
  uid: {
    type: BoxDB.Types.STRING,
    index: true,
  },
  name: {
    type: BoxDB.Types.STRING,
    index: true,
  },
  memo: BoxDB.Types.STRING,
}, {
  autoIncrement: true,
});
```

## 데이터베이스 버전 업데이트

만약 데이터베이스 버전을 변경하고 싶다면(예: Box 스키마 변경, 새로운 Box 추가 등), 이전 버전보다 큰 숫자로 변경하면 됩니다.

```typescript
// 이전 버전
// const db = new BoxDB('database-name', 1);

const db = new BoxDB('database-name', 2); // 버전 업데이트
```

## 데이터베이스 열기

```typescript
// 박스 정의
// ...

await db.open();

// 여기부터 Box를 사용할 수 있습니다! (데이터 접근 및 제어)
```

## 데이터베이스 닫기

주로 특별한 상황에서만 닫습니다. (예: 동일한 데이터베이스를 다시 열어야 할 경우)

```typescript
db.close();
```

## 트랜잭션

### 단일 레코드 추가

```typescript
await User.add({ _id: '0', name: 'EMPTY', age: 0 });
await User.add({ _id: '1', name: 'leegeunhyeok', age: 20 });
```

### 다중 레코드 추가

```typescript
const items = [
  { uid: '0', name: 'unknown 1', memo: '' },
  { uid: '0', name: 'unknown 2', memo: '' },
  { uid: '1', name: 'mac', memo: 'so expensive' },
  { uid: '1', name: 'phone', memo: '' },
  { uid: '1', name: 'desktop', memo: '' },
];

// 비동기 함수 내에서
for (let i = 0; i < items.length; i++) {
  await Item.add(items[i]);
}
```

### 레코드 가져오기

```typescript
await User.get('1'); // { _id: '1', name: 'leegeunhyeok', age: 20 }
await User.get('999'); // null
```

### 레코드 갱신하기

```typescript
await User.put({ _id: '0', name: 'temp' }); // `user._id = '0'` 인 레코드의 `name` 이 'temp' 로 변경됩니다.
```

### 레코드 삭제하기

```typescript
await User.delete('0'); // `user._id = '0'` 레코드 삭제
```

### 커서로 모든 레코드 가져오기

```typescript
await Item.find().get();

// [
//   { uid: '0', name: 'unknown 1', memo: '' },
//   { uid: '0', name: 'unknown 2', memo: '' },
//   { uid: '1', name: 'mac', memo: 'so expensive' },
//   { uid: '1', name: 'phone', memo: '' },
//   { uid: '1', name: 'desktop', memo: '' }
// ]
```

### 커서로 레코드 가져오기 (인덱스 사용)

```typescript
await Item.find({ index: 'name', value: 'phone' }).get();

// [{ uid: '1', name: 'phone', memo: '' }]
```

### 커서로 레코드 가져오기 (필터 함수 사용)

```typescript
await Item.find(
  null,
  (item) => parseInt(item.uid) % 2 === 1,
  (item) => item.memo.includes('expensive'),
).get();

// [{ uid: '1', name: 'mac', memo: 'so expensive' }]
```

### 커서로 레코드 가져오기 (둘 다 사용)

```typescript
await Item.find(
  {
    index: 'uid',
    value: BoxDB.Range.equal('1'),
  },
  (item) => !!item.memo,
  (item) => item.memo.includes('exp'),
).get();

// [{ uid: '1', name: 'mac', memo: 'so expensive' }]
```

### 커서로 다중 레코드 갱신하기

```typescript
await Item.find(null, (item) => !!item.memo).update({ memo: 'new memo' });

// 전: { uid: '1', name: 'mac', memo: 'so expensive' }
// 후: { uid: '1', name: 'mac', memo: 'new memo' }
```

### 커서로 다중 레코드 삭제하기

```typescript
await Item.find(null, (item) => item.uid === '0').delete();

// `Item.uid = '0'` 인 레코드 모두 삭제
```

### 한 트랜잭션에서 다중 작업 처리하기

만약 트랜잭션 작업 중 에러가 발생할 경우, 트랜잭션 이전으로 롤백됩니다.

> Transactionable 한 메소드 이름에는 `$` 접두사가 붙어있습니다. ($add, $put, $delete, $find)

```typescript
await db.transaction(
  User.$add({ _id: '3', name: 'Aiden', age: 16 }),
  Item.$add({ uid: '3', name: 'pencil', memo: 'super sharp' }),
  Item.$add({ uid: '3', name: 'eraser', memo: '' }),
  Item.$add({ uid: '3', name: 'book', memo: '200 pages' }),
  Item.$add({ uid: '3', name: 'juice', memo: '' }),
);

// 새로운 user 추가: { _id: '3', name: 'Aiden', age: 16 }
// 새로운 item 추가: { uid: '3', name: 'pencil', memo: 'super sharp' }
// 새로운 item 추가: { uid: '3', name: 'eraser', memo: '' }
// 새로운 item 추가: { uid: '3', name: 'book', memo: '200 pages' }
// 새로운 item 추가: { uid: '3', name: 'juice', memo: '' }
```

### 레코드 수 가져오기

```typescript
await Item.count(); // 7
```

### 모든 레코드 지우기

```typescript
// A. 커서로 지우기
await User.find().delete();

// B. clear()로 지우기
await User.clear();
```

## 웹 워커

```typescript
importScripts('https://cdn.jsdelivr.net/npm/bxd@latest/dist/bxd.min.js');

// 모든 기능 사용 가능!
self.BoxDB;
```
