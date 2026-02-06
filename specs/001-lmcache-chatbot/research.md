# Research Findings for Multi-Turn Conversational AI with LMCACHE

**Date**: 2026-02-06
**Status**: Completed

This document summarizes the findings for the technical unknowns and best practices related to the project.

## 1. Storage for lmcache

**Task**: Research storage requirements for `lmcache`.

**Decision**: `lmcache` will be configured to use an in-memory cache.

**Rationale**: The primary goal is to minimize latency during a single, long conversation. An in-memory cache provides the fastest possible access to the KV cache states. While a persistent cache (like Redis) could be used to share cache entries across different sessions and users, the immediate requirement is to demonstrate in-conversation performance gains. Using a persistent cache would introduce additional network latency and complexity that is not required to meet the core success criteria. We can revisit this decision if global caching becomes a P1 priority.

**Alternatives considered**:
- **Redis**: A Redis-backed cache would allow for persistence and sharing of cache entries across multiple instances of the application. This would be a good choice for scaling up, but adds complexity.
- **File-based cache**: A file-based cache would be simpler than Redis but slower than in-memory. It would not be suitable for the low-latency requirements of this project.

## 2. Best Practices for langgraph

**Task**: Find best practices for building multi-agent systems with `langgraph`.

**Decision**: We will use a graph-based architecture with a centralized router node.

**Rationale**: `langgraph` is designed for creating stateful, multi-agent applications. A graph is a natural way to represent the flow of a conversation between different agents. The router will be a dedicated node in the graph that inspects the user's prompt and directs it to the appropriate specialist agent (e.g., "Sales Agent", "Support Agent"). This pattern allows for clear separation of concerns and makes it easy to add new agents in the future. We will use `langgraph`'s built-in state management to maintain the conversation history.

## 3. Best Practices for langfuse

**Task**: Find best practices for monitoring and observability with `langfuse`.

**Decision**: We will integrate `langfuse` using its Python SDK to trace all agent and tool invocations.

**Rationale**: `langfuse` provides detailed tracing and analytics for LLM applications. By wrapping our `langgraph` nodes and tool calls with `langfuse` decorators, we can automatically capture traces, latency metrics, and token usage. This will be essential for verifying the success criteria (TTFT, cache hit rate, CPU usage). We will create dashboards in the `langfuse` UI to monitor these key metrics in real-time.

## 4. Best Practices for vllm

**Task**: Find best practices for using `vllm` as an inference engine.

**Decision**: We will use `vllm` as the backend for our local LLM inference.

**Rationale**: `vllm` is a high-throughput and memory-efficient inference engine. It is particularly well-suited for this project because it is compatible with `lmcache`. We will configure `vllm` to use a batching strategy that maximizes throughput while keeping latency low. The integration with `lmcache` will allow us to take advantage of KV caching at the inference level.

## 5. Best Practices for lmcache

**Task**: Find best practices for using `lmcache` for KV caching.

**Decision**: We will implement Prefix Alignment in our prompt templates to maximize cache hits.

**Rationale**: The `spec.md` explicitly calls for this. By structuring our prompts with a large, static prefix (the "Manual"), we ensure that the initial part of the prompt is the same across multiple turns of a conversation. `lmcache` can then reuse the cached KV values for this prefix, which significantly speeds up inference. The router and agents will be designed to work with these structured prompts.

## 6. Best Practices for FastAPI

**Task**: Find best practices for building high-performance APIs with `FastAPI`.

**Decision**: We will use `FastAPI`'s asynchronous capabilities to handle concurrent requests efficiently.

**Rationale**: `FastAPI` is built on `asyncio`, which makes it ideal for I/O-bound applications like a chatbot. All of our API endpoints will be `async` functions. This will allow the server to handle many concurrent connections without blocking. We will also use Pydantic for request and response validation, which helps to ensure data integrity.
