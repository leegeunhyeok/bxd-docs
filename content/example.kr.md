---
title: 'Example'
draft: false
---


# 기본 사용법

```bash
# 소스코드 구조
src --+-- database
      |     +-- index.ts
      +-- models
      |     +-- index.ts
      |     +-- user.ts
      +-- index.ts (진입점)
```

- BoxDB 인스턴스 생성하기

  ```typescript
  // src/database/index.ts
  import BoxDB from 'bxd';

  export const DATABASE_NAME = 'database';
  export const VERSION = 1;

  export const database = new BoxDB(DATABASE_NAME, VERSION);
  ```

- Box 정의하기

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

- Box 의 엔트리 소스 작성하기

  ```typescript
  // src/models/index.ts
  export { default as User } from './user';
  ```

- Box 를 사용하기 전에 데이터베이스 열기

  ```typescript
  // src/index.ts
  import { database } from './database';

  await database.open();
  ```

- 준비 끝!

  ```typescript
  import { User } from 'src/models';

  await User.get(1);
  ...
  ```

## React

- **기본 사용법에 이어서 진행**
- React Context와 Provider 생성하기

  ```typescript
  // context.ts
  import { createContext, ReactElement } from 'react';
  import { database } from './index';

  export const BoxContext = createContext(database);
  export const BoxProvider = ({ children }: { children: ReactElement }) => {
    return <BoxContext.Provider value={database}>{children}</BoxContext.Provider>;
  };
  ```

- Provider 준비

  ```html
  <BoxProvider>
    ...
  </BoxProvider>
  ```

- BoxDB 컨텍스트 사용하기

  ```typescript
  import { useContext } from 'react';
  import { BoxContext } from 'path/to/context.ts';

  const db = useContext(BoxContext);
  ```

# 예제

- [Any Note](https://any-note.vercel.app): 심플 노트 웹 애플리케이션
- [Hash Worker](https://hash-worker.vercel.app): 웹 워커를 통해 해시값을 찾고, 작업 기록을 IndexedDB 에 저장하는 샘플
