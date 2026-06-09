# FSM Washing Machine Lab — Mechatronics Simulation

---

## Project Title
**FSM Washing Machine Lab: An Interactive 3D Mechatronics Control Simulator**

---

## LinkedIn Post URL
https://www.linkedin.com/posts/dharshini-sampathkumar-668466330_hackathon-fsm-mechatronics-share-7465691682609569793-D-Tv/?utm_source=share&utm_medium=member_desktop&rcm=ACoAAFNcgjAB6h-JQarDiWj2lXYHW2aQs8qJXM4

---

## Video Demo URL


---

## Problem Statement Fit

This project directly addresses the **Mechatronics FSM Simulation** problem statement. The challenge asked for an interactive 3D laboratory that simulates the control logic of an industrial washing machine using Finite State Machine (FSM) principles.

Traditional mechatronics education relies on static 2D diagrams and code environments that create a disconnect between abstract logic and physical mechanical behavior. Students write transition logic but never "see" how sensor failures or timing changes impact a real machine. This simulation bridges that gap by providing a visual, interactive "digital twin" of an industrial washing machine where every FSM state is directly mapped to physical 3D animations, sensor outputs, and actuator behaviors.

**Problem → Solution Mapping:**
- Static diagrams → Real-time animated FSM state transition map
- No physical feedback → 3D drum with state-driven RPM physics
- No sensor experience → Interactive door/water/pump sensor simulation
- No fault exposure → Fault injection mode with FSM fault-state handling
- Fixed cycle timing → Live configurable timers per state via digital terminal

---

## Target Users

1. **Engineering Students** — studying mechatronics, embedded systems, control logic, or PLC programming who need an interactive environment to understand FSM-based control.

2. **Educators / Lab Instructors** — teaching FSM theory who need a visual demo tool that shows Moore/Mealy machine execution in a real-world context without needing physical hardware.

3. **Hobbyists / Makers** — exploring automation concepts and wanting to understand how home appliances are controlled by discrete state machines.

---

## What We Built

We built a fully interactive browser-based 3D mechatronics simulation of an industrial washing machine controlled by a Moore/Mealy Finite State Machine. The simulation runs entirely in a single HTML file — no server, no install, no dependencies beyond a modern browser.

The simulation features:
- A visual industrial washing machine with transparent porthole, drum physics, water fill/drain animations, and pipe/valve indicators
- A live FSM state transition diagram that highlights the active state in real time
- A complete FSM controller cycling through: **IDLE → SOAK → WASH → RINSE → SPIN → IDLE**
- Full sensor simulation (door lock, water level, pump status) with guards on transitions
- Fault injection mode that forces the FSM into a FAULT state
- Variable timing configuration for each state via an on-screen digital terminal
- A post-cycle Logic Report showing the execution trace and efficiency score

---

## Core Features

### 1. FSM Logic Controller (Moore/Mealy Hybrid)
A discrete state engine implemented in JavaScript manages all states and transitions. Transitions only occur when the current state's timer expires AND all input conditions are met (e.g., door locked, pump OK). This implements Moore machine outputs (outputs depend only on state) with Mealy-style guards on transition conditions.

**States:** `IDLE`, `SOAK`, `WASH`, `RINSE`, `SPIN`, `FAULT`

**Transition Table:**

| From  | To    | Condition                        |
|-------|-------|----------------------------------|
| IDLE  | SOAK  | START pressed + Door=LOCKED + Pump=OK |
| SOAK  | WASH  | Soak timer expired               |
| WASH  | RINSE | Wash timer expired               |
| RINSE | SPIN  | Rinse timer expired              |
| SPIN  | IDLE  | Spin timer expired               |
| ANY   | FAULT | Pump failure OR door opened mid-cycle |
| FAULT | IDLE  | Auto-recovery (5s timeout)       |

### 2. Real-time FSM State Map
A floating state transition diagram in the sidebar highlights the active state with a glow effect and scale animation in real time as the machine cycles through states.

### 3. Dynamic Physics Mapping
The 3D drum rotates at RPMs mapped directly to each FSM state:
- `SOAK`: 0 RPM (filling)
- `WASH`: 25–85 RPM depending on intensity (agitation)
- `RINSE`: 50 RPM (slow tumble)
- `SPIN`: 600–1400 RPM depending on intensity (centrifugal)
- `FAULT`: 0 RPM (emergency stop)

The machine body also vibrates with increasing amplitude (CSS animation) proportional to RPM.

### 4. Sensor Simulation Triggers
Three simulated sensors gate FSM transitions:
- **Door Lock Sensor**: Blocks `START` if door is open. Opening mid-cycle triggers an immediate FAULT state.
- **Water Level Sensor**: Shows FILL/DRAIN status based on active state.
- **Pump Status Sensor**: Pump failure injection causes FSM to enter FAULT state and shows degraded behavior.

