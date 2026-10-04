# Cyril Abdel Nour

**MEng Electrical and Information Engineering @ Imperial College London** (2024–2028)

I build things at the boundary between hardware and software: FPGA accelerators, processors and the compilers that target them, and low-level systems in C.

📫 [cyril.abdelnour123@gmail.com](mailto:cyril.abdelnour123@gmail.com) · [LinkedIn](https://www.linkedin.com/in/cyril-abdel-nour-0371b7229)

---

## 🔧 Digital Design

### Low-Latency Nasdaq ITCH 5.0 Feed Handler & Order Book on FPGA
`SystemVerilog` `Kria KR260` `10G Ethernet` `URAM` `Vivado` `cocotb`

**[→ Repository](https://github.com/Cyllocoding/Nasdaq-ITCH-5.0-Feed-Handler-Order-Book-on-FPGA)**

A deterministic market-data pipeline on a Zynq UltraScale+ that ingests a Nasdaq TotalView-ITCH 5.0 feed over 10G Ethernet (MoldUDP64/UDP multicast), tracks every live order for eight subscribed stocks, and emits best-bid/best-ask updates in a fixed ~130 ns from last byte on the wire — a single 156.25 MHz clock domain with no clock crossing on the fast path.

The parser assembles variable-length, lane-unaligned ITCH messages into a 320-bit register with ping-pong buffering, chosen over realignment because the ≤1-message-per-beat property it preserves is what lets every downstream block run at line rate. Order state lives in a 4-way set-associative hash table built from 32 URAMs (16,384 slots, XOR-folded 64-bit IDs, 3-bit pseudo-LRU, slot-granularity forwarding to close the read-modify-write hazard), sized from a Poisson overflow model to keep the load factor at λ ≤ 0.5; it is an inclusive write-through cache of a 256 MB DDR backing store, so eviction is free and the DRAM's variable latency never touches the fast path. The price book is a dense ±512-tick window per side with a flip-flop occupancy bitmap and a hierarchical priority encoder, so finding the next-best level when the top of book empties is a fixed 3-cycle search rather than a data-dependent walk. Verified against a Python golden model with cocotb/Verilator from packetised Nasdaq sample data, then on hardware with `tcpreplay` at line rate.

### Real-Time FPGA Electromagnetic Field Visualiser
`SystemVerilog` `PYNQ-Z1` `FDTD` `HDMI` `Vivado`

**[→ Repository](ADD_LINK_HERE)**

An educational instrument that makes Maxwell's equations visible: a 2D FDTD solver and a ray-marching 3D renderer running together on a single PYNQ-Z1, drawing a live 128×128 electromagnetic field to HDMI at 640×480 and 60 fps.

The solver is a 16-lane parallel architecture sustaining 2.4 billion field updates/s at 100 MHz — a 31.7× throughput and 96× power-efficiency gain over an optimised NEON C implementation on the board's Cortex-A9. The renderer sustains 1.18 Gsample/s of ray-marching, a 50.8× speedup over a NEON software renderer. Fitting both on one device meant cutting the renderer's memory from 192 BRAM tiles (137% of the chip) to 96 by folding each march step's heightmap reads onto a single port across sixteen cycles and running three column-interleaved lanes, then time-sharing the interpolation multipliers to fit within 210 of 220 DSP slices. A browser interface drives it live over two decoupled data planes: WebSocket parameters translated into AXI-GPIO register writes, with HDMI output captured directly to the page.

### Quantised CNN Inference Accelerator on FPGA
`SystemVerilog` `PyTorch` `Brevitas` `Zynq-7020` `INT8`

**[→ Repository](ADD_LINK_HERE)**

An end-to-end machine learning deployment, from training to silicon. A CNN is trained in PyTorch for 11-class RF signal classification and quantised to INT8 using quantisation-aware training, then deployed to a parameterisable weight-stationary systolic array whose MAC count and pipeline depth are derived from a folding budget and cut-set retiming. The full DSP front-end runs in programmable logic — CIC/FIR decimation, windowing, a 128-point FFT, and quantisation — so no data leaves the fabric between raw I/Q sample and classification. Verified bit-exact from the trained model through an integer reference to the deployed accelerator, then characterised on throughput, arithmetic intensity, and energy per inference against a CPU baseline.

### Fuzz-Testing FPGA Place-and-Route Tools (UROP)
`Python` `Vivado` `Quartus` `FUZNET`

**[→ Repository](ADD_LINK_HERE)**

Undergraduate research on whether place-and-route tools actually do what they claim. Extended FUZNET, a randomised netlist generator for fuzz-testing P&R tools, from Xilinx Vivado to Intel Quartus, building the flow to compile, execute, and compare randomly generated designs across both vendor toolchains. Widened the generated design space beyond the original primitive set to include block RAM and DSP units, reaching classes of bugs the prior generator could not. Added checks on the physical placement and routing themselves rather than only netlist equivalence before and after optimisation — catching faults such as overlapping component placement that equivalence checking cannot detect by construction.

### RISC-V CPU — Best-Performing Design of the Cohort
`SystemVerilog` `RV32I` `Verilator`

**[→ Repository](ADD_LINK_HERE)**

A 32-bit RV32I processor built from scratch, developed from a single-cycle design into a 5-stage pipelined CPU with custom control, ALU, and datapath modules, then extended with data forwarding, branch prediction, and an L1/L2 cache hierarchy. A further out-of-order superscalar extension adds register renaming, reservation stations, a reorder buffer, and a common data bus for Tomasulo-style parallel instruction issue. Judged the highest-performing processor of the year group in a cohort-wide design project.

### Two-Node Real-Time IoT Platform — Maze Runner & Pac-Man
`SystemVerilog` `PYNQ-Z1` `AXI-DMA` `AWS` `OpenCV`

**[→ Repository](ADD_LINK_HERE)**

Two physical games built on a pair of PYNQ-Z1 boards linked through an AWS relay server, with game state projected onto the floor beneath a real rover.

Pathfinding runs entirely in programmable logic: a BFS solver implemented as a 3-state Moore FSM performing wavefront relaxation, and an A* variant using a Manhattan heuristic and a flat priority queue, with the maze streamed in over AXI-DMA in a single burst rather than polled through AXI-Lite registers. Closing timing at 100 MHz required splitting the BFS distance-map critical path into a 3-stage pipeline, moving worst negative slack from −1.9 ns to +0.5 ns for roughly 1% cycle overhead. Four line-of-sight ghost agents run in parallel in fabric, integrated with the server at sub-100 ms end-to-end round-trip latency. A camera pipeline handles hardware downsampling, HSV thresholding, and perspective warp; the rover navigates by stepper motors with gyro-corrected heading.

### Autonomous Rover — Electronics Design Project
`Embedded C` `Sensors` `Motor Control` `Web`

**[→ Repository](ADD_LINK_HERE)**

A rover that navigates an obstacle arena at speed while detecting and decoding several classes of signal through its sensors, paired with a web interface for remote control and storage of the collected sensor data.

---

## ⚙️ Software Systems and Compilers

### C90-to-RISC-V Compiler
`C++` `RISC-V` `Compilers`

**[→ Repository](ADD_LINK_HERE)**

A complete C90 compiler that lowers C source to RISC-V assembly, implementing a full lexer, parser, AST, and code generation pipeline conforming to the RV32IMFD ILP32D ABI. Achieves a 95% pass rate on the assessed test suite, including 100% of seen tests. Extended well beyond the specification with a middle-end: a custom three-address-code IR over basic blocks and a control-flow graph, enabling graph-colouring register allocation over liveness analysis alongside IR-dependent optimisation passes.

### Custom Dynamic Memory Allocator
`C` `mmap` `Systems Programming`

**[→ Repository](ADD_LINK_HERE)**

A drop-in `malloc`/`free` replacement built over `mmap`-reserved arenas, using an intrusive free list with in-band metadata headers and byte-level pointer arithmetic, plus block splitting, address-ordered insertion, and adjacent-block coalescing. Benchmarked first-fit against best-fit placement and measured the effect of coalescing under a 50,000-request stress harness, instrumenting external fragmentation, heap utilisation at failure, and peak free-list length to quantify the fragmentation/scan-cost tradeoff. Extended to segregated size-class arenas (TINY/SMALL/MEDIUM, each `mmap`-backed with its own free list and escalation on exhaustion), routing by request size and resolving zone ownership on free by pointer range.

### Concurrent UDP Client–Server Messaging System
`C` `pthreads` `UDP` `Network Programming`

**[→ Repository](ADD_LINK_HERE)**

A concurrent client–server system over raw UDP sockets with a custom application-level protocol, a dispatcher thread accepting datagrams, and per-request handler threads. Includes a reliability layer over UDP — sequence numbering, acknowledgements, and retransmission — recovering ordering and delivery guarantees over an unreliable datagram transport. Shared client state is synchronised with POSIX reader–writer locks, allowing parallel readers while serialising mutations, and liveness is tracked through a thread-safe min-heap keyed on last-activity timestamp that drives automated ping and eviction.

### Unix Shell (s3)
`C` `POSIX` `Process Management`

**[→ Repository](ADD_LINK_HERE)**

A functional Unix shell implementing process creation and execution via `fork`/`execvp`/`wait`, I/O redirection and arbitrary-length pipelines via `dup2`/`pipe`, shell built-ins, batched commands, and arbitrarily nested subshells. Extended past the specification with globbing, foreground/background job control, command history, and tab completion.

---

## 🛠 Tech

**Languages** · C · C++ · Rust · Python · Java · JavaScript · R · SQL · Tcl · SystemVerilog · Verilog

**Hardware** · Vivado · Quartus · Verilator · cocotb · PYNQ-Z1 (Zynq-7000) · Kria KR260 (Zynq UltraScale+) · MicroBlaze · AXI-Lite/Stream/DMA/VDMA · 10G Ethernet · I²C · UART · GPIO · timing closure · BRAM/URAM/DSP resource planning · fixed-point arithmetic · clock-domain crossing

**AI & ML** · PyTorch · Brevitas · quantisation-aware training · INT8 inference

**Systems** · POSIX threads · memory allocators · UDP sockets · compilers & IR design · Linux · Git · Docker · Kubernetes · GDB

---

<sub>Certifications: RAG and Agentic AI Professional Certificate (IBM/Coursera) · Machine Learning Specialization (Stanford Online & DeepLearning.AI) · Algorithmic Trading (Optiver & Imperial Trading Academy) · n8n and AI Workflow Automation (Udemy)</sub>
