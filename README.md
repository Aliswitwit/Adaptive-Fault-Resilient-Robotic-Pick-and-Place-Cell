# Adaptive Fault-Resilient Robotic Pick-and-Place Cell

## Overview

The Adaptive Fault-Resilient Robotic Pick-and-Place Cell is an industrial-grade PLC-controlled system designed for intelligent material handling. It is structured around a robust state machine that incorporates real-time fault detection, automatic retry logic, pressure-based actuation control, and adaptive behavior based on historical fault data. It simulates a robotic cell capable of sensing, adapting, and reacting to environmental and system anomalies.

## System Features

* **Fully Integrated State Machine** controlling Extend, Grip, Retract, and Cycle Completion.
* **Fault Detection Subsystem** with watchdogs and position validation.
* **Automatic Retry and Adaptive Timeout Logic** based on fault counters.
* **Head Scanner Subsystem** simulating a robotic head searching for targets.
* **Brain Logic Unit** with decision-making based on performance metrics.
* **Loop Mode and E-Stop Handling** for operator safety and flexibility.
* **Auto-Shutdown on Repeated Failures**.

## State Decision Table

| State No. | Name           | Entry Condition                                        | Exit Condition                    | Output Motors                 |
| --------: | -------------- | ------------------------------------------------------ | --------------------------------- | ----------------------------- |
|         0 | Idle           | No other states active, part not detected              | Part Detected + Cycle Trigger     | None                          |
|         1 | Arm Extend     | Part Detected + Brain Ready + Sensors OK + Pressure OK | Arm Fully Extended + Timer Done   | Motor 1 (Extend Arm)          |
|         2 | Gripper Close  | Arm Fully Extended + Sensors OK + Pressure OK          | Gripper Fully Closed + Timer Done | Motor 2 (Gripper)             |
|         3 | Arm Retract    | Gripper Fully Closed + Sensors OK + Pressure OK        | Arm Fully Retracted + Timer Done  | Motor 3 (Retract Arm)         |
|         4 | Cycle Complete | Arm Fully Retracted                                    | Trigger Cycle Complete Pulse      | O:2/4 = Cycle Complete Output |

---

## Safety Subsystem Logic

* **E-Stop and Reset**: Immediate halt of all motion with dedicated reset logic.
* **Pressure Watchdog**: Detects overpressure faults via analog input.
* **Position Validation**: Ensures sensors report expected positions after movement.
* **Lockouts**: Engages lockout bits after multiple (2x) faults per subsystem.
* **Total Fault Counter**: Enables global system shutdown if 5+ faults occur.

## Retry + Adaptive Logic

* **Motor Retry**: If a timeout occurs but position sensor remains inactive, system retries once.
* **Timeout Extend**: Based on fault history, timers extend to allow more time before declaring failure.
* **Brain Decision Logic**:

  * Extends or shrinks timeout values adaptively
  * Reactivates retry on clean cycles
  * Auto-shutdowns enabled after fault threshold exceeded

---

## Head Scanning Subsystem

* Uses states to mimic robotic scanning for targets.
* Watchdog and timeout logic prevent endless scanning.
* Locks head after repeated scan failures.

| Head State | Name          | Entry Condition                 | Exit Condition                | Action              |
| ---------: | ------------- | ------------------------------- | ----------------------------- | ------------------- |
|          0 | Idle          | System Start + No Target Found  | Target Found or Timeout       | None                |
|          1 | Scanning      | Trigger from Idle               | Scanner Sensor Detects Target | Motor 5 (Head Scan) |
|          2 | Target Locked | Target Detected + Scan Complete | Part Picked or Reset          | None                |

---

## Address Mapping

> Full digital, analog, binary, and timer/counter address mappings are documented in the internal address table.

---

## Reset Logic

* Unified reset condition for timers and counters.
* Clears retry lockouts and state machine bits.
* Ensures system returns to Idle with all faults cleared.

---

## Project Goals

* Serve as an advanced demonstration of fault-resilient PLC control.
* Provide a foundation for robotic cells and smart automation development.
* Integrate industry-standard design with educational clarity.

---

## Future Improvements

* PID loop integration for analog pressure modulation.
* Full HMI interface for operator diagnostics.
* Modular expansion of new robotic subsystems.
* Ethernet/IP or Modbus integration for remote monitoring.

---

## Author

Ali Witwit

---

## License

This project is open for educational, prototyping, and internal industrial use. For commercial deployment, seek appropriate certification and safety review.
