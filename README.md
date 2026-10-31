# 🎓 ExamOS — Online Examination & Result Processing System
> **An Interactive Operating Systems Simulation & Academic Case Study Project**  
> Modeling how modern Operating Systems handle peak student answer submission bursts without deadlocks, race conditions, CPU starvation, or memory fragmentation.

---

## 🌟 Overview & Real-World Motivation

During high-stakes online examinations (e.g., university semester finals, national entrance exams), thousands of candidates submit their final answer sheets within the last 60 seconds before the countdown timer hits zero. Monolithic, unsynchronized server architectures collapse under this workload due to four classic Operating System failure modes:

1. **Race Conditions & Packet Loss:** Thousands of concurrent HTTP client threads modifying shared result buffers simultaneously without synchronization.
2. **CPU Starvation & Convoy Delays:** Heavy descriptive evaluation tasks (plagiarism detection, code compilation) monopolizing CPU cores, blocking short objective MCQ grading.
3. **Circular Wait Deadlocks:** Worker threads holding database write locks while competing for grading threads and PDF rendering slots.
4. **Dynamic Memory Fragmentation:** Variable-sized student answer payloads (80 KB to 500 KB) continuously allocated and released, creating isolated holes that reject large payloads despite abundant total free RAM.

**ExamOS** is a zero-dependency, pure vanilla web application (HTML5, CSS3, ES6+ JavaScript) and formal academic case study that implements, visualizes, and benchmarks OS kernel solutions to each failure mode.

---

## 🏛 Architecture & Project Layout

```
c:\OS Mini Project\
├── index.html                 # Main dashboard layout with 5-tab cyber-academic navigation
├── style.css                  # Obsidian/slate design system, glassmorphism, glowing status badges
├── app.js                     # Tab switcher, view controller, module registration, shared utilities
├── js/
│   ├── producer-consumer.js   # Module 1: Thread synchronization, semaphores, bounded buffer
│   ├── cpu-scheduler.js       # Module 2: CPU scheduling (RR, FCFS, SJF, SRTF, Priority) & Gantt
│   ├── bankers-algorithm.js   # Module 3: Dijkstra's Banker's algorithm & safety check evaluator
│   ├── memory-manager.js      # Module 4: Dynamic contiguous memory allocation & compaction
│   └── integrated-pipeline.js # Module 5: End-to-end 5-stage live simulation pipeline
├── generate_pdf.py            # Python ReportLab script generating a formal academic PDF report
├── CASE_STUDY_REPORT.md       # University-level formal case study report with proofs & benchmarks
└── README.md                  # Comprehensive documentation, benchmark workloads & traces
```

---

## 🔬 Core OS Modules & Mathematical Traces

### Module 1: Producer-Consumer (Bounded Buffer Submission Queue)
- **Concept:** Student submission portals act as **Producers**; background automated evaluation worker threads act as **Consumers**.
- **Shared Bounded Buffer:** Circular ring of capacity $N$ (default $N = 8$).
- **Synchronization Primitives:**
  - `mutex` (Binary Semaphore, initial = $1$): Ensures mutual exclusion over buffer array access.
  - `empty` (Counting Semaphore, initial = $N = 8$): Tracks available slots for incoming answer payloads.
  - `full` (Counting Semaphore, initial = $0$): Tracks queued answer payloads waiting for grading.
- **Invariants:**
  $$\text{empty} + \text{full} = N$$
  $$\text{mutex} \in \{0, 1\}$$
- **Controls & Visuals:** Real-time buffer slot visualization (`[0]` to `[7]`), write and read head indicators, single produce/consume actions, auto-simulation sliders, and "⚡ Peak Submission Burst (5 Submissions)" button.

---

### Module 2: CPU Scheduling Engine & Interactive Gantt Chart
- **Preset Examination Tasks:**
  - $P_0$: MCQ Objective Scoring (Arrival: 0 ms, Burst: 5 ms, Priority: 2, Memory: 212 MB)
  - $P_1$: Plagiarism & Similarity Scan (Arrival: 1 ms, Burst: 3 ms, Priority: 1, Memory: 417 MB)
  - $P_2$: Descriptive Code Evaluation (Arrival: 2 ms, Burst: 8 ms, Priority: 3, Memory: 112 MB)
  - $P_3$: Percentile & Rank Computation (Arrival: 3 ms, Burst: 2 ms, Priority: 1, Memory: 426 MB)
  - $P_4$: PDF Result Card Generation (Arrival: 4 ms, Burst: 6 ms, Priority: 2, Memory: 90 MB)

#### Detailed Round Robin ($q = 2\text{ ms}$) Execution Trace

