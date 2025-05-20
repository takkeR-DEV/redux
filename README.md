# Redux Toolkit: Простое управление состоянием

## Слайд 1: Что такое Redux Toolkit?

**Redux Toolkit** — это современная официальная библиотека от Redux Team, которая упрощает работу с Redux:

* Убирает "боли" классического Redux
* Делает код компактнее
* Добавляет поддержку иммутабельности через Immer
* Включает мощные инструменты, такие как `createSlice`, `configureStore`, `createAsyncThunk`

---

## Слайд 2: Зачем вообще нужен Redux?

React не даёт глобального состояния из коробки.

> Проблема: Компоненты на разных уровнях дерева хотят делиться одними и теми же данными.

**Решение: Redux — глобальное хранилище.**

* Один источник истины
* Прозрачное управление состоянием
* Простое масштабирование

---

## Слайд 3: Установка

```bash
npm install @reduxjs/toolkit react-redux
```

---

## Слайд 4: Структура проекта

```
src/
  store.ts
  features/
    counter/
      counterSlice.ts
    users/
      usersSlice.ts
  App.tsx
```

---

## Слайд 5: Создание Slice

```ts
// counterSlice.ts
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
    decrement: (state) => { state.value -= 1; }
  }
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

---

## Слайд 6: Создание Store

```ts
// store.ts
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './features/counter/counterSlice';
import usersReducer from './features/users/usersSlice';

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    users: usersReducer
  }
});
```

---

## Слайд 7: Подключение Store к приложению

```tsx
// main.tsx
import { Provider } from 'react-redux';
import { store } from './store';

<Provider store={store}>
  <App />
</Provider>
```

---

## Слайд 8: Использование в компоненте (Counter)

```tsx
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './features/counter/counterSlice';

const Counter = () => {
  const value = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <button onClick={() => dispatch(decrement())}>-</button>
      <span>{value}</span>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
};
```

---

## Слайд 9: Асинхронность — createAsyncThunk

```ts
// usersSlice.ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';

export const fetchUsers = createAsyncThunk(
  'users/fetch',
  async () => {
    const res = await fetch('https://jsonplaceholder.typicode.com/users');
    return await res.json();
  }
);

const usersSlice = createSlice({
  name: 'users',
  initialState: { data: [], status: 'idle' },
  reducers: {},
  extraReducers: (builder) => {
    builder.addCase(fetchUsers.pending, (state) => {
      state.status = 'loading';
    });
    builder.addCase(fetchUsers.fulfilled, (state, action) => {
      state.status = 'succeeded';
      state.data = action.payload;
    });
    builder.addCase(fetchUsers.rejected, (state) => {
      state.status = 'failed';
    });
  }
});

export default usersSlice.reducer;
```

---

## Слайд 10: Использование запроса в компоненте

```tsx
import { useSelector, useDispatch } from 'react-redux';
import { fetchUsers } from './features/users/usersSlice';
import { useEffect } from 'react';

const Users = () => {
  const dispatch = useDispatch();
  const users = useSelector((state) => state.users.data);
  const status = useSelector((state) => state.users.status);

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  if (status === 'loading') return <p>Загрузка...</p>;
  if (status === 'failed') return <p>Ошибка загрузки</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};
```

---

## Слайд 11: Заключение

✅ Redux Toolkit делает Redux современным и удобным.

* Простая настройка store
* Slice = редьюсер + экшены
* Мутируем state — но безопасно
* Поддержка асинхронных операций через createAsyncThunk

> Используйте Redux Toolkit в новых проектах вместо "ручного" Redux!
