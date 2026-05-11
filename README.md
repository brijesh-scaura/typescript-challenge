## Type-Safe Event Bus

```typescript
// ─────────────────────────────────────────────
// Type-Safe Event Bus
// ─────────────────────────────────────────────
//
// Implement an EventBus<TEvents> class that supports typed
// subscribe/emit/unsubscribe with full type safety.
//
// RULES (must hold):
// 1. Zero `any`. Zero `as` casts. (One internal cast is OK
//    if you can defend it out loud.)
// 2. Calling `emit` with a wrong payload shape → compile error.
// 3. Calling `emit` with an unknown event name → compile error.
// 4. Handler payload must be inferred — no manual generics
//    at the call site.
// 5. `on(...)` returns an unsubscribe function. Calling it
//    must remove the handler (no leaks).
// 6. `emit` returns a Promise that resolves when ALL handlers
//    (sync + async) settle. One handler throwing must NOT
//    prevent the others from running.
// 7. No external libraries.
//
// REQUIRED API:
//   - on<K>(event: K, handler: (payload: TEvents[K]) => void | Promise<void>): () => void
//   - off<K>(event: K, handler): void
//   - emit<K>(event: K, payload: TEvents[K]): Promise<void>
//   - once<K>(event: K, handler): () => void
//   - waitFor<K>(event: K): Promise<TEvents[K]>
//
// BONUS (if time):
//   - onAny(handler: (e: { type: K; payload: TEvents[K] }) => void): () => void
//     — must be a discriminated union over keyof TEvents
//
// ─────────────────────────────────────────────

type AppEvents = {
  'user.signup':   { userId: string; email: string; plan: 'free' | 'pro' };
  'user.delete':   { userId: string };
  'order.placed':  { orderId: string; userId: string; total: number };
  'order.refund':  { orderId: string; reason: string };
};

// TODO: Implement
class EventBus<TEvents extends Record<string, unknown>> {
  // ...
}

// ─────────────────────────────────────────────
// These calls should ALL type-check:
// ─────────────────────────────────────────────
const bus = new EventBus<AppEvents>();

const unsub = bus.on('user.signup', (p) => {
  // p is { userId: string; email: string; plan: 'free' | 'pro' }
  console.log(p.email, p.plan);
});

await bus.emit('user.signup', {
  userId: 'u_1', email: 'z@example.com', plan: 'pro',
});

unsub();

// ─────────────────────────────────────────────
// These should ALL be compile errors:
// ─────────────────────────────────────────────
// bus.emit('user.signup', { userId: '1' });           // missing fields
// bus.emit('unknown.event', {} as never);             // unknown event
// bus.on('user.signup', (p) => p.somethingWrong);     // unknown field
// bus.emit('user.signup', { userId: '1', email: 'x', plan: 'enterprise' }); // bad union
```