| Time Window | Process Executing | Time Slice Used | Remaining Burst | Ready Queue State |
| :---: | :---: | :---: | :---: | :--- |
| **0 – 2 ms** | $P_0$ | 2 ms | 3 ms | Incoming: $P_1$ (at 1 ms). Queue: $[P_1, P_0]$ |
| **2 – 4 ms** | $P_1$ | 2 ms | 1 ms | Incoming: $P_2$ (at 2 ms), $P_3$ (at 3 ms). Queue: $[P_0, P_2, P_3, P_1]$ |
| **4 – 6 ms** | $P_0$ | 2 ms | 1 ms | Incoming: $P_4$ (at 4 ms). Queue: $[P_2, P_3, P_1, P_4, P_0]$ |
| **6 – 8 ms** | $P_2$ | 2 ms | 6 ms | Queue: $[P_3, P_1, P_4, P_0, P_2]$ |
| **8 – 10 ms** | $P_3$ | 2 ms | 0 ms (**Finished**) | $P_3$ completes at $t=10$. Queue: $[P_1, P_4, P_0, P_2]$ |
| **10 – 11 ms** | $P_1$ | 1 ms | 0 ms (**Finished**) | $P_1$ completes at $t=11$. Queue: $[P_4, P_0, P_2]$ |
| **11 – 13 ms** | $P_4$ | 2 ms | 4 ms | Queue: $[P_0, P_2, P_4]$ |
| **13 – 14 ms** | $P_0$ | 1 ms | 0 ms (**Finished**) | $P_0$ completes at $t=14$. Queue: $[P_2, P_4]$ |
| **14 – 16 ms** | $P_2$ | 2 ms | 4 ms | Queue: $[P_4, P_2]$ |
| **16 – 18 ms** | $P_4$ | 2 ms | 2 ms | Queue: $[P_2, P_4]$ |
| **18 – 20 ms** | $P_2$ | 2 ms | 2 ms | Queue: $[P_4, P_2]$ |
| **20 – 22 ms** | $P_4$ | 2 ms | 0 ms (**Finished**) | $P_4$ completes at $t=22$. Queue: $[P_2]$ |
| **22 – 24 ms** | $P_2$ | 2 ms | 0 ms (**Finished**) | $P_2$ completes at $t=24$. |

#### Scheduling Results Table

| Task ID | Arrival ($A_i$) | Burst ($B_i$) | Priority | Completion ($C_i$) | Turnaround ($TAT = C_i - A_i$) | Waiting ($WT = TAT - B_i$) | Norm. TAT ($TAT / B_i$) |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **$P_0$** | 0 ms | 5 ms | 2 | 14 ms | 14 ms | 9 ms | 2.80 |
| **$P_1$** | 1 ms | 3 ms | 1 | 11 ms | 10 ms | 7 ms | 3.33 |
| **$P_2$** | 2 ms | 8 ms | 3 | 24 ms | 22 ms | 14 ms | 2.75 |
| **$P_3$** | 3 ms | 2 ms | 1 | 10 ms | 7 ms | 5 ms | 3.50 |
| **$P_4$** | 4 ms | 6 ms | 2 | 22 ms | 18 ms | 12 ms | 3.00 |

- **Average Turnaround Time:** $\frac{14 + 10 + 22 + 7 + 18}{5} = \mathbf{14.20\text{ ms}}$
- **Average Waiting Time:** $\frac{9 + 7 + 14 + 5 + 12}{5} = \mathbf{9.40\text{ ms}}$
- **CPU Utilization:** $\frac{24}{24} \times 100\% = \mathbf{100.0\%}$
- **Starvation Risk:** **Zero** (Round Robin guarantees fair quantum distribution).

---

### Module 3: Deadlock Avoidance (Dijkstra's Banker's Algorithm)
- **Examination Resources:**
  - $R_1$: Auto-Grading Engine Threads (Total: 10)
  - $R_2$: Database Write Locks on Result Ledger (Total: 5)
  - $R_3$: PDF Generator Slots (Total: 7)
- **Initial Available Vector:** $V = [3, 3, 2]$
- **Matrices ($5 \times 3$):**

$$\text{Allocation} = \begin{pmatrix} 0 & 1 & 0 \\ 2 & 0 & 0 \\ 3 & 0 & 2 \\ 2 & 1 & 1 \\ 0 & 0 & 2 \end{pmatrix}, \quad \text{Max} = \begin{pmatrix} 7 & 5 & 3 \\ 3 & 2 & 2 \\ 9 & 0 & 2 \\ 2 & 2 & 2 \\ 4 & 3 & 3 \end{pmatrix}, \quad \text{Need} = \begin{pmatrix} 7 & 4 & 3 \\ 1 & 2 & 2 \\ 6 & 0 & 0 \\ 0 & 1 & 1 \\ 4 & 3 & 1 \end{pmatrix}$$

