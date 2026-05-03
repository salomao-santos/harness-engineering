# Implementation Plan

<!--
  HOW TO USE THIS TEMPLATE
  ─────────────────────────
  This document is derived from requirements.md and design.md.
  Every task must be traceable to a requirement. Every design element must have a task.

  PREREQUISITES:
  - requirements.md: complete and approved
  - design.md: complete and approved

  TASK STATUS SYNTAX:
    - [ ]   Not started (mandatory)
    - [x]   Done
    - [-]   In progress
    - [~]   Queued
    - [ ]*  Not started (optional — can be skipped for MVP)

  TRACEABILITY:
  Each sub-task ends with: _Requirements: N.N_  (or N.N, N.N for multiple)
-->

## Overview

<!--
  2–4 sentences covering:
  - What this plan implements (system/feature name)
  - Technology stack (runtime, framework, database, UI)
  - Incremental philosophy (no orphan code)
  - Number of main tasks and phases
-->

This plan converts the design of {feature name} into incremental coding tasks.
Stack: {describe stack, e.g., "Bun + TypeScript for backend, React + Vite + CSS Modules for frontend"}.
Each task builds on the previous ones — no orphan code.

---

## Tasks

### Phase 1 — Setup and Shared Types

- [ ] 1. {Setup task title}
  - [ ] 1.1 {Initialize project and configure structure}
    - {Create configuration file with scripts for each layer}
    - {Initialize framework/bundler with appropriate template}
    - {Create directories for each layer}
    - {Configure compiler/transpiler}
    - {Install dependencies: list each one}
    - _Requirements: {N.N}_

  - [ ] 1.2 {Define shared types/interfaces}
    - {Create types file in backend with: list each type/interface}
    - {Create types file in frontend mirroring backend types}
    - _Requirements: {N.N, N.N}_

---

### Phase 2 — Data Layer

- [ ] 2. {Database task title}
  - [ ] 2.1 {Create schema and database initialization}
    - {Create file with initialization function}
    - {List tables to create with constraints}
    - {Database configuration: PRAGMAs, indexes, etc.}
    - _Requirements: {N.N, N.N}_

  - [ ] 2.2 {Implement main entity repository}
    - {Create file with repository class/module}
    - {List each method: name, parameters, behavior}
    - {Mention patterns: parameterized queries, RETURNING *, etc.}
    - _Requirements: {N.N, N.N}_

  - [ ] 2.3 {Create seed data}
    - {Create file with seed function}
    - {Describe idempotency check (do not re-insert)}
    - {List sample data with quantities and distribution}
    - _Requirements: {N.N, N.N}_

  - [ ]* 2.4 {Write unit tests for repositories}
    - {List specific test scenarios}
    - {Cover CRUD, CASCADE, ordering, edge cases}
    - _Requirements: {N.N, N.N}_

---

### Phase 3 — Backend / API

- [ ] 3. {API task title}
  - [ ] 3.1 {Implement routes for main resource}
    - {Create routes/controllers file}
    - {List each endpoint: METHOD /route — description}
    - {Describe how each handler works}
    - {List HTTP return codes}
    - _Requirements: {N.N, N.N}_

  - [ ] 3.2 {Implement validation and error handling}
    - {Create validation file with specific functions}
    - {Create error handler file}
    - {Describe standardized error format}
    - _Requirements: {N.N, N.N, N.N}_

  - [ ] 3.3 {Create main server / entry point}
    - {Create backend entry file}
    - {Describe initialization: database, seed, server}
    - {Configure route mapping}
    - {Define port and 404 fallback}
    - _Requirements: {N.N, N.N}_

  - [ ]* 3.4 {Write unit tests for API}
    - {List test scenarios for validation}
    - {List test scenarios for each endpoint}
    - _Requirements: {N.N, N.N}_

---

### Phase 4 — Backend Checkpoint

- [ ] 4. Checkpoint — Verify functional backend
  - Ensure all tests pass and the server starts correctly. Ask the user if there are any questions.

---

### Phase 5 — Frontend — Base Components

