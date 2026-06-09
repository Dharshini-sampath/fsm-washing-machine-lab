# fsm-washing-machine-lab
Overview

FSM Washing Machine Lab is an interactive simulation that demonstrates how a washing machine operates using Finite State Machine (FSM) principles.

The project helps students understand how industrial automation systems move between different states and respond to user inputs, sensor conditions, and fault scenarios.

Problem Statement

Traditional FSM learning relies on diagrams and theory, making it difficult to visualize real machine behavior.

This project bridges that gap by providing an interactive simulation where users can observe state transitions and machine operations in real time.

Features
Real-time FSM state visualization
Washing machine cycle simulation
Start, Pause, and Emergency Stop controls
Multiple wash modes
Standard
Heavy Duty
Eco
Delicate
Quick
Sensor simulation
Door Lock
Water Level
Pump Status
Adjustable timing controls
Fault injection mode
State transition monitoring

FSM Workflow
IDLE
 ↓
SOAK
 ↓
WASH
 ↓
RINSE
 ↓
SPIN
 ↓
IDLE

If a failure occurs:

ANY STATE → FAULT
Components Simulated
Mechanical Components
Drum
Motor
Water Inlet Valve
Drain Pump
Sensors
Door Lock Sensor
Water Level Sensor
Pump Status Sensor
Control Components
FSM Controller
State Transition Logic
Timing Manager

Tech Stack
HTML5
CSS3
JavaScript
CreatorEngine
Babylon.js
Vercel

The project was tested through:

FSM state transition testing
Sensor validation testing
Fault injection testing
Timing logic verification
User interface testing
End-to-end wash cycle testing
Future Improvements
Full 3D washing machine model
Advanced physics simulation
IoT sensor integration
AI-based fault prediction
Multiple industrial machine simulations
Learning analytics dashboard
Educational Value

This project helps learners understand:

Finite State Machines (FSM)
Industrial automation
Mechatronics systems
Sensor-based control logic
Fault detection and handling

Team

Developed as part of a Hackathon project focused on FSM-based mechatronics simulation and industrial automation learning.
