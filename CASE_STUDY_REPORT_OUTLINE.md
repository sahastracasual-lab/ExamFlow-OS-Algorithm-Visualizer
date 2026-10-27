# Assignment No. 3 — Case Study Report Outline

**Title:** OS Resource Management in an Online Examination and Result Processing System  
**Student:** Sahastranshu Shandilya  
**UID:** 24003113  
**College:** St. Vincent Pallotti College of Engineering and Technology, Nagpur  
**Subject:** Operating Systems  
**Selected problem:** Problem 4 — Online Examination and Result Processing System

> The assignment sheet specifies a maximum of five pages. This is a scaffold, not a completed report. Add screenshots from your own final program, include your signature where required, and verify all output values before submission.

## 1. Problem statement and context
An online examination platform handles student logins, question-paper delivery, answer submissions, automatic evaluation and result generation. During peak periods, many tasks compete for CPU time, memory and shared resources. ExamFlow uses this scenario to illustrate four OS concepts.

## 2. Objectives, constraints and assumptions
**Objectives:** schedule jobs and calculate timing metrics; coordinate answer-submission jobs; identify safe resource-allocation states; and allocate memory while displaying remaining free space.

**Assumptions:** all values are illustrative; FCFS is used for CPU scheduling; the bounded buffer has a finite capacity; Banker’s Algorithm uses three resource types; memory allocation uses First Fit or Best Fit. This is a browser-based algorithm visualizer, not a real operating system or examination service.

## 3. Operating Systems concepts
### 3.1 CPU scheduling — FCFS
Processes execute in arrival-time order. Waiting Time = Start Time − Arrival Time. Turnaround Time = Completion Time − Arrival Time. Include the Gantt-style chart and the actual process table.

### 3.2 Process synchronization — Producer–Consumer
The producer represents answer submissions and the consumer represents evaluation jobs. The bounded FIFO buffer prevents insertion when full and removal when empty. The interface shows conceptual `empty`, `full` and `mutex` semaphore state and illustrates the `P`/`V` operation order. **Limitation:** the browser demo is single-threaded and simulates semaphore behaviour; it does not run true concurrent threads or OS semaphore APIs. State this clearly.

### 3.3 Deadlock avoidance — Banker’s Algorithm
`Need[i][j] = Maximum[i][j] − Allocation[i][j]`. Start with `Work = Available`; find an unfinished process whose Need is no greater than Work, mark it finished, and add its Allocation to Work. If every process finishes, the state is safe and the order is a safe sequence. Include the Need matrix and results for safe and unsafe test cases.

### 3.4 Memory management — First Fit and Best Fit
First Fit selects the first remaining block large enough for a process. Best Fit selects the smallest remaining block that can accommodate it. The program splits a block by reducing its remaining capacity. Include a comparison table and explain that the simplified model does not simulate address translation or all physical-memory behaviour.

## 4. Integration in the online examination scenario
FCFS schedules login, submission and evaluation jobs. Producer–Consumer models answer submissions waiting for evaluation. Banker’s Algorithm checks whether the declared resource state admits a safe completion order. First Fit and Best Fit illustrate memory allocation to active tasks. Explain that these modules are connected by a common scenario but run as separate simulations.

## 5. Test cases and execution results
Insert screenshots and record actual results. Suggested tests:
- FCFS: `P1,0,4`, `P2,1,3`, `P3,2,2`; verify average waiting time 2.67 and average turnaround time 5.67.
- Producer–Consumer: normal produce/consume, full buffer, empty buffer, FIFO order and semaphore counter relation `empty + full = capacity`.
- Banker: preset safe case; unsafe case with Available `[0,0,0]`; invalid Maximum/Allocation relationship; mismatched matrix sizes.
- Memory: compare First Fit and Best Fit with blocks `[100,500,200,300,600]` and processes `[212,417,112,426]`; test insufficient memory.

## 6. Conclusion
Summarize how scheduling, synchronization, deadlock avoidance and memory allocation address different parts of the simulated examination workload. Acknowledge the simplified simulation model and the absence of actual parallel execution.

## Viva preparation
- Define waiting time and turnaround time.
- Explain producer, consumer, bounded buffer, `empty`, `full` and `mutex`.
- Explain why the current browser model is not real concurrent synchronization.
- Distinguish a safe state from a deadlock.
- Explain the Need matrix and safe-sequence search.
- Compare First Fit and Best Fit and describe fragmentation carefully.
