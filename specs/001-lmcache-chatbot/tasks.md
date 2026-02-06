# Tasks: Multi-Turn Conversational AI with LMCACHE

**Input**: Design documents from `/specs/001-lmcache-chatbot/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- Paths are based on the `plan.md` structure: `backend/src/`, `backend/tests/`

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [X] T001 Create project structure `backend/src` and `backend/tests`
- [ ] T002 Initialize poetry project in `backend/` with dependencies from `plan.md`
- [ ] T003 [P] Configure linting and formatting tools (`ruff`, `black`) in `backend/pyproject.toml`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

- [ ] T004 Setup basic FastAPI application in `backend/src/main.py`
- [ ] T005 [P] Implement a client for the vLLM server in `backend/src/services/vllm_client.py`
- [ ] T006 Integrate `lmcache` with the vLLM client in `backend/src/services/vllm_client.py`
- [ ] T007 [P] Configure `langfuse` for tracing in `backend/src/main.py`
- [ ] T008 Implement the agent router using `langgraph` in `backend/src/services/agent_router.py`
- [ ] T009 [P] Define base agent prompt templates with prefix alignment in `backend/src/services/prompt_templates.py`

---

## Phase 3: User Story 1 - Experience Fast First-Time Responses (Priority: P1) 🎯 MVP

**Goal**: Deliver a fast initial response for a new conversation.

**Independent Test**: Send a unique prompt and measure the TTFT.

### Tests for User Story 1

- [ ] T010 [P] [US1] Contract test for `POST /conversations` in `backend/tests/contract/test_api.py`
- [ ] T011 [US1] Integration test for a single-turn conversation in `backend/tests/integration/test_conversation.py`

### Implementation for User Story 1

- [ ] T012 [P] [US1] Create `Conversation` and `Turn` data models in `backend/src/models/conversation.py` based on `data-model.md`
- [ ] T013 [US1] Implement `ConversationService` to handle conversation logic in `backend/src/services/conversation_service.py`
- [ ] T014 [US1] Implement `POST /conversations` endpoint in `backend/src/api/endpoints.py`
- [ ] T015 [US1] Integrate `ConversationService` with the `langgraph` agent router in `backend/src/services/conversation_service.py`

---

## Phase 4: User Story 2 - Benefit from Cached Responses in Long Conversations (Priority: P1)

**Goal**: Decrease response time for follow-up questions in a long conversation.

**Independent Test**: Simulate a multi-turn conversation and verify that latency decreases.

### Tests for User Story 2

- [ ] T016 [P] [US2] Contract test for `POST /conversations/{conversation_id}/turns` in `backend/tests/contract/test_api.py`
- [ ] T017 [US2] Performance test to measure latency decrease in a multi-turn conversation in `backend/tests/performance/test_latency.py`

### Implementation for User Story 2

- [ ] T018 [US2] Implement `POST /conversations/{conversation_id}/turns` endpoint in `backend/src/api/endpoints.py`
- [ ] T019 [US2] Update `ConversationService` to handle subsequent turns in a conversation in `backend/src/services/conversation_service.py`
- [ ] T020 [US2] Ensure prompt templates in `backend/src/services/prompt_templates.py` use a shared prefix to maximize cache hits.

---

## Phase 5: User Story 3 - Receive Instant Answers for Repeated Questions (Priority: P2)

**Goal**: Deliver near-instant responses for frequently asked questions.

**Independent Test**: Send the same prompt multiple times and verify a high cache hit rate.

### Tests for User Story 3

- [ ] T021 [US3] Performance test to measure cache hit rate for repeated prompts in `backend/tests/performance/test_cache_hit_rate.py`

### Implementation for User Story 3

- [ ] T022 [US3] Review and optimize `lmcache` configuration for global cache effectiveness.
- [ ] T023 [US3] Add logging of cache hit metrics to `langfuse` from `backend/src/services/vllm_client.py`

---

## Phase 6: User Story 4 - Understand Caching Benefits via a Tutorial (Priority: P3)

**Goal**: Create a tutorial for developers to replicate the solution and performance benefits.

**Independent Test**: A developer can follow the tutorial and successfully verify the performance metrics.

### Implementation for User Story 4

- [ ] T024 [P] [US4] Write the developer tutorial in `specs/001-lmcache-chatbot/tutorial.md`
- [ ] T025 [P] [US4] Create performance test scripts for the tutorial in `backend/tests/performance/`
- [ ] T026 [US4] Update `quickstart.md` with instructions on running the tutorial tests.

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T027 [P] Update all documentation in `specs/001-lmcache-chatbot/`
- [ ] T028 Code cleanup and refactoring across the `backend/src/` directory.
- [ ] T029 Security hardening of the API in `backend/src/main.py`
- [ ] T030 Final validation of all performance metrics.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: Can start immediately.
- **Foundational (Phase 2)**: Depends on Setup. Blocks all user stories.
- **User Stories (Phases 3-6)**: Depend on Foundational. Can proceed in priority order.
- **Polish (Phase 7)**: Depends on all desired user stories.

### User Story Dependencies

- **User Story 1 & 2 (P1)**: Can start after Foundational.
- **User Story 3 (P2)**: Can start after Foundational.
- **User Story 4 (P3)**: Can start after Foundational.

### Parallel Opportunities

- Tasks marked with `[P]` can be executed in parallel.
- Once the Foundational phase is complete, different developers can work on different user stories in parallel.

---

## Implementation Strategy

### MVP First (User Story 1 & 2)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Complete Phase 3: User Story 1
4. Complete Phase 4: User Story 2
5. **STOP and VALIDATE**: Test the core functionality of multi-turn conversations and caching benefits.
