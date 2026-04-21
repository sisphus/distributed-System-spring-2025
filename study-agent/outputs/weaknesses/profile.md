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