- [ ] 5. {Frontend components task title}
  - [ ] 5.1 {Configure global CSS and design variables}
    - {Create CSS file with reset and variables}
    - {List ALL CSS variables with concrete values (hex colors, px, fonts)}
    - {Define base styles: body, focus, typography}
    - _Requirements: {N.N, N.N}_

  - [ ] 5.2 {Implement component X}
    - {Create .tsx and .module.css files}
    - {List Props with types}
    - {Describe visual and conditional behavior}
    - {Mention concrete values: colors, sizes, texts}
    - {Mention accessibility: aria-labels, semantic HTML}
    - _Requirements: {N.N, N.N}_

  - [ ] 5.3 {Implement component Y}
    - {Same structure as 5.2}
    - _Requirements: {N.N, N.N}_

  <!-- Repeat for each component defined in design.md -->

---

### Phase 6 — Frontend — API Client

- [ ] 6. {API Client task title}
  - [ ] 6.1 {Implement API Client}
    - {Create file per resource: list functions with names}
    - {Create utility function for response handling}
    - {Define base URL and proxy strategy}
    - _Requirements: {N.N, N.N}_

  - [ ] 6.2 {Configure proxy/CORS}
    - {Update bundler/server configuration}
    - _Requirements: {N.N}_

---

### Phase 7 — Frontend — Full Integration

- [ ] 7. {Integration task title}
  - [ ] 7.1 {Implement layout/container component}
    - {Create .tsx and .module.css files}
    - {Describe Props, rendering, semantic HTML}
    - _Requirements: {N.N, N.N}_

  - [ ] 7.2 {Implement main component (Dashboard/Page)}
    - {Create .tsx and .module.css files}
    - {Describe data fetching (useEffect, fetch on mount)}
    - {Describe data grouping/filtering}
    - {Describe modal state management}
    - {List ALL handlers: create, edit, delete, etc.}
    - {Describe local state update after CRUD operations}
    - {Mention semantic HTML: main, nav, section}
    - _Requirements: {N.N, N.N, N.N, ...}_

  - [ ] 7.3 {Integrate in App and configure entry point}
    - {Update root component to render the main component}
    - _Requirements: {N.N}_

---

### Phase 8 — End-to-End Checkpoint

- [ ] 8. Checkpoint — Verify functional end-to-end application
  - Ensure all tests pass, the backend serves the API correctly, and the frontend communicates with the backend. Ask the user if there are any questions.

---

### Phase 9 — Property-Based Tests (PBT)

- [ ] 9. {PBT task title}
  - [ ]* 9.1 {Write property-based test for Entity1 round-trip}
    - **Property: {Formal property name}**
    - {Description: For any valid object X, operation Y SHALL produce result Z}
    - {How to generate random data: fields, types, valid ranges}
    - {PBT library to use}
    - **Validates: Requirements {N.N}**

  - [ ]* 9.2 {Write property-based test for domain invariant}
    - **Property: {Formal property name}**
    - {Same structure as 9.1}
    - **Validates: Requirements {N.N}**

  <!-- Add more properties as needed:
    - Operation idempotency
    - Domain invariants
    - Field preservation in updates
  -->

---

### Phase 10 — Accessibility and Polish (Optional)

- [ ] 10. {Accessibility task title}
  - [ ] 10.1 {Review and ensure accessibility}
    - {Verify aria-labels on icon-only buttons}
    - {Verify role="dialog" and aria-modal="true" on modals}
    - {Verify keyboard navigation: Tab, Enter/Space, Escape}
    - {Verify visible focus indicators}
    - {Verify semantic HTML: main, nav, section, article}
    - {Verify minimum contrast of 4.5:1}
    - _Requirements: {N.N}_

---

### Phase 11 — Final Checkpoint

- [ ] 11. Final Checkpoint — Ensure all tests pass
  - Ensure all tests pass and the application is fully functional. Ask the user if there are any questions.

---

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property-based tests validate universal correctness properties (e.g., serialization round-trip)
- Unit tests validate specific examples and edge cases
- {Language/runtime of the project}
