## Topic: Distributed Systems

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: What makes a system distributed instead of just being a normal single-computer program?
- User answer: "because it is a group of many computers aimed to provide services"
- Assessment: Mostly correct.
- Strength: Identified multiple computers and service goal.
- Weakness: Needs to emphasize cooperation/coordination among computers as one service.
- Error type: Minor missing precision.
- Fix strategy: Reinforce "many computers cooperating" with examples.
- Status: Watch.

## Topic: Partial Failure

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: Why is partial failure harder than a normal single-computer crash?
- User answer: "I think the reason is that distributed systems always have uncertainty problems which is hard to detect like single computer"
- Assessment: Mostly correct.
- Strength: Identified uncertainty as the main difficulty.
- Weakness: Needs to state that one component can fail while others keep running, and network delay can look like failure.
- Error type: Minor missing precision.
- Fix strategy: Reinforce the difference between total crash and partial failure using message/no-reply examples.
- Status: Watch.

## Topic: Reasons for Distributed Systems

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: Give two reasons why engineers build distributed systems, even though they are harder to design.
- User answer: "one reason is that more computers can increase capacity to accelerate jobs, and another reason is that fault tolerance can be applied when some machines fail"
- Assessment: Correct.
- Strength: Clearly identified capacity through parallelism and fault tolerance.
- Weakness: None detected for this concept.
- Error type: None.
- Fix strategy: Continue to tradeoffs among capacity, fault tolerance, consistency, and performance.
- Status: Understood.

## Topic: Replication and Consistency Tradeoff

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: Why can replication improve fault tolerance but make consistency harder?
- User answer: "replication improve fault tolerance by adding more machines stored the same data, as a result, when updating new data , machines have to wait to be updated so that when one may read stale data in some old data machine"
- Assessment: Correct with minor wording imprecision.
- Strength: Identified duplicate data for fault tolerance and stale reads as the consistency problem.
- Weakness: Needs cleaner phrasing around coordination: replicas must either all be updated before reads, or the system risks reading stale data.
- Error type: Minor missing precision.
- Fix strategy: Reinforce the write/read sequence with a two-replica example.
- Status: Watch.

## Topic: MapReduce Data Flow

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: In MapReduce word count, what does the Map function output for input "a b a"?
- User answer: "a->[1,1] , b ->[1]"
- Assessment: Partially correct.
- Strength: Correctly understands that values are grouped by key eventually.
- Weakness: Confuses raw Map output with the Shuffle/grouping output.
- Error type: Concept boundary misunderstanding.
- Fix strategy: Re-teach the sequence Map emits individual pairs, then Shuffle groups pairs by key, then Reduce combines values.
- Follow-up answer: For input "x y x", user correctly gave raw Map output `(x,1) (y,1) (x,1)`.
- Status: Improving.

## Topic: Shuffle Network Communication

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: Why does the Shuffle step often require network communication in MapReduce?
- User answer: "I think the main reason is that Shuffle need aggregate the data from map workers which are distributed in different machines, in that network communication is inevitable."
- Assessment: Correct.
- Strength: Correctly identified that intermediate map outputs are distributed across machines and must be gathered/grouped for reduce.
- Weakness: None detected.
- Error type: None.
- Fix strategy: Continue to coordinator and fault recovery.
- Status: Understood.

## Topic: MapReduce Coordinator

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: What are two things the MapReduce coordinator is responsible for?
- User answer: "mapreduce coordinator is responsible for tracking and assigning tasks to works, such as tracking which one are finished and runing or failing"
- Assessment: Correct.
- Strength: Correctly identified task assignment and tracking task/worker status.
- Weakness: None detected.
- Error type: None.
- Fix strategy: Continue to worker crash recovery.
- Status: Understood.

## Topic: MapReduce Worker Crash Recovery

