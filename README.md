# ExamFlow — OS Algorithm Visualizer

**Selected scenario:** Problem 4, Online Examination and Result Processing System. This is a beginner-friendly, browser-based Operating Systems mini-project built with HTML, CSS and JavaScript.

## Features

1. **CPU Scheduling — FCFS:** process timeline, completion time, waiting time, turnaround time and averages. Validates process rows and duplicate names.
2. **Producer–Consumer:** bounded FIFO buffer with full/empty checks and a visible semaphore-state model (`empty`, `full`, `mutex`). Each click shows the conceptual `P`/`V` sequence and updated counters.
3. **Deadlock Avoidance — Banker’s Algorithm:** validates matrix dimensions and resource values, calculates `Need = Maximum − Allocation`, and reports a safe sequence when one exists.
4. **Memory Management — First Fit / Best Fit:** compares allocation strategies, reports failed allocations and shows remaining free space.

## Run on Windows

1. Extract the ZIP archive.
2. Open the `os-exam-visualizer` folder.
3. Double-click `index.html` to open it in Chrome, Edge or Firefox.
4. No Python, Node.js, external packages, server or internet connection is required for the local demo.

## Suggested test cases

- **FCFS:** enter `P1,0,4`, `P2,1,3`, `P3,2,2`. Expected waiting times: 0, 3, 5; turnaround times: 4, 6, 7; averages: 2.67 and 5.67.
- **Producer–Consumer:** fill the buffer, attempt another produce, consume to empty, attempt another consume, and check FIFO order. Confirm `empty + full = capacity` after each operation.
- **Banker:** run the preset safe case, then set Available to `0,0,0` to test an unsafe state. Also test Maximum < Allocation and mismatched matrix row counts.
- **Memory:** use blocks `100,500,200,300,600` and processes `212,417,112,426`; compare First Fit and Best Fit. Also test a process larger than every remaining block.
- **Input validation:** try blank fields, negative values, decimals where integers are required, and invalid buffer capacity.

## Important technical limitation

The Producer–Consumer module is a **single-threaded educational simulation**. It displays the conceptual semaphore counters and the order of `P(empty)`, `P(mutex)`, enqueue/dequeue, `V(mutex)` and `V(full/empty)` operations, but it does not create real concurrent threads or call OS-level semaphore/mutex APIs. Describe it accurately in the report and viva. The other modules are algorithm simulations, not a real OS kernel or live examination platform.

## GitHub submission

1. Sign in at https://github.com/ and create a repository named `examflow-os-visualizer`.
2. Upload `index.html`, `README.md`, and `CASE_STUDY_REPORT_OUTLINE.md`; commit the files.
3. If desired, enable **Settings → Pages → Deploy from a branch → `main` → `/(root)`** for a live demo.
4. Open the repository link in a private/incognito window to confirm public access.
5. Submit the **repository URL** through the Google Form provided by your teacher. The live demo URL is supplementary.

## Final submission checklist

- [ ] Test all four modules with the suggested test cases.
- [ ] Add screenshots of actual results to your report.
- [ ] Complete the case study within the assignment's five-page maximum.
- [ ] Add your name, UID, college details and signature as required.
- [ ] Confirm the repository contains the source code and documentation.
- [ ] Submit the repository URL in the teacher's Google Form.
