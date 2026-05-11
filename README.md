## Type-Safe Multi-Provider LLM SDK**

You're building a TypeScript SDK that lets developers chat with multiple LLM providers — OpenAI, Anthropic, and Gemini — through a single unified `chat()` function.

The SDK must satisfy these requirements:

1. **Three providers are supported:** `openai`, `anthropic`, `gemini`. Each is created via a factory function (`openai()`, `anthropic()`, `gemini()`) that returns an `Adapter`.

2. **Each provider supports only its own models:**
   - OpenAI: `"gpt-4o" | "gpt-4.1" | "gpt-4.1-mini" | "gpt-3.5-turbo"`
   - Anthropic: `"claude-3-opus" | "claude-3-sonnet" | "claude-3-haiku"`
   - Gemini: `"gemini-1.5-pro" | "gemini-1.5-flash"`

3. **Each provider has its own options shape:**
   - OpenAI: `temperature`, `maxTokens`, `presencePenalty`
   - Anthropic: `topK`, `topP`, `maxOutputTokens`
   - Gemini: `safetyLevel` (`"low" | "medium" | "high"`), `responseMimeType` (`"text/plain" | "application/json"`)

4. The `chat()` function takes a config object with `adapter`, `messages`, `model`, `conversationId`, and optional `providerOptions`.

**The hard requirement — enforce all of this at compile time:**

- If `adapter` is `openai()`, then `model` MUST be an OpenAI model, and `providerOptions` MUST match `OpenAIOptions`. Passing `"claude-3-opus"` as the model, or `topK` in options, should be a **TypeScript compile error** — not a runtime check.
- Same constraint for Anthropic and Gemini.
- The user should NOT have to pass a generic parameter manually. `chat({ adapter: openai(), ... })` should infer everything automatically.

**Your task:**

Implement the full type system and the `chat()` function so that:

```ts
// ✅ Should compile
chat({
  adapter: openai(),
  messages: [{ role: "user", content: "hi" }],
  model: "gpt-4o",
  conversationId: "c1",
  providerOptions: { temperature: 0.7 }
});

// ❌ Should FAIL to compile — wrong model for adapter
chat({
  adapter: openai(),
  messages: [],
  model: "claude-3-opus",
  conversationId: "c2"
});

// ❌ Should FAIL to compile — temperature not valid for Gemini
chat({
  adapter: gemini(),
  messages: [],
  model: "gemini-1.5-pro",
  conversationId: "c3",
  providerOptions: { temperature: 1.0 }
});
```
