## Review Schedule

### 2026-04-21

- Topic: Distributed system definition
- Reason: User gave mostly correct answer but omitted explicit cooperation/coordination.
- Review timing: Same day, quick recall.
- Review prompt: Define a distributed system using the words "computers", "cooperate", and "service".
- Status: Scheduled.

- Topic: Partial failure
- Reason: User understood uncertainty but needs sharper distinction between partial failure and single-machine crash.
- Review timing: Same day, quick recall.
- Review prompt: Give two possible explanations for why a server did not reply to a request.
- Status: Scheduled.

- Topic: Reasons for distributed systems
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Name two benefits and one cost of using multiple computers for one service.
- Status: Scheduled.

- Topic: Replication and consistency
- Reason: User understood the core tradeoff but should polish the explanation.
- Review timing: 3 days.
- Review prompt: Explain how a stale read can happen when one replica has been updated but another has not.
- Status: Scheduled.

- Topic: Map vs Shuffle
- Reason: User gave the grouped shuffle output when asked for raw Map output.
- Review timing: Same day.
- Review prompt: For input "x y x", list the Map output first, then the Shuffle output.
- Status: Scheduled.

- Topic: Shuffle network communication
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Explain why reduce workers may need to fetch data from every map worker.
- Status: Scheduled.

- Topic: MapReduce coordinator
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Name two task states the coordinator tracks, and explain why tracking them matters.
- Status: Scheduled.

- Topic: Map task crash recovery
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Explain why completed map output can still be lost after a worker crash.
- Status: Scheduled.

- Topic: MapReduce determinism
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Explain why the same Map input must always produce the same intermediate output.
- Status: Scheduled.

- Topic: MapReduce stragglers
- Reason: User understood reducer waiting but should generalize to all required tasks in a stage.
- Review timing: 3 days.
- Review prompt: Explain why a single slow map task can delay the start or completion of reduce work.
- Status: Scheduled.

- Topic: MapReduce data locality
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Explain how running Map near its input reduces network bottlenecks.
- Status: Scheduled.

- Topic: MapReduce restrictions
- Reason: User understood shared-state risk but should connect restrictions to safe retries and parallel execution.
- Review timing: 3 days.
- Review prompt: Explain why deterministic independent tasks make MapReduce fault recovery easier.
- Status: Scheduled.

### 2026-04-22

- Topic: Goroutines and I/O concurrency
- Reason: User answered correctly.
- Review timing: 1 week.
- Review prompt: Explain why overlapping network waits can make a concurrent web crawler faster than a serial crawler.
- Status: Scheduled.

- Topic: Race conditions in shared state
- Reason: User identified shared-data danger but needs sharper check-then-set reasoning.
- Review timing: Same day.
- Review prompt: Show a two-goroutine timeline where both goroutines read `fetched[url] == false` and both fetch the same URL.
- Status: Scheduled.