### 5. Variable Timing Logic (Digital Terminal)
Users can configure the duration (in seconds) for each timed state — SOAK, WASH, RINSE, SPIN — via numeric inputs in the sidebar. Changes take effect immediately on the next cycle. Live per-state progress bars show time remaining.

### 6. Feedback Visualizers (Output Indicators)
- **Inlet valve LED** (blue): Lights when water inlet is open (SOAK, WASH)
- **Drain pump LED** (amber): Lights when drain pump is active (RINSE, SPIN)
- **Drain particle animation**: Falling water droplets visible during drain states
- **Foam overlay**: Visible in the porthole during WASH
- **Water level fill animation**: Drum water fills/drains smoothly between states
- **Machine digit display**: Shows active state name with color-coded glow

### 7. Fault Injection Mode
The "⚠ Pump Fail" button instantly injects a pump failure. The FSM transitions to `FAULT` state, all actuators stop, red fault light activates, the screen flashes red, and the event is logged. The system auto-recovers after 5 seconds and returns to `IDLE`. The full fault path is recorded in the Logic Report.

### 8. Logic Report
After a complete cycle, a modal displays:
- Total cycle duration
- State execution path (every state visited with its duration)
- User-configured timer values
- Calculated efficiency score and grade

---

## Technical Architecture

```
┌─────────────────────────────────────────────────────┐
│                  FSM CONTROLLER                      │
│  fsm.transition(newState)                           │
│  ┌───────────┐   timer    ┌───────────┐            │
│  │   IDLE    │──────────→ │   SOAK    │            │
│  └───────────┘            └─────┬─────┘            │
│        ↑ DONE                   │ timer            │
│  ┌───────────┐            ┌─────▼─────┐            │
│  │   SPIN    │←─────────  │   WASH    │            │
│  └───────────┘  timer     └─────┬─────┘            │
│                                 │ timer            │
│                           ┌─────▼─────┐            │
│                           │   RINSE   │            │
│                           └───────────┘            │
│                                                    │
│  ANY STATE ──(fault)──→ FAULT ──(auto)──→ IDLE    │
└─────────────────────────────────────────────────────┘
         │                        │
         ▼                        ▼
 applyState(state)         Sensor Guards
 - updateUI()              - doorLocked check
 - setRPMTarget()          - pumpOk check
 - setWaterLevel()         - waterLevel check
 - setValves()
 - setVibration()
         │
         ▼
 requestAnimationFrame loop
 - RPM lerp (smooth acceleration)
 - Drum rotation physics
 - Progress bar updates
 - Countdown timer display
```

**Architecture Decisions:**
- **Pure browser (Vanilla JS/CSS/HTML)**: Zero dependencies, runs offline, instant load
- **Single RAF loop**: Smooth 60fps physics simulation decoupled from FSM timer logic
- **CSS animations for vibration**: `vib-low/med/high` keyframes triggered by state, proportional to RPM
- **Timeout-based FSM timers**: State durations drive `setTimeout` callbacks, separate from render loop
- **Separation of concerns**: `fsm` object handles pure logic; `applyState()` handles all UI side-effects

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Vanilla JavaScript (ES6+) |
| Rendering | HTML5 + CSS3 + SVG |
| Physics | CSS Animations + requestAnimationFrame |
| FSM Engine | Custom JS state machine (no library) |
| Fonts | Google Fonts (Rajdhani, Share Tech Mono, Exo 2) |
| Deployment | Static HTML file — runs in any browser |
| Editor | VS Code |
| Platform | CreatorEngine / Babylon.js compatible |

**No frameworks. No build step. No server.** Open `index.html` in a browser and it runs.

---

## Innovation / Uniqueness

1. **Zero-dependency, single-file simulation**: The entire mechatronics lab — FSM engine, 3D-style machine, physics, animations, sensor system — runs in a single `index.html` file. No npm, no server, no install.

2. **Live FSM-to-physics binding**: Unlike static diagrams, every FSM state change *immediately* drives physical outputs: RPM changes are smoothly interpolated, water level transitions use CSS transitions, vibration amplitude scales with spin speed. The logic and the physics are inseparably linked.

3. **Safety-guard transition model**: The FSM implements real-world safety patterns — the door sensor doesn't just display a warning, it *prevents the state transition* until the condition is met. Opening the door mid-cycle forces an immediate FAULT state, exactly as a real machine would behave.

