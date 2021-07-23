---
title: 'API Reference'
draft: false
ShowToc: true
TocOpen: true
---

> 모든 기능들은 IndexedDB 의 매커니즘을 따릅니다. ([w3c](https://w3c.github.io/IndexedDB), [MDN](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API))

## BoxDB

> IndexedDB 와 Object store 를 관리하기 위한 클래스입니다.

```javascript
const db = new BoxDB(databaseName, version);
```

Parameters

- databaseName: `string`
  - 데이터베이스 이름
- version: `number`
  - 열고자 하는 데이터베이스 버전

Properties

- [BoxDB.Types](#boxdbtypes) `static`
- [BoxDB.Range](#boxdbrange) `static`
- [BoxDB.Order](#boxdborder) `static`
- databaseName: `string`
  - 데이터베이스 이름
- version: `number`
  - 데이터베이스 버전
- ready: `boolean`
  - 데이터베이스 준비 상태

Methods

- [BoxDB.interrupt](#boxdbinterrupt) `static`
- [box()](#box)
- [open()](#boxdbopen)
- [transaction()](#boxdbtransaction)

### BoxDB.Types

> `BoxDB.Types` 은 데이터 타입의 집합입니다.

```javascript
BoxDB.Types;

// Properties
BoxDB.Types.BOOLEAN;
BoxDB.Types.NUMBER;
BoxDB.Types.STRING;
BoxDB.Types.DATE;
BoxDB.Types.ARRAY;
BoxDB.Types.OBJECT;
BoxDB.Types.REGEXP;
BoxDB.Types.FILE;
BoxDB.Types.BLOB;
BoxDB.Types.ANY;
```

Properties

- BoxDB.Types.BOOLEAN: [Boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean) 값을 사용하기 위한 타입
- BoxDB.Types.NUMBER: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number) 값을 사용하기 위한 타입
- BoxDB.Types.STRING: [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) 값을 사용하기 위한 타입
- BoxDB.Types.DATE: [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) 값을 사용하기 위한 타입
- BoxDB.Types.ARRAY: [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) 값을 사용하기 위한 타입
- BoxDB.Types.OBJECT: [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) 값을 사용하기 위한 타입
- BoxDB.Types.REGEXP: [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 값을 사용하기 위한 타입
- BoxDB.Types.FILE: [File](https://developer.mozilla.org/en-US/docs/Web/API/File) 값을 사용하기 위한 타입
- BoxDB.Types.BLOB: [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) 값을 사용하기 위한 타입
- BoxDB.Types.ANY: 모든 타입의 값을 사용하기 위한 타입 (타입 체크를 건너뜁니다)

### BoxDB.Range

> `BoxDB.Range` 는 [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)의 집합입니다.

```javascript
// Properties
BoxDB.Range.equal(x);
BoxDB.Range.upper(x[, open]);
BoxDB.Range.lower(x[, open]);
BoxDB.Range.bound(x, y[, lowerOpen, upperOpen]);
```

Methods

- BoxDB.Range.equal: 단일 값을 포함하는 새 키 범위를 반환합니다. (`A = B` 조건)
  - [IDBKeyRange.only](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/only)를 사용합니다.
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.upper: 새로운 상한키 범위를 반환합니다. (`A > B` 조건)
  - [IDBKeyRange.upperBound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/upperBound)를 사용합니다.
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.lower: 새로운 하한키 범위를 반환합니다. (`A < B` 조건)
  - [IDBKeyRange.lowerBound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/lowerBound)를 사용합니다.
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.bound: 지정된 상한 및 하한 범위를 가진 키를 반환합니다. (`A < N < B` 조건)
  - [IDBKeyRange.bound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/bound)를 사용합니다.
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)

### BoxDB.Order

> `BoxDB.Order` 는 [IDBCursor.direction](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor/direction)의 집합입니다.

```javascript
BoxDB.Order;

BoxDB.Order.ASC; // 'next'
BoxDB.Order.ASC_UNIQUE; // 'nextunique'
BoxDB.Order.DESC; // 'prev'
BoxDB.Order.DESC_UNIQUE; // 'prevunique'
```

- BoxDB.Order.ASC: [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) 에서 사용하는 `next` direction 입니다.
- BoxDB.Order.ASC_UNIQUE: [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) 에서 사용하는 `nextunique` direction 입니다.
- BoxDB.Order.DESC: [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) 에서 사용하는 `prev` direction 입니다.
- BoxDB.Order.DESC_UNIQUE: [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) 에서 사용하는 `prevunique` direction 입니다.

### BoxDB.interrupt()

> BoxDB 인터페이스의 `interrupt()` 메서드는 트랜잭션을 중단하기 위한 [TransactionTask](#transactiontask)를 반환합니다.

```javascript
const abortTask = BoxDB.interrupt(); // TransactionTask

// Usage
db.transaction(Task_1, Task_2, abortTask, Task_3);
```

### BoxDB.box()

> BoxDB 인터페이스의 `box()` 메서드는 새로운 [Box](#box)를 반환합니다.

```javascript
db.box(name, schema[, options]);
```

Parameters

- storeName: `string`
  - 객체 저장소의 이름
- schema: [BoxSchema](#boxschema)
  - 데이터를 저장하기 위한 데이터 스키마
- options: [BoxOption](#boxoption) (`선택`)
  - 객체 저장소 옵션

Return value

- [Box](#box)

### BoxDB.open()

> BoxDB 인터페이스의 `open()` 메서드는 IndexedDB 를 열고 생성된 박스들을 기반으로 객체 저장소를 생성/갱신/삭제합니다.

```javascript
await db.open();
```

Return value

- Promise<[Event](https://developer.mozilla.org/en-us/docs/Web/API/Event)>

### BoxDB.transaction()

> `BoxDB.transaction()` 메서드는 [TransactionTask](#transactiontask)로 이루어진 리스트를 받아, 하나의 트랜잭션 내에서 순차적으로 작업을 수행합니다.

> 중요한 점은, 트랜잭션 작업 내에서 에러가 발생할 경우 이전 상태로 롤백됩니다. (원자성 보장)

```javascript
// ACID guaranteed
db.transaction(
  task_1,
  task_2,
  task_3,
  ...,
  task_n
);
```

Parameters

- task: ...[TransactionTask](#transactiontask)[]
  - 트랜잭션 작업들

Return value

- Promise<`void`>

## BoxSchema

> `BoxSchema` 는 데이터 모델 객체입니다. 필드명과 데이터 타입, 그리고 상세한 옵션(키, 인덱스)이 포함됩니다.

```typescript
interface BoxSchema {
  [field: string]: ConfiguredType | DataType;
}

// BoxDB.Types
export enum DataType {
  BOOLEAN = '1',
  NUMBER = '2',
  STRING = '3',
  DATE = '4',
  ARRAY = '5',
  OBJECT = '6',
  REGEXP = '7',
  FILE = '8',
  BLOB = '9',
  ANY = '0',
}

type ConfiguredType = {
  type: DataType;
  key?: boolean;
  index?: boolean;
  unique?: boolean;
};
```

```javascript
// Example
const schema = {
  // 방법 1. 상세하게 필드 정의하기
  name: {
    type: BoxDB.Types.STRING,
    index: true,
    unique: true,
  },
  // 방법 2. 타입만 정의하기 (key 없음, index 없음)
  age: BoxDB.Types.NUMBER,
};
```

Options

- type: [BoxDB.Types](#boxdbtypes)
  - 해당 필드의 타입 (타입 체크시 사용됩니다)
- key: `boolean` (`선택`)
  - 활성화 할 경우 해당 필드를 [in-line 키](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key)로 지정합니다.
  - 인라인 키는 객체 저장소당 하나만 설정 가능합니다.
    - _만약 인라인 키를 변경하고 싶을 경우, 박스를 삭제하고 다시 생성해야합니다_ (`force` 옵션을 통해 새로 생성할 수 있습니다)
- index: `boolean` (`선택`)
  - 해당 필드의 인덱스를 새로 [생성](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/createIndex) 하거나 [삭제](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/deleteIndex)합니다.
  - 만약 인덱스를 통해 해당 필드의 값을 검색하고 싶다면, 반드시 `true` 로 설정해야합니다.
- unique: `boolean` (`선택`)
  - 해당 필드의 인덱스에 [고유 제약조건](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/createIndex#parameters)을 추가합니다.
    - 반드시 `index` 옵션과 함께 사용해야합니다.

## BoxOption

> 박스 생성 옵션

```typescript
interface BoxOption {
  autoIncrement?: boolean;
  force?: boolean;
}
```

- autoIncrement: `boolean` (기본값: `false`)
  - 객체 저장소에 [out-of-line 키](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_outofline_key)를 추가합니다.
  - [autoIncrement](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/autoIncrement) 옵션
- force: `boolean` (기본값: `false`)
  - [versionchange](https://developer.mozilla.org/en-US/docs/Web/API/IDBDatabase/versionchange_event) 이벤트가 발생했을 때, 객체 저장소를 강제로 업데이트합니다.
  - 주의: 이 옵션은 객체 저장소를 생성하기 전에 기존 객체 저장소를 삭제합니다.

## BoxData

> [BoxSchema](#boxschema) 기반의 데이터

```typescript
// 샘플 box
const User = db.box('user', {
  _id: {
    type: BoxDB.Types.NUMBER,
    key: true,
  }
  name: {
    type: BoxDB.Types.STRING,
    index: true,
  }
  age: BoxDB.Types.NUMBER,
  email: {
    type: BoxDB.Types.STRING,
    index: true,
    unique: true
  }
});

// BoxData 는 아래와 같이 추론됩니다.
type BoxData = {
  _id: number
  name: string,
  age: number,
  email: string
}
```

### OptionalBoxData

```typescript
type OptionalBoxData<S extends Schema> = Partial<BoxData<S>>;
```

## BoxRange

> `BoxRange` 는 값 혹은 [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)로 조회할 때 사용되는 객체입니다.

```javascript
// age = 10 (`age` 필드는 반드시 index 로 정의되어있어야 합니다)
const range_1 = {
  value: 10,
  index: 'age',
};

// in-line-key(_id) < 5
const range_2 = {
  value: BoxDB.Range.lower(5),
};

Box.find(range_1);
Box.find(range_2);
```

Properties

- value: [BoxDB.Range](#boxdbrange) 혹은 [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) 값
- index: `string` (`선택`)
  - 스키마에서 인덱스된 필드 이름 (`index: true` 인 필드)
  - 만약 인덱스 필드 이름을 전달하지 않으면, 기본적으로 [in-line 키](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key)를 따릅니다.

## FilterFunction

> `FilterFunction` 는 각 레코드를 평가하는 함수입니다. 이 함수는 [BoxData](#boxdata)를 받고, 불린 값을 반환합니다.

```javascript
const predicate_1 = (data) => data.age === 10;
const predicate_2 = (data) => !data.name === 'UNKNOWN';
const predicate_3 = (data) => true;

Box.find(null, predicate_1, predicate_2, predicate_3);
```

## Box

> `Box` 는 [객체 저장소](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore)를 나타내는 추상화입니다. 그리고 정의된 형태(스키마)의 값을 생성할 수 있습니다.

```javascript
const User = db.box('user', {
  _id: {
    type: BoxDB.Types.NUMBER,
    key: true,
  }
  name: {
    type: BoxDB.Types.STRING,
    index: true,
  }
  age: BoxDB.Types.NUMBER,
  email: BoxDB.Types.STRING
});

// (1) 빈 값 생성
const u1 = new User();
u1.name // string

// (2) 초기 값으로 생성
const u2 = new User({
  _id: 0,
  name: 'Jack',
  age: 12
});

// 단일 트랜잭션 작업을 위한 메서드들
User.get(key);
User.put(value, key);
User.delete(key);
User.count();
User.clear();
User.find(range, ...predicate).get(order, limit);
User.find(range, ...predicate).update(updateValue);
User.find(range, ...predicate).delete();

// BoxDB.transaction()에서 사용되는 TransactionTask 반환
User.$get(key);
User.$put(key, value);
User.$delete(key);
User.$find(range, ...predicate).update(updateValue);
User.$find(range, ...predicate).delete();
```

Parameters

- storeName: `string`
  - 객체 저장소 이름
- schema: [BoxSchema](#boxschema)
  - 데이터를 저장하기 위한 데이터 스키마
- options: [BoxOption](#boxoption) (`선택`)
  - 객체 저장소 옵션

Methods

- Box.getName(): 객체 저장소 이름을 반환합니다.
- Box.getVersion(): 데이터베이스 버전을 반환합니다.
- Box.add(value, key): 객체 저장소에 레코드를 추가합니다.
- Box.get(key): 객체 저장소에서 레코드를 가져옵니다.
- Box.put(value[, key]): 객체 저장소에 해당 레코드가 존재하지 않을 경우 추가하거나, 갱신합니다.
- Box.delete(key): 객체 저장소에서 레코드를 삭제합니다.
- Box.count(key): 모든 레코드 수를 반환합니다.
- Box.clear(): 객체 저장소를 삭제합니다.
- Box.find(range, ...predicate): `BoxCursorHandler` 를 반환합니다. [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)로 트랜잭션을 처리합니다.
  - find().get(): 레코드들을 가져옵니다.
  - find().update(updateValue): 레코드들을 갱신합니다.
  - find().delete(): 레코드들을 삭제합니다.
- [Box.$add()](#box$add): 레코드를 추가하기 위한 [TransactionTask](#transactiontask)를 반환합니다.
- [Box.$put()](#box$put): 레코드를 갱신하기 위한 [TransactionTask](#transactiontask)를 반환합니다.
- [Box.$delete()](#box$delete): 레코드를 삭제하기 위한 [TransactionTask](#transactiontask)를 반환합니다.
- [Box.$find()](#box$find): [TransactionCursorHandler](#transactioncursorhandler)를 반환합니다.

### Box.getName()

> `getName()` 메서드는 현재 객체 저장소의 이름을 반환합니다.

```javascript
Box.getName();
```

Return value

- string

### Box.getVersion()

> `getVersion()` 메서드는 데이터베이스의 버전을 반환합니다.

```javascript
Box.getVersion();
```

Return value

- number

### Box.add()

> Box 인터페이스의 `add()` 메서드는 지정된 객체 저장소에 데이터를 저장합니다.

```javascript
Box.add(value[, key]);
Box.add({ id: 1, name: 'Tom', age: 15 });
```

Parameters

- value: [BoxData](#boxdata)
  - 저장할 데이터
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`선택`)
  - 레코드를 식별하기 위한 키 값 (default: `null`)
  - `autoIncrement` 옵션과 함께 박스를 정의한 경우 사용

Return value

- Promise<[IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)>
  - 추가된 레코드의 키 (in/out-of-line 키 모두)

### Box.get()

> Box 인터페이스의 `get()` 메서드는 객체 저장소에서 지정한 데이터를 반환합니다.

```javascript
Box.get(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)
  - 검색할 레코드를 식별하는 키 혹은 키 범위

Return value

- Promise<[BoxData](#boxdata)>

### Box.put()

> Box 인터페이스의 `put()` 메서드는 이미 데이터베이스에 존재하는 레코드를 갱신하거나, 존재하지 않는 경우 새로운 레코드를 추가합니다.

```javascript
Box.put(value[, key]);
```

Parameters

- value: [BoxData](#boxdata)
  - 갱신하고자 하는 데이터
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`선택`)
  - 갱신하고자 하는 레코드를 식별하는 키
  - `autoIncrement` 옵션과 함께 박스를 정의한 경우 사용

Return value

- Promise<`void`>

### Box.delete()

> Box 인터페이스의 `delete()` 메서드는 지정한 레코드를 삭제합니다.

```javascript
Box.delete(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)
  - 삭제하고자 하는 레코드를 식별하는 키

Return value

- Promise<`void`>

## Box.find()

> Box 인터페이스의 `find()` 메서드는 [BoxRange](#boxrange)와 제공된 [FilterFunction](#filterfunction)에 의해 필터링된 모든 레코드를 선택합니다.

Parameters

- range: [BoxRange](#boxrange) (`선택`)
- pridicate: ...[FilterFunction](#filterfunction) (`선택`)

Return value

- [BoxCursorHandler](#boxcursorhandler)

### Box.count()

> Box 인터페이스의 `count()` 메서드는 객체 저장소 내의 모든 레코드 수를 반환합니다.

```javascript
Box.count();
```

Return value

- Promise<`number`>

### Box.clear()

> Box 인터페이스의 `clear()` 메서드는 객체 저장소 내의 모든 데이터를 삭제합니다.

```javascript
Box.clear();
```

Return value

- Promise<`void`>

### Box.$add()

> Box 인터페이스의 `$add()` 메서드는 레코드를 추가하기 위한 [TransactionTask](#transactiontask)를 반환합니다.

> `$` 접두사가 붙은 메서드들은 [TransactionTask](#transactiontask)를 반환합니다.

```javascript
Box.$add(value[, key]);
Box.$add({ id: 2, name: 'Carl', age: 12 });
```

Parameters

- value: [BoxData](#boxdata)
  - 저장할 데이터
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`선택`)
  - 레코드를 식별하기 위한 키 값 (기본값: `null`)
  - `autoIncrement` 옵션과 함께 박스를 정의한 경우 사용

Return value

- [TransactionTask](#transactiontask)

### Box.$put()

> Box 인터페이스의 `$put()` 메서드는 레코드를 갱신하거나, 삽입하는 [TransactionTask](#transactiontask)를 반환합니다.

```javascript
Box.$put(value[, key]);
```

Parameters

- value: [BoxData](#boxdata)
  - The data you wish to update.
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`선택`)
  - 갱신하고자 하는 레코드를 식별하는 키 (기본값: `null`)
  - `autoIncrement` 옵션과 함께 박스를 정의한 경우 사용

Return value

- [TransactionTask](#transactiontask)

### Box.$delete()

> Box 인터페이스의 `$delete()` 메서드는 레코드를 삭제하는 [TransactionTask](#transactiontask)를 반환합니다.

```javascript
Box.$delete(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`선택`)
  - 삭제하고자 하는 레코드를 식별하는 키
  - `autoIncrement` 옵션과 함께 박스를 정의한 경우 사용

Return value

- [TransactionTask](#transactiontask)

## Box.$find()

> Box 인터페이스의 `find()` 메서드는 [BoxRange](#boxrange)와 제공된 [FilterFunction](#filterfunction)에 의해 필터링된 모든 레코드를 선택합니다.

> `find()` 와 `$find()` 의 차이점은 [TransactionCursorHandler](#transactioncursorhandler)를 반환하는 점입니다.

```javascript
Box.$find(range, ...predicate);
```

Parameters

- range: [BoxRange](#boxrange) (`선택`)
- pridicate: ...[FilterFunction](#filterfunction) (`선택`)

Return value

- [TransactionCursorHandler](#transactioncursorhandler)

## BoxCursorHandler

> `BoxCursorHandler` 인터페이스는 데이터를 제어하기 위해 [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)를 사용합니다.

```typescript
Box.find(...).get(order, limit);
Box.find(...).update(updateValue);
Box.find(...).delete();
```

Methods

- [BoxCursorHandler.get()](#boxcursorhandlerget): 객체 저장소에서 레코드들을 가져옵니다.
- [BoxCursorHandler.update()](#boxcursorhandlerupdate): 객체 저장소의 레코드들을 갱신합니다.
- [BoxCursorHandler.delete()](#boxcursorhandlerdelete): 객체 저장소의 레코드들을 삭제합니다.

### BoxCursorHandler.get()

> BoxCursorHandler 인터페이스의 `get()` 메서드는 객체 저장소에서 필터링된 레코드 리스트를 반환합니다.

```javascript
Box.find(...).get(order, limit);
```

Parameters

- order: [BoxDB.Order](#boxdborder) (`선택`)
  - 기본값: BoxDB.Order.ASC
  - [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)를 열 때 사용됩니다.
  - 지정한 인덱스 기준으로 정렬합니다.(기본값: [in-line key](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key))
- limit: `number` (`선택`)
  - 기본값: _unlimited_

Return value

- [Promise<BoxData[]>](#boxdata)

### BoxCursorHandler.update()

> BoxCursorHandler 인터페이스의 `update()` 메서드는 객체 저장소에서 필터링된 레코드들을 갱신합니다.

```javascript
Box.find(...).update(updateValue);
```

Parameters

- updateValue: [OptionalBoxData](#optionalboxdata)

Return value

- Promise<`void`>

### BoxCursorHandler.delete()

> BoxCursorHandler 인터페이스의 `delete()` 메서드는 객체 저장소에서 필터링된 레코드들을 삭제합니다.

```javascript
Box.find(...).delete();
```

Return value

- Promise<`void`>

## TransactionCursorHandler

```typescript
Box.$find(...).update(updateValue);
Box.$find(...).delete();
```

Methods

- [TransactionCursorHandler.update()](#transactioncursorhandlerupdate)
- [TransactionCursorHandler.delete()](#transactioncursorhandlerdelete)

### TransactionCursorHandler.update()

> `TransactionCursorHandler.update()` 메서드는 [BoxCursorHandler.update()](#boxcursorhandlerupdate)와 동일하지만, [TransactionTask](#transactiontask)를 반환합니다.

```javascript
// Do nothing (only returns TransactionTask)
const task = Box.$find(...).update(updateValue);
```

Parameters

- updateValue: [OptionalBoxData](#optionalboxdata)

Return value

- [TransactionTask](#transactiontask)

### TransactionCursorHandler.delete()

> The `TransactionCursorHandler.delete()` 메서드는 [BoxCursorHandler.delete()](#boxcursorhandlerdelete)와 동일하지만, [TransactionTask](#transactiontask)를 반환합니다.

```javascript
// Do nothing (only returns TransactionTask)
const task_1 = Box.$find(...).delete();
```

Return value

- [TransactionTask](#transactiontask)

## TransactionTask

> `TransactionTask` 는 [transaction()](#transaction)에서 사용되는 객체입니다. 이는 [$get()](#$get), [$delete()](#$delete)와 같이 `$` 접두사가 붙은 메서드로 생성할 수 있습니다.
