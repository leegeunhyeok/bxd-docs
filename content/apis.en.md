---
title: 'API Reference'
draft: false
ShowToc: true
TocOpen: true
---

> All of features follows IndexedDB mechanism. ([w3c](https://w3c.github.io/IndexedDB), [MDN](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API))

## BoxDB

> The class for IndexedDB and object stores management.

```javascript
const db = new BoxDB(databaseName, version);
```

Parameters

- databaseName: `string`
  - Name of the database
- version: `number`
  - Version to open the database with

Properties

- [BoxDB.Types](#boxdbtypes) `static`
- [BoxDB.Range](#boxdbrange) `static`
- [BoxDB.Order](#boxdborder) `static`
- databaseName: `string`
  - Database name
- version: `number`
  - Database version
- ready: `boolean`
  - Database ready status

Methods

- [BoxDB.interrupt](#boxdbinterrupt) `static`
- [create()](#create)
- [open()](#boxdbopen)
- [transaction()](#boxdbtransaction)

### BoxDB.Types

> The `BoxDB.Types` is a set of defined data types.

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

- BoxDB.Types.BOOLEAN: for [Boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean) value
- BoxDB.Types.NUMBER: for [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number) value
- BoxDB.Types.STRING: for [String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) value
- BoxDB.Types.DATE: for [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) value
- BoxDB.Types.ARRAY: for [Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) value
- BoxDB.Types.OBJECT: for [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) value
- BoxDB.Types.REGEXP: for [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) value
- BoxDB.Types.FILE: for [File](https://developer.mozilla.org/en-US/docs/Web/API/File) value
- BoxDB.Types.BLOB: for [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob) value
- BoxDB.Types.ANY: for _any_ value (Skip type checking)

### BoxDB.Range

> The `BoxDB.Range` is a set of [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange).

```javascript
// Properties
BoxDB.Range.equal(x);
BoxDB.Range.upper(x[, open]);
BoxDB.Range.lower(x[, open]);
BoxDB.Range.bound(x, y[, lowerOpen, upperOpen]);
```

Methods

- BoxDB.Range.equal: returns a new key range containing a single value.
  - Using [IDBKeyRange.only](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/only)
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.upper: returns a new upper-bound key range
  - Using [IDBKeyRange.upperBound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/upperBound)
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.lower: returns a new key range with only a lower bound.
  - Using [IDBKeyRange.lowerBound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/lowerBound)
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
- BoxDB.Range.bound: returns a new key range with the specified upper and lower bounds
  - Using [IDBKeyRange.bound](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange/bound)
  - Returns: [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)

### BoxDB.Order

> The `BoxDB.Order` is a set of [IDBCursor.direction](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor/direction).

```javascript
BoxDB.Order;

BoxDB.Order.ASC; // 'next'
BoxDB.Order.ASC_UNIQUE; // 'nextunique'
BoxDB.Order.DESC; // 'prev'
BoxDB.Order.DESC_UNIQUE; // 'prevunique'
```

- BoxDB.Order.ASC: for using [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) with `next` direction
- BoxDB.Order.ASC_UNIQUE: for using [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) with `nextunique` direction
- BoxDB.Order.DESC: for using [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) with `prev` direction
- BoxDB.Order.DESC_UNIQUE: for using [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) with `prevunique` direction

### BoxDB.interrupt()

> The `interrupt()` method of the BoxDB interface returns a [TransactionTask](#transactiontask) to abort transaction.

```javascript
const abortTask = BoxDB.interrupt(); // TransactionTask

// Usage
db.transaction(Task_1, Task_2, abortTask, Task_3);
```

### BoxDB.create()

> The `create()` method of the BoxDB interface returns new [Box](#box).

```javascript
db.create(name, schema[, options]);
```

Parameters

- storeName: `string`
  - Name of the object store
- schema: [BoxSchema](#boxschema)
  - Data schema of the data to store
- options: [BoxOption](#boxoption) (`optional`)
  - Object store options

Return value

- [Box](#box)

### BoxDB.open()

> The `open()` method of the BoxDB interface open IndexedDB and create/update/delete object store based on created boxes.

```javascript
await db.open();
```

Return value

- Promise<[Event](https://developer.mozilla.org/en-us/docs/Web/API/Event)>

### BoxDB.transaction()

> The `BoxDB.transaction()` method takes an list of [TransactionTask](#transactiontask) as an input, and perform tasks sequentially in one transaction.

> Most important, if an error occurs during transaction, rolled back to the previous state.

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
  - Transaction tasks

Return value

- Promise<`void`>

## BoxSchema

> The `BoxSchema` is an object that for data model. It's includes field name with data type, and detailed options(key/index).

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
  // Method 1. Define field in detail
  name: {
    type: BoxDB.Types.STRING,
    index: true,
    unique: true,
  },
  // Method 2. Only type (No key, No index)
  age: BoxDB.Types.NUMBER,
};
```

Options

- type: [BoxDB.Types](#boxdbtypes)
  - Type of this property (used by type checking)
- key: `boolean` (`optional`)
  - Set this property as [in-line key](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key)
  - Can be set in-line key only once each object store
    - _If you want change, create new box after drop_ ( re-create box with `force` option)
- index: `boolean` (`optional`)
  - [Create](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/createIndex) or [delete](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/deleteIndex) index for this property
  - If you want search values on this field via index, should set to `true`
- unique: `boolean` (`optional`)
  - Add [unique constraint](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/createIndex#parameters) to this property's index
    - Must have use with `index` option

## BoxOption

> Box creation options

```typescript
interface BoxOption {
  autoIncrement?: boolean;
  force?: boolean;
}
```

- autoIncrement: `boolean` (default: `false`)
  - Use [out-of-line key](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_outofline_key) for this object store
  - For [autoIncrement](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/autoIncrement) flag
- force: `boolean` (default: `false`)
  - Force update when [versionchange](https://developer.mozilla.org/en-US/docs/Web/API/IDBDatabase/versionchange_event) event
  - WARNING: This option will be drop exist object store before create

## BoxData

> Data of based on [BoxSchema](#boxschema).

```typescript
// Sample box
const User = db.create('user', {
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

// BoxData will be inferenced like this
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

> The `BoxRange` is an object for query with value or [IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange).

```javascript
// Find age = 10 (should `age` field is defined to index)
const range_1 = {
  value: 10,
  index: 'age',
};

// Find in-line-key(_id) < 5
const range_2 = {
  value: BoxDB.Range.lower(5),
};

Box.find(range_1);
Box.find(range_2);
```

Properties

- value: [BoxDB.Range](#boxdbrange) or [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) value
- index: `string` (`optional`)
  - Indexed field name in schema (`index: true` option)
  - If you don't serve index name, follows [in-line key](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key) as default.

## FilterFunction

> The `FilterFunction` is a function to evaluate each record. This function receives [BoxData](#boxdata) and returns a boolean value.

```javascript
const predicate_1 = (data) => data.age === 10;
const predicate_2 = (data) => !data.name === 'UNKNOWN';
const predicate_3 = (data) => true;

Box.find(null, predicate_1, predicate_2, predicate_3);
```

## Box

> A `Box` is an abstraction that represents a [object store](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore). and also can generate data in a defined form (schema).

```javascript
const User = db.create('user', {
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

// (1) Create empty data
const u1 = new User();
u1.name // string

// (2) Create data with initial data
const u2 = new User({
  _id: 0,
  name: 'Jack',
  age: 12
});

// Model methods for single transaction
User.get(key);
User.put(value, key);
User.delete(key);
User.count();
User.clear();
User.find(range, ...predicate).get(order, limit);
User.find(range, ...predicate).update(updateValue);
User.find(range, ...predicate).delete();

// Returns TransactionTask for BoxDB.transaction()
User.$get(key);
User.$put(key, value);
User.$delete(key);
User.$find(range, ...predicate).update(updateValue);
User.$find(range, ...predicate).delete();
```

Parameters

- storeName: `string`
  - Name of the object store
- schema: [BoxSchema](#boxschema)
  - Data schema of the data to store
- options: [BoxOption](#boxoption) (`optional`)
  - Object store options

Methods

- Box.getName(): Returns object store name
- Box.getVersion(): Returns target database version
- Box.add(value, key): Add record to object store
- Box.get(key): Get record from object store
- Box.put(value[, key]): Put record to object store
- Box.delete(key): Delete record from object store
- Box.count(key): Get records count
- Box.clear(): Drop the object store
- Box.find(range, ...predicate): Returns `BoxCursorHandler`, Transaction by [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)
  - find().get(): Get records
  - find().update(updateValue): Update records
  - find().delete(): Delete records
- [Box.$add()](#box$add): Returns [TransactionTask](#transactiontask) that add record.
- [Box.$put()](#box$put): Returns [TransactionTask](#transactiontask) that update record.
- [Box.$delete()](#box$delete): Returns [TransactionTask](#transactiontask) that delete record.
- [Box.$find()](#box$find): Returns [TransactionCursorHandler](#transactioncursorhandler).

### Box.getName()

> The `getName()` methods returns current object store name

```javascript
Box.getName();
```

Return value

- string

### Box.getVersion()

> The `getVersion()` methods returns version of database

```javascript
Box.getVersion();
```

Return value

- number

### Box.add()

> The `add()` method of the Box interface stores the data in the target object store.

```javascript
Box.add(value[, key]);
Box.add({ id: 1, name: 'Tom', age: 15 });
```

Parameters

- value: [BoxData](#boxdata)
  - The data to be stored.
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`optional`)
  - The key to use to identify the record. (default: `null`)
  - Using this parameter when box defined with `autoIncrement` option.

Return value

- Promise<[IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)>
  - Returns added record's key (both in/out-of-line key)

### Box.get()

> The `get()` method of the Box interface returns an specific record data from object store

```javascript
Box.get(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)
  - The key or key range that identifies the record to be retrieved.

Return value

- Promise<[BoxData](#boxdata)>

### Box.put()

> The `put()` method of the Box interface updates a given record in a database, or inserts a new record if the given item does not already exist.

```javascript
Box.put(value[, key]);
```

Parameters

- value: [BoxData](#boxdata)
  - The data you wish to update.
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`optional`)
  - The key that identifies the record to be updated.
  - Using this parameter when box defined with `autoIncrement` option.

Return value

- Promise<`void`>

### Box.delete()

> The `delete()` method of the Box interface deletes target record

```javascript
Box.delete(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey)
  - The key that identifies the record to be deleted.

Return value

- Promise<`void`>

## Box.find()

> The `find()` method of the Box interface selects all records that are retrieved by [BoxRange](#boxrange) and passed the provided [FilterFunction](#filterfunction).

Parameters

- range: [BoxRange](#boxrange) (`optional`)
- pridicate: ...[FilterFunction](#filterfunction) (`optional`)

Return value

- [BoxCursorHandler](#boxcursorhandler)

### Box.count()

> The `BoxMode.count()` method returns all records count in object store.

```javascript
Box.count();
```

Return value

- Promise<`number`>

### Box.clear()

> The `BoxMode.clear()` method clears all of data in object store.

```javascript
Box.clear();
```

Return value

- Promise<`void`>

### Box.$add()

> The `$add()` of the Box interface returns [TransactionTask](#transactiontask) to add record.

> `$` Prefixed methods returns [TransactionTask](#transactiontask)

```javascript
Box.$add(value[, key]);
Box.$add({ id: 2, name: 'Carl', age: 12 });
```

Parameters

- value: [BoxData](#boxdata)
  - The data to be stored.
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`optional`)
  - The key to use to identify the record. (default: `null`)
  - Using this parameter when box defined with `autoIncrement` option.

Return value

- [TransactionTask](#transactiontask)

### Box.$put()

> The `$put()` of the Box interface returns a [TransactionTask](#transactiontask) to update or insert record.

```javascript
Box.$put(value[, key]);
```

Parameters

- value: [BoxData](#boxdata)
  - The data you wish to update.
- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`optional`)
  - The key that identifies the record to be updated.
  - Using this parameter when box defined with `autoIncrement` option.

Return value

- [TransactionTask](#transactiontask)

### Box.$delete()

> The `$delete()` of the Box interface returns a [TransactionTask](#transactiontask) to delete record.

```javascript
Box.$delete(key);
```

Parameters

- key: [IDBValidKey](https://microsoft.github.io/PowerBI-JavaScript/modules/_node_modules_typedoc_node_modules_typescript_lib_lib_dom_d_.html#idbvalidkey) (`optional`)
  - The key that identifies the record to be deleted.
  - Using this parameter when box defined with `autoIncrement` option.

Return value

- [TransactionTask](#transactiontask)

## Box.$find()

> The `find()` method of the Box interface selects all records that are retrieved by [BoxRange](#boxrange) and passed the provided [FilterFunction](#filterfunction).

> Different from `find()`, `$find()` returns [TransactionCursorHandler](#transactioncursorhandler).

```javascript
Box.$find(range, ...predicate);
```

Parameters

- range: [BoxRange](#boxrange) (`optional`)
- pridicate: ...[FilterFunction](#filterfunction) (`optional`)

Return value

- [TransactionCursorHandler](#transactioncursorhandler)

## BoxCursorHandler

> The `BoxCursorHandler` interface uses [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor) to control data.

```typescript
Box.find(...).get(order, limit);
Box.find(...).update(updateValue);
Box.find(...).delete();
```

Methods

- [BoxCursorHandler.get()](#boxcursorhandlerget): Get records from object store
- [BoxCursorHandler.update()](#boxcursorhandlerupdate): Update records in object store
- [BoxCursorHandler.delete()](#boxcursorhandlerdelete): Delete records in object store

### BoxCursorHandler.get()

> The `get()` method of the BoxCursorHandler interface returns a filtered list of record data from object store.

```javascript
Box.find(...).get(order, limit);
```

Parameters

- order: [BoxDB.Order](#boxdborder) (`optional`)
  - Default: BoxDB.Order.ASC
  - Uses in open [IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)
  - Ordering based on specified index (default: [in-line key](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Basic_Concepts_Behind_IndexedDB#gloss_inline_key))
- limit: `number` (`optional`)
  - Default: _unlimited_

Return value

- [Promise<BoxData[]>](#boxdata)

### BoxCursorHandler.update()

> The `update()` method of the BoxCursorHandler interface updates filtered records data from object store.

```javascript
Box.find(...).update(updateValue);
```

Parameters

- updateValue: [OptionalBoxData](#optionalboxdata)

Return value

- Promise<`void`>

### BoxCursorHandler.delete()

> The `delete()` method of the BoxCursorHandler interface deletes filtered records data from object store.

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

> The `TransactionCursorHandler.update()` interface behaves the same as [BoxCursorHandler.update()](#boxcursorhandlerupdate), but returns [TransactionTask](#transactiontask).

```javascript
// Do nothing (only returns TransactionTask)
const task = Box.$find(...).update(updateValue);
```

Parameters

- updateValue: [OptionalBoxData](#optionalboxdata)

Return value

- [TransactionTask](#transactiontask)

### TransactionCursorHandler.delete()

> The `TransactionCursorHandler.delete()` interface behaves the same as [BoxCursorHandler.delete()](#boxcursorhandlerdelete), but returns [TransactionTask](#transactiontask).

```javascript
// Do nothing (only returns TransactionTask)
const task_1 = Box.$find(...).delete();
```

Return value

- [TransactionTask](#transactiontask)

## TransactionTask

> The `TransactionTask` is a value object for using in [transaction()](#transaction). It can be created with `$` prefixed methods like [$get()](#$get), [$delete()](#$delete).