4. **Educational layering**: The UI is designed so a student can see three things simultaneously — the abstract FSM diagram, the physical machine behavior, and the I/O signal map — all updating in sync. This directly addresses the "disconnect between abstract transitions and physical mechanical behavior" described in the problem statement.

5. **Configurable timing with live feedback**: Per-state timers with live progress bars let students experiment with cycle efficiency. The post-cycle Logic Report shows exactly which states were visited, how long was spent in each, and calculates an efficiency score — enabling the "reflection" step in the user journey.

---

## Demo Instructions

### Quick Start
1. Open `index.html` in any modern browser (Chrome, Firefox, Edge, Safari)
2. The machine starts in **IDLE** state — observe the FSM diagram showing IDLE highlighted
3. Click **▶ Start** — the cycle begins: IDLE → SOAK → WASH → RINSE → SPIN → IDLE

### Exploring Features

**Basic Cycle:**
- Press **▶ Start** and watch the FSM diagram highlight each state as it transitions
- Observe the drum rotation speed changing between states (slow during WASH, fast during SPIN)
- Watch the water fill (SOAK), foam appear (WASH), and water drain (RINSE/SPIN)
- The drain particle animation and valve LEDs activate at the correct states

**Sensor Guards:**
- Click **🔓 Door: Open** before starting — notice START is blocked with an error
- Lock the door, start the cycle, then open the door mid-WASH — a FAULT is triggered immediately
- Observe the FSM diagram jump to FAULT state and auto-recover after 5 seconds

**Fault Injection:**
- Click **⚠ Pump Fail** during any active state
- Watch the machine enter FAULT state, all outputs stop, screen flashes red
- The system logs the fault and auto-recovers after ~5 seconds

**Variable Timing:**
- Change the WASH timer from 10s to 3s and start a new cycle — observe faster transitions
- Increase SPIN to 20s and watch the high-speed spin state persist longer

**Intensity Modes:**
- Switch to **Heavy** mode and start a cycle — the drum spins at 1400 RPM during SPIN vs 600 RPM in Delicate
- Watch the vibration animation scale with the higher RPM

**Logic Report:**
- Let a full cycle complete — the report modal shows the full execution trace, state durations, and efficiency score

---

## Known Limitations

1. **2D visual representation**: The simulation uses 2D CSS/SVG rendering to simulate a 3D-style machine. True 3D would require a WebGL framework like Babylon.js or Three.js, which would require a build environment.

2. **Simplified water physics**: Water fill/drain is CSS height animation, not particle-based fluid simulation. Real water physics would require a physics engine.

3. **Single wash program**: The simulation implements one standard wash cycle. A real machine has multiple programs (cotton, synthetic, quick wash, etc.) each with different state sequences and RPM profiles.

4. **No load sensing**: Real machines adjust RPM and vibration based on laundry load weight. This simulation uses fixed RPM profiles per intensity mode.

5. **No temperature simulation**: The WASH state in real machines includes heating logic (temperature sensors, heating element state). This dimension is not simulated in the current version.

6. **Mobile layout**: The UI is designed for desktop (1024px+). On narrow mobile screens some panels may be cramped.

---

## Future Work

1. **True 3D in Babylon.js**: Migrate the machine mesh to a full Babylon.js scene with a proper transparent drum, 3D drum rotation, and particle effects for water/foam/steam.

2. **Multiple wash programs**: Add Cotton, Synthetic, Quick Wash, and Wool programs with their own FSM state sequences, RPM curves, and temperature targets.

3. **Temperature state**: Add a HEAT sub-state inside WASH with a PID-like temperature controller, heating element output, and NTC sensor simulation.

4. **Load balancing simulation**: Simulate unbalanced loads that cause excessive vibration, triggering a REBALANCE state that reduces RPM until the load redistributes.

5. **PLC ladder logic view**: Add a parallel view that shows the equivalent PLC ladder logic diagram updating in real time alongside the FSM diagram — bridging FSM theory with industrial PLC programming.

6. **Multi-user collaborative debugging**: Allow two browser tabs to share state via WebSockets or BroadcastChannel, so two students can inject faults and observe FSM responses collaboratively.

7. **Timed quiz mode**: Add an assessment mode where the FSM has hidden bugs (wrong transition conditions) and students must identify and fix them by observing the machine's incorrect behavior.

---

## Attributions

- Problem statement inspired by simulation on [vlabs.ac.in](https://da-iitb.vlabs.ac.in/exp/washin-machine-control/theory.html), an MoE Govt. of India initiative
- FSM theory reference: Moore/Mealy machine models as described in the problem specification
- Fonts: Google Fonts (Rajdhani, Share Tech Mono, Exo 2) — Open Font License
- License: CC BY 4.0 International