- Date: 2026-04-21
- Source: MIT 6.5840 Lecture 1
- Check question: Why does MapReduce need to re-run a completed Map task if the worker that ran it crashes before reducers fetch its output?
- User answer: "due to the fact that the previous woker crached, coodinator have to assign new worker to restart task so that it can generate new intermediate data from beginning without losing anything important"
- Assessment: Correct.
- Strength: Correctly identified reassigning the map task to regenerate intermediate data.
- Weakness: None detected conceptually.
- Error type: None.
- Fix strategy: Continue to deterministic Map/Reduce functions.
- Status: Understood.

## Topic: MapReduce Determinism

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 1
- Check question: Why is randomness dangerous inside a Map function?
- User answer: "because it is a non-deterministic process, reruning them can produce different data which is not accurate."
- Assessment: Correct.
- Strength: Correctly identified that rerunning a task can produce different outputs if randomness is used.
- Weakness: None detected.
- Error type: None.
- Fix strategy: Continue to stragglers and load balance.
- Status: Understood.

## Topic: MapReduce Stragglers

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 1
- Check question: Why can one slow worker delay the entire MapReduce job?
- User answer: "because in last step, reduce function have to aggregate all map data to finalise the output, if one straggler delays, the final step have to wait."
- Assessment: Correct with minor scope limitation.
- Strength: Correctly identified that downstream work may wait for the slowest needed task.
- Weakness: Should generalize beyond reduce: a job stage cannot complete until all required tasks in that stage finish.
- Error type: Minor missing precision.
- Fix strategy: Reinforce "slowest required task controls completion time" using map and reduce examples.
- Status: Watch.

## Topic: MapReduce Data Locality

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 1
- Check question: Why does MapReduce try to run a Map task on a machine that already stores its input data?
- User answer: "it can avoid unnecessary network traffic,accelerating the process."
- Assessment: Correct.
- Strength: Correctly identified reduced network traffic and better performance.
- Weakness: None detected.
- Error type: None.
- Fix strategy: Continue to MapReduce restrictions and tradeoffs.
- Status: Understood.

## Topic: MapReduce Restrictions

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 1
- Check question: Why does MapReduce restrict programmers to the Map/Reduce pattern instead of allowing arbitrary communication between tasks?
- User answer: "because if it does, it can produce inconsistent output when arbitrary communication violate the shared state"
- Assessment: Correct with minor wording imprecision.
- Strength: Correctly identified that arbitrary communication/shared state can cause inconsistent outputs.
- Weakness: Needs cleaner phrasing: independent deterministic tasks make retries, parallelism, and backup execution safe.
- Error type: Minor missing precision.
- Fix strategy: Reinforce the link between restrictions and safe reruns/fault tolerance.
- Status: Watch.

## Topic: Goroutines and I/O Concurrency

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 2
- Check question: Why can goroutines make a web crawler faster than a serial crawler, even if each individual page fetch still takes the same amount of time?
- User answer: "because goroutines can run in parrallel which network waiting overlaps"
- Assessment: Correct.
- Strength: Correctly identified that goroutines allow parallel/concurrent fetches and overlap network waiting time.
- Weakness: None detected conceptually.
- Error type: None.
- Fix strategy: Continue to race conditions and shared-memory safety.
- Status: Understood.

## Topic: Race Conditions in Shared State

- Date: 2026-04-22
- Source: MIT 6.5840 Lecture 2
- Check question: Why is checking `fetched[url] == false` and later setting `fetched[url] = true` unsafe if two goroutines can do it at the same time?
- User answer: "because there are multiple threads, they can modify the same old data many times or fetch the stale data"
- Assessment: Partially correct.
- Strength: Correctly identified that multiple threads/goroutines can interact with the same shared data and act on outdated information.
- Weakness: Needs sharper explanation of the check-then-set interleaving: both goroutines can read `false` before either writes `true`, so both decide to fetch.
- Error type: Minor concept precision issue.
- Fix strategy: Re-teach race as timing-dependent interleaving, not just stale data, using a two-goroutine timeline.
- Status: Active.
