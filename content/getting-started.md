---
title: 'Getting Started'
draft: false
ShowToc: true
---

BoxDB is a promise-based browser ORM for [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)

# Concept

[IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) is async, fast, powerful. but not support [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) and
must use callback pattern to using IndexedDB. This feels like a legacy code.

For this reason, I started user friendly IndexedDB library, BoxDB project.

But, why box?

- Boxes are lightweight.
- Boxes has shape.
- Boxes can store something.
- Boxes are easy to take.

This is the starting point and core concept of BoxDB.

Boxes are the essence of BoxDB. A box is an abstraction that represents a [object store](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore) in your IndexedDB.

Box is the same concept like `Model` used by other ORMs.

# Features

- Promise based ORM
- User friendly and easy to use
- Lightweight(< 10kb) IndexedDB wrapper
- Zero dependencies
- Database and object store version management
- Data validation and transaction control via model (box)
- ACID(Atomicity, Consistency, Isolation, Durability) guaranteed with transaction
- Supports TypeScript
- Works on [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)

# Browser Support

| ![ie](https://user-images.githubusercontent.com/26512984/121935549-8292ca00-cd83-11eb-885c-9497bc78b104.png)</br>IE | ![edge](https://user-images.githubusercontent.com/26512984/121934559-64789a00-cd82-11eb-9238-4fc21eb835e2.png)</br>Edge | ![firefox](https://user-images.githubusercontent.com/26512984/121934551-62aed680-cd82-11eb-8a33-593af8b5fdbd.png)</br>Firefox | ![chrome](https://user-images.githubusercontent.com/26512984/121934545-604c7c80-cd82-11eb-884d-d9d8dad26e01.png)</br>Chrome | ![safari](https://user-images.githubusercontent.com/26512984/121934539-5dea2280-cd82-11eb-96ed-fbef553ec0e6.png)</br>Safari | ![ios-safari](https://user-images.githubusercontent.com/26512984/121934534-5c205f00-cd82-11eb-846b-cac169df47c7.png)</br>iOS Safari | ![samsung](https://user-images.githubusercontent.com/26512984/121934526-5aef3200-cd82-11eb-981d-835490f7b1b2.png)</br>Samsung | ![opera](https://user-images.githubusercontent.com/26512984/121934519-59256e80-cd82-11eb-9b11-4805c7dd0ba1.png)</br>Opera |
| ------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| 11                                                                                                                  | 12~                                                                                                                     | 10~                                                                                                                           | 23~                                                                                                                         | 10~                                                                                                                         | 10~                                                                                                                                 | 4~                                                                                                                            | 15~                                                                                                                       |

- Test features in your browser [here](/bxd/demo.html).
- Checkout `IE11` test [here](/bxd/ie).

# Installation

BoxDB is available via [npm](https://www.npmjs.com/bxd) (or [yarn](https://yarnpkg.com/package/bxd)).

```bash
yarn add bxd
# or
npm install bxd
```

# Basics

## Preparing for database

If database is not exist, create new one.

```typescript
import BoxDB from 'bxd';

const db = new BoxDB('database-name', 1); // database name, version
```

## Create box

```typescript
// User Box (object store name: user)
const User = db.box('user', {
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

// Item Box (object store name: item)
const Item = db.box('item', {
  uid: {
    type: BoxDB.Types.STRING,
    index: true,
  },
  name: {
    type: BoxDB.Types.STRING,
    index: true,
  },
  memo: BoxDB.Types.STRING,
});
```

## Update database version

If you want change database version (eg. change box schema, create new box, etc) just change to larger number then previous version

```typescript
// Previous version
// const db = new BoxDB('database-name', 1);

const db = new BoxDB('database-name', 2); // Just like this!
```

## Open database

```typescript
// define boxes
// ...

await db.open();

// You can use Boxes from here! (data access/control)
```

## Close database

It usually used in special cases. (eg. open database that has same name)

```typescript
db.close();
```

## Transaction

### Add single record

```typescript
await User.add({ _id: '0', name: 'EMPTY', age: 0 });
await User.add({ _id: '1', name: 'leegeunhyeok', age: 20 });
```

### Add multiple records

```typescript
const items = [
  { uid: '0', name: 'unknown 1' },
  { uid: '0', name: 'unknown 2' },
  { uid: '1', name: 'mac', memo: 'so expensive' },
  { uid: '1', name: 'phone' },
  { uid: '1', name: 'desktop' },
];

// In async function
for (let i = 0; i < items.length; i++) {
  await Item.add(items[i]);
}
```

### Get record

```typescript
await User.get('1'); // { _id: '1', name: 'leegeunhyeok', age: 20 }
await User.get('999'); // null
```

### Update record

```typescript
await User.put({ _id: '0', name: 'temp' }); // Update `user._id = '0'`: `name` will update to 'temp'
```

### Delete record

```typescript
await User.delete('0'); // Delete `user._id = '0'`
```

### Get all records by cursor

```typescript
await Item.find().get();

// [
//   { uid: '1', name: 'mac', memo: 'so expensive' },
//   { uid: '2', name: 'phone' },
//   { uid: '3', name: 'desktop' }
// ]
```

### Get records by cursor (using index)

```typescript
await Item.find({ index: 'name', value: 'phone' }).get();

// [{ uid: '2', name: 'phone' }]
```

### Get records by cursor (using filter functions)

```typescript
await Item.find(
  null,
  (item) => parseInt(item.uid) % 2 === 1,
  (item) => item.memo.includes('expensive'),
).get();

// [{ uid: '1', name: 'mac', memo: 'so expensive' }]
```

### Get records by cursor (both)

```typescript
await Item.find(
  {
    index: 'uid',
    value: BoxDB.Range.lower('5'),
  },
  (item) => !!item.memo,
  (item) => parseInt(item.uid) % 2 === 1,
).get();

// [{ uid: '3', name: 'desktop' }]
```

### Update multiple records by cursor

```typescript
await Item.find(null, (item) => !!item.memo).update({ memo: 'new memo' });

// Before: { uid: '1', name: 'mac', memo: 'so expensive' }
//  After: { uid: '1', name: 'mac', memo: 'new memo' }
```

### Delete multiple records by cursor

```typescript
await Item.find(null, (item) => item.uid === '0').delete();

// Delete all of `Item.uid = '0'`
```

### Multiple task in one transaction

If error occurs during transaction task, will be rollback to before transaction.

> Transactionable methods name has `$` prefix ($add, $put, $delete, $find)

```typescript
await db.transaction(
  User.$add({ _id: '3', name: 'Aiden', age: 16 }),
  Item.$add({ uid: '3', name: 'pencil', memo: 'super sharp' }),
  Item.$add({ uid: '3', name: 'eraser' }),
  Item.$add({ uid: '3', name: 'book', memo: '200p' }),
  Item.$add({ uid: '3', name: 'juice' }),
);

// Add new user: { _id: '3', name: 'Aiden', age: 16 }
// Add new item: { uid: '3', name: 'pencil', memo: 'super sharp' }
// Add new item: { uid: '3', name: 'eraser' }
// Add new item: { uid: '3', name: 'juice' }
```

### Get records count

```typescript
await Item.count(); // n
```

### Clear all records

```typescript
// A. Using cursor
await User.find().delete();

// B. Using clear()
await User.clear();
```

## Web workers

```typescript
importScripts('https://cdn.jsdelivr.net/npm/bxd@latest/dist/bxd.min.js');

// Full features available!
self.BoxDB;
```