#### Step-by-Step Safety Algorithm Verification

| Step | Candidate Process | Condition Check ($\text{Need}_i \le \text{Work}$) | Result | Resources Released ($\text{Alloc}_i$) | New Work Vector |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **0** | — | Initial state | — | — | $\text{Work} = [3, 3, 2]$ |
| **1** | **$P_1$** | $[1, 2, 2] \le [3, 3, 2]$ | **Satisfied** | $[2, 0, 0]$ | $\text{Work} = [5, 3, 2]$ |
| **2** | **$P_3$** | $[0, 1, 1] \le [5, 3, 2]$ | **Satisfied** | $[2, 1, 1]$ | $\text{Work} = [7, 4, 3]$ |
| **3** | **$P_4$** | $[4, 3, 1] \le [7, 4, 3]$ | **Satisfied** | $[0, 0, 2]$ | $\text{Work} = [7, 4, 5]$ |
| **4** | **$P_0$** | $[7, 4, 3] \le [7, 4, 5]$ | **Satisfied** | $[0, 1, 0]$ | $\text{Work} = [7, 5, 5]$ |
| **5** | **$P_2$** | $[6, 0, 0] \le [7, 5, 5]$ | **Satisfied** | $[3, 0, 2]$ | $\text{Work} = [10, 5, 7]$ |

$$\text{Safe Sequence: } \mathbf{\langle P_1 \to P_3 \to P_4 \to P_0 \to P_2 \rangle}$$

The system is mathematically proven to be in a **Safe State**, preventing any possible circular wait deadlock.

---

### Module 4: Dynamic Contiguous Memory Allocation & Compaction
- **Memory Pool:** $1024\text{ KB}$ Physical RAM (64 KB Kernel, 960 KB User Space).
- **Strategies Supported:**
  - **First Fit:** Allocates from the lowest-address hole large enough.
  - **Best Fit:** Allocates from the smallest free hole large enough (minimizes wasted slack).
  - **Worst Fit:** Allocates from the largest free hole.
- **Defragmentation via Compaction:**
  Coalesces isolated free holes into one continuous free block by sliding active payload blocks toward lower physical memory addresses, resetting the partition map without data loss.

---

### Module 5: Integrated Live 5-Stage Pipeline
Simulates end-to-end processing of submissions traversing all 4 OS subsystems sequentially:
1. **Stage 1 (Ingestion):** Semaphore verification and buffering (`SUB-#...`).
2. **Stage 2 (CPU Dispatch):** Round Robin execution slices.
3. **Stage 3 (Deadlock Gate):** Banker's algorithm safety validation.
4. **Stage 4 (Memory Allocation):** Best Fit contiguous RAM allocation.
5. **Stage 5 (Publication):** Final grade computed, result card published, resources released.

---

## 🚀 Quickstart & Local Installation

ExamOS is completely self-contained with **zero external dependencies** (no Node.js, no React, no build steps required).

### Option 1: Direct Browser Launch
Simply double-click `index.html` or open it with your browser:
```bash
# Windows PowerShell
Start-Process "c:\OS Mini Project\index.html"
```

### Option 2: Local HTTP Server (Python)
```bash
# Navigate to project directory
cd "c:\OS Mini Project"

# Launch Python HTTP server
python -m http.server 8000

# Open in browser
# http://localhost:8000
```

---

## 📄 Generating the Formal Academic PDF Report

A production-grade Python script using `ReportLab` is included to generate a publication-quality PDF report:

```bash
# Install reportlab if not already present
pip install reportlab

# Generate the PDF
python "c:\OS Mini Project\generate_pdf.py"
```

This outputs `ExamOS_Academic_Case_Study.pdf` complete with formal university formatting, mathematical matrices, benchmarks, and diagrams.

---

## 💻 Tech Stack & Design System
- **Markup:** HTML5 semantic structure.
- **Styling:** Vanilla CSS3 with CSS custom properties, CSS Flexbox/Grid, and glassmorphic card effects.
- **Scripting:** Modular vanilla ES6+ JavaScript (class-based state management, event-driven reactive updates).
- **Palette:** Dark Cyber-Academic Obsidian/Slate (`#0a0f1d`, `#111827`, `#1e293b`).
- **Typography:** Google Fonts (`Plus Jakarta Sans` for UI, `JetBrains Mono` for matrices and logs).
