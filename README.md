# @sharedutils/event-bus

輕量級 TypeScript 事件匯流排，支援本地和遠端事件分發。

## 特性

- 🚀 輕量級，零依賴
- 💪 完整 TypeScript 類型支援
- 📡 支援本地/遠端事件分發
- 📦 Channel API 介面

## 安裝

```bash
npm install @sharedutils/event-bus
```

## 使用

### 基礎用法

```typescript
import { EventBus } from '@sharedutils/event-bus';

const bus = new EventBus();

// 訂閱
const unsubscribe = bus.subscribe('user:login', async (event) => {
  console.log('User logged in:', event);
});

// 分發
bus.dispatch('user:login', { userId: '123', username: 'alice' });

// 取消訂閱
unsubscribe();
```

### Channel API

```typescript
interface UserLoginEvent {
  userId: string;
  username: string;
}

const loginChannel = bus.channel<UserLoginEvent>('user:login');

loginChannel.subscribe(async (event) => {
  console.log('User logged in:', event.username);
});

loginChannel.dispatch({ userId: '123', username: 'alice' });
```

### 遠端事件匯流排

```typescript
import { RemoteEventBus } from '@sharedutils/event-bus';

const remoteBus = new RemoteEventBus((topic, event) => {
  websocket.send(JSON.stringify({ topic, event }));
});

// 訂閱（接收遠端事件）
remoteBus.subscribe('chat:message', async (msg) => console.log(msg));

// 發布到遠端
remoteBus.publish('chat:message', { text: 'Hello!' });

// 僅本地分發
remoteBus.dispatch('chat:message', { text: 'Local only' });
```

## API

### EventBus

| 方法 | 說明 |
|------|------|
| `subscribe(topic, handler)` | 訂閱主題，返回取消函數 |
| `dispatch(topic, event)` | 本地分發事件 |
| `channel<T>(topic)` | 建立類型安全的 Channel |
| `onSubscriberChange(handler)` | 監聽訂閱變化 |
| `topics()` | 取得所有主題 |

### RemoteEventBus

繼承 EventBus，額外提供：

| 方法 | 說明 |
|------|------|
| `publish(topic, event)` | 發布到遠端 |
| `setPublishProvider(fn)` | 設定發布函數 |
| `publisherChannel<T>(topic)` | 建立發布 Channel |

## License

ISC
