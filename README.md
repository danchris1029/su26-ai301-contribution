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
1. Downloading MSVC C++ toolchain
2. Wiring up PowerShell environment variables so the build scripts could actually find the compilers.
3.
4. Tracking down and independently compiling the missing testmain.lib dependency so our HTTP cache test suite would link properly.
Gauging between AI usage, and analyzing how to balance personal understanding and supplemented work.

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

The underlying limitation stems from structural omissions within both the data models and string tokenizers. The CacheEntry object lacks tracking attributes for holding explicit calendar expirations or functional constraint markers. To fix this, the engine needs an expanded parsing pipeline that safely processes multiple string date formats without risking runtime stability across target execution environments.

### Proposed Solution

Add a robust date utility, ParseHttpDate, inside the network lifecycle flow to cleanly transform various web timestamp expressions into standardized Unix time_t values. Update the core CacheEntry storage model to maintain these values alongside explicit boolean flags for no-store and no-cache. Finally, adjust the engine’s decision loop (IsCacheFresh) to execute sequential priority validations matching RFC criteria.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Upgrade dlib's underlying HTTP layer to support Expires, no-cache, no-store, and must-revalidate functionalities, ensuring correct header hierarchy.

**Match:** Mirror the pattern currently employed for processing max-age values and ETag string parameters.

**Plan:** [Step-by-step implementation plan]
  1. Build a static helper utility ParseHttpDate() inside http_client.cpp to correctly interpret RFC 1123, RFC 850, and ANSI C date layouts using strptime.
  
  2. Implement an enhanced configuration extractor (ParseCacheControl) to parse behavioral flags.
  
  3. Expand the CacheEntry struct definition within http_cache.cpp to include fields for m_ExpiresAt, m_NoCache, and m_NoStore.
  
  4. Modify IsCacheFresh() to assess parameters sequentially: checking max-age boundaries first, then falling back to m_ExpiresAt.
  
  5. Short-circuit storage operations inside http_cache.cpp if a no-store instruction is encountered.
  
  6. Introduce automated test coverage loops into test_httpcache.cpp.

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
