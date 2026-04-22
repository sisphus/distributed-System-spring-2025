## [6.5840](http://nil.csail.mit.edu/6.5840/2025/index.html) - Spring 2025

# 6.5840 Lab 1: MapReduce

**[Collaboration policy](http://nil.csail.mit.edu/6.5840/2025/labs/collab.html)** // **[Submit lab](http://nil.csail.mit.edu/6.5840/2025/labs/submit.html)** // **[Setup Go](http://nil.csail.mit.edu/6.5840/2025/labs/go.html)** // **[Guidance](http://nil.csail.mit.edu/6.5840/2025/labs/guidance.html)** // **[Piazza](https://piazza.com/mit/spring2025/65840)**

------

### Introduction

In this lab you'll build a MapReduce system. You'll implement a worker process that calls application Map and Reduce functions and handles reading and writing files, and a coordinator process that hands out tasks to workers and copes with failed workers. You'll be building something similar to the [MapReduce paper](http://research.google.com/archive/mapreduce-osdi04.pdf). (Note: this lab uses "coordinator" instead of the paper's "master".)

### Getting started

You need to [setup Go](http://nil.csail.mit.edu/6.5840/2025/labs/go.html) to do the labs.

Fetch the initial lab software with [git](https://git-scm.com/) (a version control system). To learn more about git, look at the [Pro Git book](https://git-scm.com/book/en/v2) or the [git user's manual](http://www.kernel.org/pub/software/scm/git/docs/user-manual.html).

```
$ git clone git://g.csail.mit.edu/6.5840-golabs-2025 6.5840
$ cd 6.5840
$ ls
Makefile src
$
```

We supply you with a simple sequential mapreduce implementation in `src/main/mrsequential.go`. It runs the maps and reduces one at a time, in a single process. We also provide you with a couple of MapReduce applications: word-count in `mrapps/wc.go`, and a text indexer in `mrapps/indexer.go`. You can run word count sequentially as follows:

```
$ cd ~/6.5840
$ cd src/main
$ go build -buildmode=plugin ../mrapps/wc.go
$ rm mr-out*
$ go run mrsequential.go wc.so pg*.txt
$ more mr-out-0
A 509
ABOUT 2
ACT 8
...
```

`mrsequential.go` leaves its output in the file `mr-out-0`. The input is from the text files named `pg-xxx.txt`.

Feel free to borrow code from `mrsequential.go`. You should also have a look at `mrapps/wc.go` to see what MapReduce application code looks like.

For this lab and all the others, we might issue updates to the code we provide you. To ensure that you can fetch those updates and easily merge them using `git pull`, it's best to leave the code we provide in the original files. You can add to the code we provide as directed in the lab write-ups; just don't move it. It's OK to put your own new functions in new files.

### Your Job ([moderate/hard](http://nil.csail.mit.edu/6.5840/2025/labs/guidance.html))

Your job is to implement a distributed MapReduce, consisting of two programs, the coordinator and the worker. There will be just one coordinator process, and one or more worker processes executing in parallel. In a real system the workers would run on a bunch of different machines, but for this lab you'll run them all on a single machine. The workers will talk to the coordinator via RPC. Each worker process will, in a loop, ask the coordinator for a task, read the task's input from one or more files, execute the task, write the task's output to one or more files, and again ask the coordinator for a new task. The coordinator should notice if a worker hasn't completed its task in a reasonable amount of time (for this lab, use ten seconds), and give the same task to a different worker.

We have given you a little code to start you off. The "main" routines for the coordinator and worker are in `main/mrcoordinator.go` and `main/mrworker.go`; don't change these files. You should put your implementation in `mr/coordinator.go`, `mr/worker.go`, and `mr/rpc.go`.

Here's how to run your code on the word-count MapReduce application. First, make sure the word-count plugin is freshly built:

```
$ go build -buildmode=plugin ../mrapps/wc.go
```

In the `main` directory, run the coordinator.

```
$ rm mr-out*
$ go run mrcoordinator.go pg-*.txt
```

The `pg-*.txt` arguments to `mrcoordinator.go` are the input files; each file corresponds to one "split", and is the input to one Map task.

In one or more other windows, run some workers:

```
$ go run mrworker.go wc.so
```

When the workers and coordinator have finished, look at the output in `mr-out-*`. When you've completed the lab, the sorted union of the output files should match the sequential output, like this:

```
$ cat mr-out-* | sort | more
A 509
ABOUT 2
ACT 8
...
```

We supply you with a test script in `main/test-mr.sh`. The tests check that the `wc` and `indexer` MapReduce applications produce the correct output when given the `pg-xxx.txt` files as input. The tests also check that your implementation runs the Map and Reduce tasks in parallel, and that your implementation recovers from workers that crash while running tasks.

If you run the test script now, it will hang because the coordinator never finishes:

```
$ cd ~/6.5840/src/main
$ bash test-mr.sh
*** Starting wc test.
```

You can change `ret := false` to true in the Done function in `mr/coordinator.go` so that the coordinator exits immediately. Then:

```
$ bash test-mr.sh
*** Starting wc test.
sort: No such file or directory
cmp: EOF on mr-wc-all
--- wc output is not the same as mr-correct-wc.txt
--- wc test: FAIL
$
```

The test script expects to see output in files named `mr-out-X`, one for each reduce task. The empty implementations of `mr/coordinator.go` and `mr/worker.go` don't produce those files (or do much of anything else), so the test fails.

When you've finished, the test script output should look like this:

```
$ bash test-mr.sh
*** Starting wc test.
--- wc test: PASS
*** Starting indexer test.
--- indexer test: PASS
*** Starting map parallelism test.
--- map parallelism test: PASS
*** Starting reduce parallelism test.
--- reduce parallelism test: PASS
*** Starting job count test.
--- job count test: PASS
*** Starting early exit test.
--- early exit test: PASS
*** Starting crash test.
--- crash test: PASS
*** PASSED ALL TESTS
$
```

You may see some errors from the Go RPC package that look like

```
2019/12/16 13:27:09 rpc.Register: method "Done" has 1 input parameters; needs exactly three
```

Ignore these messages; registering the coordinator as an [RPC server](https://golang.org/src/net/rpc/server.go) checks if all its methods are suitable for RPCs (have 3 inputs); we know that `Done` is not called via RPC.

Additionally, depending on your strategy for terminating worker processes, you may see some errors of the form

```
2025/02/11 16:21:32 dialing:dial unix /var/tmp/5840-mr-501: connect: connection refused
```

It is fine to see a handful of these messages per test; they arise when the worker is unable to contact the coordinator RPC server after the coordinator has exited.



### A few rules:

- The map phase should divide the intermediate keys into buckets for `nReduce` reduce tasks, where `nReduce` is the number of reduce tasks -- the argument that `main/mrcoordinator.go` passes to `MakeCoordinator()`. Each mapper should create `nReduce` intermediate files for consumption by the reduce tasks.
- The worker implementation should put the output of the X'th reduce task in the file `mr-out-X`.
- A `mr-out-X` file should contain one line per Reduce function output. The line should be generated with the Go `"%v %v"` format, called with the key and value. Have a look in `main/mrsequential.go` for the line commented "this is the correct format". The test script will fail if your implementation deviates too much from this format.
- You can modify `mr/worker.go`, `mr/coordinator.go`, and `mr/rpc.go`. You can temporarily modify other files for testing, but make sure your code works with the original versions; we'll test with the original versions.
- The worker should put intermediate Map output in files in the current directory, where your worker can later read them as input to Reduce tasks.
- `main/mrcoordinator.go` expects `mr/coordinator.go` to implement a `Done()` method that returns true when the MapReduce job is completely finished; at that point, `mrcoordinator.go` will exit.
- When the job is completely finished, the worker processes should exit. A simple way to implement this is to use the return value from `call()`: if the worker fails to contact the coordinator, it can assume that the coordinator has exited because the job is done, so the worker can terminate too. Depending on your design, you might also find it helpful to have a "please exit" pseudo-task that the coordinator can give to workers.

### Hints

- The [Guidance page](http://nil.csail.mit.edu/6.5840/2025/labs/guidance.html) has some tips on developing and debugging.

- One way to get started is to modify `mr/worker.go`'s `Worker()` to send an RPC to the coordinator asking for a task. Then modify the coordinator to respond with the file name of an as-yet-unstarted map task. Then modify the worker to read that file and call the application Map function, as in `mrsequential.go`.

- The application Map and Reduce functions are loaded at run-time using the Go plugin package, from files whose names end in `.so`.

- If you change anything in the `mr/` directory, you will probably have to re-build any MapReduce plugins you use, with something like `go build -buildmode=plugin ../mrapps/wc.go`

- This lab relies on the workers sharing a file system. That's straightforward when all workers run on the same machine, but would require a global filesystem like GFS if the workers ran on different machines.

- A reasonable naming convention for intermediate files is `mr-X-Y`, where X is the Map task number, and Y is the reduce task number.

- The worker's map task code will need a way to store intermediate key/value pairs in files in a way that can be correctly read back during reduce tasks. One possibility is to use Go's `encoding/json` package. To write key/value pairs in JSON format to an open file:

  ```
    enc := json.NewEncoder(file)
    for _, kv := ... {
      err := enc.Encode(&kv)
  ```

  and to read such a file back:

  ```
    dec := json.NewDecoder(file)
    for {
      var kv KeyValue
      if err := dec.Decode(&kv); err != nil {
        break
      }
      kva = append(kva, kv)
    }
  ```

- The map part of your worker can use the `ihash(key)` function (in `worker.go`) to pick the reduce task for a given key.

- You can steal some code from `mrsequential.go` for reading Map input files, for sorting intermedate key/value pairs between the Map and Reduce, and for storing Reduce output in files.

- The coordinator, as an RPC server, will be concurrent; don't forget to lock shared data.

- Use Go's race detector, with `go run -race`. `test-mr.sh` has a comment at the start that tells you how to run it with `-race`. When we grade your labs, we will **not** use the race detector. Nevertheless, if your code has races, there's a good chance it will fail when we test it even without the race detector.

- Workers will sometimes need to wait, e.g. reduces can't start until the last map has finished. One possibility is for workers to periodically ask the coordinator for work, sleeping with `time.Sleep()` between each request. Another possibility is for the relevant RPC handler in the coordinator to have a loop that waits, either with `time.Sleep()` or `sync.Cond`. Go runs the handler for each RPC in its own thread, so the fact that one handler is waiting needn't prevent the coordinator from processing other RPCs.

- The coordinator can't reliably distinguish between crashed workers, workers that are alive but have stalled for some reason, and workers that are executing but too slowly to be useful. The best you can do is have the coordinator wait for some amount of time, and then give up and re-issue the task to a different worker. For this lab, have the coordinator wait for ten seconds; after that the coordinator should assume the worker has died (of course, it might not have).

- If you choose to implement Backup Tasks (Section 3.6), note that we test that your code doesn't schedule extraneous tasks when workers execute tasks without crashing. Backup tasks should only be scheduled after some relatively long period of time (e.g., 10s).

- To test crash recovery, you can use the `mrapps/crash.go` application plugin. It randomly exits in the Map and Reduce functions.

- To ensure that nobody observes partially written files in the presence of crashes, the MapReduce paper mentions the trick of using a temporary file and atomically renaming it once it is completely written. You can use `ioutil.TempFile` (or `os.CreateTemp` if you are running Go 1.17 or later) to create a temporary file and `os.Rename` to atomically rename it.

- `test-mr.sh` runs all its processes in the sub-directory `mr-tmp`, so if something goes wrong and you want to look at intermediate or output files, look there. Feel free to temporarily modify `test-mr.sh` to `exit` after the failing test, so the script does not continue testing (and overwrite the output files).

- `test-mr-many.sh` runs `test-mr.sh` many times in a row, which you may want to do in order to spot low-probability bugs. It takes as an argument the number of times to run the tests. You should not run several `test-mr.sh` instances in parallel because the coordinator will reuse the same socket, causing conflicts.

- Go RPC sends only struct fields whose names start with capital letters. Sub-structures must also have capitalized field names.

- When calling the RPC `call()` function, the reply struct should contain all default values. RPC calls should look like this:

  ```
    reply := SomeType{}
    call(..., &reply)
  ```

  without setting any fields of reply before the call. If you pass reply structures that have non-default fields, the RPC system may silently return incorrect values.

### No-credit challenge exercises

Implement your own MapReduce application (see examples in `mrapps/*`), e.g., Distributed Grep (Section 2.3 of the MapReduce paper).

Get your MapReduce coordinator and workers to run on separate machines, as they would in practice. You will need to set up your RPCs to communicate over TCP/IP instead of Unix sockets (see the commented out line in `Coordinator.server()`), and read/write files using a shared file system. For example, you can `ssh` into multiple [Athena cluster](http://kb.mit.edu/confluence/display/istcontrib/Getting+Started+with+Athena) machines at MIT, which use [AFS](http://kb.mit.edu/confluence/display/istcontrib/AFS+at+MIT+-+An+Introduction) to share files; or you could rent a couple AWS instances and use [S3](https://aws.amazon.com/s3/) for storage.



### Go

You'll implement all the labs in [Go](http://www.golang.org/). The Go web site contains lots of tutorial information. You should use Go 1.22 or any later version. You can check your Go version by running `go version`.

We recommend that you work on the labs on your own machine, so you can use the tools, text editors, etc. that you are already familiar with. Many editors have plug-ins for Go, e.g. the [Go extension](https://code.visualstudio.com/docs/languages/go) for VS Code. Some commercial IDEs like [GoLand](https://www.jetbrains.com/go/) have [free educational licenses](https://www.jetbrains.com/community/education/#students). We are happy to provide support over Piazza and in office hours to help you set up Go. The labs probably won't work on Athena.

#### macOS

You can use [Homebrew](https://brew.sh/) to install Go. After installing Homebrew, run `brew install go`.

#### Linux

Depending on your Linux distribution, you might be able to get an up-to-date version of Go from the package repository, e.g. by running `apt install golang`. Otherwise, you can manually install a binary from Go's website. First, make sure that you're running a 64-bit kernel (`uname -a` should mention "x86_64 GNU/Linux"), and then run:

```
$ wget -qO- https://go.dev/dl/go1.23.5.linux-amd64.tar.gz | sudo tar xz -C /usr/local
```

You'll need to make sure `/usr/local/go/bin` is on your `PATH`. You can do this by adding `export PATH=$PATH:/usr/local/go/bin` to your shell's init file ( commonly this is one of `.bashrc`, `.bash_profile` or `.zshrc`)

#### Windows

The labs are believed to work under Microsoft's WSL2 (Windows Subsystem for Linux, version 2).

To use WSL 2, first make sure you have the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) installed. Then add [Ubuntu 24.04 from the Microsoft Store](https://apps.microsoft.com/detail/9nz3klhxdjp5). Afterwards you should be able to launch Ubuntu Linux. Then you can follow the directions for Linux (above).

Make sure that you are running version 2 of WSL. WSL 1 does not work with the labs. To check, run `wsl -l -v `in a Windows terminal to confirm that WSL 2 and the correct Ubuntu version are installed.

# Lab guidance

## Hardness of assignments

Each lab task is tagged to indicate roughly how long we expect the task to take:

- Easy: A few hours.
- Moderate: ~ 6 hours (per week).
- Hard: More than 6 hours (per week). If you start late, your solution is unlikely to pass all tests.

Most of the labs require only a modest amount of code (perhaps a few hundred lines per lab part), but can be conceptually difficult and may require a good deal of thought and debugging. Some of the tests are difficult to pass.

Don't start a lab the night before it is due; it's more efficient to do the labs in several sessions spread over multiple days. Tracking down bugs in distributed systems is difficult, because of concurrency, crashes, and an unreliable network.

## Tips

- Do the [Online Go tutorial](http://tour.golang.org/) and consult [Effective Go](https://golang.org/doc/effective_go.html). See [Editors](https://golang.org/doc/editors.html) to set up your editor for Go.
- Use Go's [race detector](https://blog.golang.org/race-detector), with `go test -race`. Fix any races it reports.
- Read this [guide](https://thesquareplanet.com/blog/students-guide-to-raft/) for Raft specific advice.
- Advice on [locking](http://nil.csail.mit.edu/6.5840/2025/labs/raft-locking.txt) in labs.
- Advice on [structuring](http://nil.csail.mit.edu/6.5840/2025/labs/raft-structure.txt) your Raft lab.
- This [Diagram of Raft interactions](http://nil.csail.mit.edu/6.5840/2025/notes/raft_diagram.pdf) may help you understand code flow between different parts of the system.
- It may be helpful when debugging to insert print statements when a peer sends or receives a message, and collect the output in a file with `go test > out`. Then, by studying the trace of messages in the `out` file, you can identify where your implementation deviates from the desired behavior.
- Structure your debug messages in a consistent format so that you can use `grep` to search for specific lines in `out`.
- You might find `DPrintf` useful instead of calling `log.Printf` directly to turn printing on and off as you debug different problems.
- Learn about Go's `Printf`format strings: [Go format strings](https://golang.org/pkg/fmt/).
- You can use colors or columns to help you parse log output. [This post](https://blog.josejg.com/debugging-pretty/) explains one strategy.
- To learn more about git, look at the [Pro Git book](https://git-scm.com/book/en/v2) or the [git user's manual](http://www.kernel.org/pub/software/scm/git/docs/user-manual.html).

### Debugging

Efficient debugging takes experience. It helps to be systematic: form a hypothesis about a possible cause of the problem; collect evidence that might be relevant; think about the information you've gathered; repeat as needed. For extended debugging sessions it helps to keep notes, both to accumulate evidence and to remind yourself why you've discarded specific earlier hypotheses.

One approach is to progessively narrow down the specific point in time at which things start to go wrong. You could add code at various points in the execution that tests whether the system has reached the bad state. Or your code could print messages with relevant state at various points; collect the output in a file, and look through the file for the first point where things look wrong.

The Raft labs involve events, such as RPCs arriving or timeouts expiring or peers failing, that may occur at times you don't expect, or may be interleaved in unexpected orders. For example, one peer may decide to become a candidate while another peer thinks it is already the leader. It's worth thinking through the "what can happen next" possibilities. For example, when your Raft code releases a mutex, the very next thing that happens (before the next line of code is executed!) might be the delivery (and processing) of an RPC request, or a timeout going off. Add Print statements to find out the actual order of events during execution.

The Raft paper's Figure 2 must be followed fairly exactly. It is easy to miss a condition that Figure 2 says must be checked, or a state change that it says must be made. If you have a bug, re-check that all of your code adheres closely to Figure 2.

As you're writing code (i.e., before you have a bug), it may be worth adding explicit checks for conditions that the code assumes to be true, perhaps using Go's [panic](https://gobyexample.com/panic). Such checks may help detect situations where later code unwittingly violates the assumptions.

If code used to work, but now it doesn't, maybe a change you've recently made is at fault.

The bug is often in the very last place you think to look, so be sure to look even at code you feel certain is correct.

The TAs are happy to help you think about your code during office hours, but you're likely to get the most mileage out of limited office hour time if you've already dug as deep as you can into the situation.