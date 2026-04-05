🚦 Stack Light Simulation — IIoT Supervision Dashboard
PLC Siemens S7-1500 → Modbus TCP → Node-RED → InfluxDB → Grafana
Overview
This project implements a complete Industry 4.0 data chain by simulating a 3-LED stack light (signal tower) controlled by a Siemens S7-1500 PLC and supervised end-to-end through modern IIoT tools.
The system covers every layer of industrial data management:

Acquisition — PLC logic in TIA Portal
Transport — Modbus TCP communication
Processing — Node-RED flows and dashboard
Storage — InfluxDB time-series database + Excel/CSV export
Visualization — Grafana Labs supervision dashboard + interactive HTML demo


┌─────────────────┐        Modbus TCP         ┌──────────────────┐
│  Siemens S7-1500│  ────────── port 502 ────► │    Node-RED      │
│  (TIA Portal)   │                            │  Flow + Dashboard│
│                 │                            └────────┬─────────┘
│  Q0.0 → RED     │                                     │
│  Q0.1 → YELLOW  │                              ┌──────┴──────┐
│  Q0.2 → GREEN   │                              │             │
│  DB1.DBW2 → word│                         InfluxDB      Excel/CSV
└─────────────────┘                              │
                                                 ▼
                                           Grafana Labs
                                        (Supervision 4.0)

How It Works
1 — PLC Side (TIA Portal)
Three digital outputs drive the stack light LEDs:
OutputLEDDB1.DBW2 valueQ0.0RED1Q0.1YELLOW2Q0.2GREEN4
A function block (BitsToWord) converts the output bits into a single word stored in DB1.DBW2. The MB_SERVER block exposes this register over Modbus TCP so Node-RED can read and write it.
PLCSIM is used to simulate the PLC without physical hardware.
2 — Communication (Modbus TCP)

Protocol: Modbus TCP
Port: 502
Read: Function Code FC03 (Read Holding Registers)
Write: Function Code FC06 (Write Single Register)
Register: DB1.DBW2 mapped to holding register 40001

3 — Node-RED Flow
The Node-RED flow handles three responsibilities:

Command: sends write requests to the PLC on button press
Reading: polls the holding register and decodes red, yellow, green, word variables
Export: feeds data simultaneously to the dashboard, InfluxDB, and a CSV file

4 — InfluxDB
All state changes are stored as time-series measurements in InfluxDB, enabling historical queries and trend analysis. The data is also exported to Excel/CSV for offline traceability.
5 — Grafana
Grafana connects directly to InfluxDB and provides:

Gauge panels showing the current LED state
Stat panels for quick status overview
Time-series graphs for historical supervision


Live Demo
An interactive web dashboard simulating the system is available here:
➜ Open Live Dashboard

Click the LEDs or buttons to simulate PLC commands. Modbus registers, DB1.DBW2 value, event history, and the InfluxDB time-series chart update in real time.


Project Structure
stack-light-dashboard/
├── index.html          # Interactive supervision dashboard (standalone)
├── README.md           # This file

Validation
The system was validated end-to-end through the following test matrix:
TestExpectedResultRED command sentQ0.0=1, DBW2=1✅ PassYELLOW command sentQ0.1=1, DBW2=2✅ PassGREEN command sentQ0.2=1, DBW2=4✅ PassNode-RED reads registerDecoded correctly✅ PassInfluxDB records stateTimestamp stored✅ PassGrafana reflects historyTimeline matches✅ Pass

Key Concepts Demonstrated

Industrial Modbus TCP communication between PLC and SCADA layer
Real-time data acquisition and decoding in Node-RED
Time-series data storage with InfluxDB
Industry 4.0 supervision with Grafana Labs
Full IIoT data chain from field device to cloud visualization


Author
Hajar Thamir
