# Contribution [#5687]: [Improve HTTP cache options]

**Contribution Number:** [1]  
**Student:** [Christian Guardiola]  
**Issue:** [[GitHub issue link](https://github.com/defold/defold/issues/5687)]  
**Status:** [Phase II]

---

## Why I Chose This Issue

Improving HTTP cache is an important lesson to improve efficiency for an engine. My learning goal for this issue is to develop a working solution for the Defold engine, while being supported by AI. If successful, both the engine and I will benefit by establishing standard web compliance metrics directly inside the core network layer.

---

## Understanding the Issue

### Problem Description
Defold's internal network library (`dlib`) features a minimal HTTP caching system that only recognizes a single caching instruction: `Cache-Control: max-age`. Modern web architectures, CDNs, and proxies rely on a wider array of standard headers to manage resource lifecycles. Because the engine cannot process the `Expires` header or major directives like `no-cache`, `no-store`, and `must-revalidate`, it performs redundant network requests for assets that should be cached, or mistakenly caches sensitive data that should be excluded.

### Expected Behavior
The HTTP client should comply closely with RFC 7234 caching guidelines. It should:
* Extract and evaluate the `Expires` date timestamp when `max-age` is missing.
* Honor the `no-store` directive by entirely bypassing local disk persistence.
* Honor the `no-cache` directive by forcing an ETag validation handshake with the server before reusing assets.
* Ensure that if both headers are present, `max-age` overrides `Expires`.

### Current Behavior
The client processes headers using primitive string lookups (`strstr`) target-locked specifically onto the `max-age=` text pattern. If a web server responds with an `Expires` timestamp or strict behavioral control directives without explicitly defining a `max-age`, Defold overlooks them entirely, evaluating the payload as if no caching specifications exist.

### Affected Components
* `engine/dlib/src/dlib/http_client.cpp` — Intercepts and parses incoming server headers.
* `engine/dlib/src/dlib/http_cache.cpp` — Stores cache entries and runs freshness calculations.
* `engine/dlib/src/test/test_httpcache.cpp` — Manages the unit testing validation suite.

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
