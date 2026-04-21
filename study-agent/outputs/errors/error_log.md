## 2026-04-21 - Distributed Systems

- Topic: MIT 6.5840 Lecture 1 - Distributed System Definition
- Question: What makes a system distributed instead of just being a normal single-computer program?
- User answer: "because it is a group of many computers aimed to provide services"
- Correct reasoning: A distributed system is multiple computers cooperating and coordinating to provide what appears to users as one service.
- Error type: Minor missing precision.
- Fix strategy: When defining distributed systems, include both parts: "multiple computers" and "cooperate as one service."

## 2026-04-21 - Partial Failure

- Topic: MIT 6.5840 Lecture 1 - Partial Failure
- Question: Why is partial failure harder than a normal single-computer crash?
- User answer: "I think the reason is that distributed systems always have uncertainty problems which is hard to detect like single computer"
- Correct reasoning: Partial failure is harder because one part can fail while the rest continues, and a missing reply may mean a crash, delay, lost request, or lost response.
- Error type: Minor missing precision.
- Fix strategy: Emphasize that distributed systems must keep making decisions even when they cannot immediately distinguish slow components from failed components.

## 2026-04-21 - MapReduce Data Flow

- Topic: MIT 6.5840 Lecture 1 - Map vs Shuffle
- Question: In MapReduce word count, what does the Map function output for input "a b a"?
- User answer: "a->[1,1] , b ->[1]"
- Correct reasoning: The Map function emits individual key-value pairs: `(a, 1)`, `(b, 1)`, `(a, 1)`. The shuffle step later groups them as `a -> [1, 1]` and `b -> [1]`.
- Error type: Concept boundary misunderstanding.
- Fix strategy: Separate the three stages explicitly: Map emits pairs, Shuffle groups by key, Reduce combines values.
