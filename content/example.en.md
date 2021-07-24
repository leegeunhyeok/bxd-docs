---
title: 'Example'
draft: false
---

# Basic usage

```bash
# source structure
src --+-- database
      |     +-- index.ts
      +-- models
      |     +-- index.ts
      |     +-- user.ts
      +-- index.ts (entry point)
```

- Create BoxDB instance

  ```typescript
  // src/database/index.ts
  import BoxDB from 'bxd';

  export const DATABASE_NAME = 'database';
  export const VERSION = 1;

  export const database = new BoxDB(DATABASE_NAME, VERSION);
  ```

- Define Box

  ```typescript
  // src/models/user.ts
  import BoxDB, { BoxData } from 'bxd';
  import { database } from '../database';

  const schema = {
    _id: {
      type: BoxDB.Types.NUMBER,
      key: true,
    },
    name: BoxDB.Types.STRING,
    address: BoxDB.Types.STRING,
    hobby: BoxDB.Types.ARRAY,
    created_at: BoxDB.Types.DATE,
    updated_at: BoxDB.Types.DATE,
  } as const;

  const User = database.create('user', schema);

  export type UserType = BoxData<typeof schema>;
  export default User;
  ```

- Setup boxes entry source

  ```typescript
  // src/models/index.ts
  export { default as User } from './user';
  ```

- Open database before using boxes

  ```typescript
  // src/index.ts
  import { database } from './database';

  await database.open();
  ```

- Done!

  ```typescript
  import { User } from 'src/models';

  await User.get(1);
  ...
  ```

## React

- **Continuing from basic setup**
- Create react context and provider

  ```typescript
  // context.ts
  import { createContext, ReactElement } from 'react';
  import { database } from './index';

  export const BoxContext = createContext(database);
  export const BoxProvider = ({ children }: { children: ReactElement }) => {
    return <BoxContext.Provider value={database}>{children}</BoxContext.Provider>;
  };
  ```

- Setup provider

  ```html
  <BoxProvider>
    ...
  </BoxProvider>
  ```

- Using BoxDB context

  ```typescript
  import { useContext } from 'react';
  import { BoxContext } from 'path/to/context.ts';

  const db = useContext(BoxContext);
  ```

# Demo

- [Any Note](https://any-note.vercel.app): Simple note web application
- [Hash Worker](https://hash-worker.vercel.app): Find hash via Web Worker and store task history to IndexedDB
