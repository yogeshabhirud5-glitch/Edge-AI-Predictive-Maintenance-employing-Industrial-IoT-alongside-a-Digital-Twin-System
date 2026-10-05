# Edge-AI Predictive Maintenance with Industrial IoT and Digital Twin

## Overview

This repository contains the implementation and dataset for an **Edge-AI based predictive maintenance and Industrial IoT monitoring system** with a real-time Digital Twin dashboard.

The system monitors four machine-condition parameters:

- Temperature
- Vibration
- Current
- Sound

Sensor data can be acquired through a serial-connected device or generated in Demo/Simulation Mode. The data is normalized, classified into machine-health states, used to generate maintenance decisions, monitored through system-performance metrics, optionally uploaded to ThingSpeak, and stored in CSV format.

The implementation demonstrates an edge-based monitoring workflow for industrial predictive-maintenance applications.

## Key Features

- Real-time monitoring of temperature, vibration, current, and sound
- Serial communication through `/dev/ttyUSB0` at 9600 baud
- Automatic Demo/Simulation Mode when a serial device is unavailable
- Sensor-data preprocessing and normalization
- Health-state classification:
  - **HEALTHY**
  - **DISTURBED**
  - **FAULT**
- Maintenance decision support:
  - Continue Operation
  - Schedule Inspection
  - Preventive Maintenance
- Real-time Tkinter/ttkbootstrap Digital Twin dashboard
- Sensor gauges and historical trend graphs
- ThingSpeak cloud upload support
- CSV logging of sensor, health, maintenance, latency, CPU, RAM, and reward metrics
- End-to-end latency monitoring
- Mean, P50, P95, and P99 latency calculations
- RMSE and MAE utility functions
- CPU and RAM utilization monitoring
- Reinforcement-learning-related parameters and cumulative reward tracking

## Repository Contents

```text
.
├── edge_ai_code.py
├── Edge_AI_Dataset.xlsx
├── edge_ai_metrics.csv        # Generated during execution
└── README.md
```

### `edge_ai_code.py`

Main Python implementation of the Edge-AI Digital Twin monitoring application. It contains sensor acquisition, preprocessing, health classification, maintenance decision logic, ThingSpeak communication, performance measurement, CSV logging, and the real-time dashboard.

### `Edge_AI_Dataset.xlsx`

Dataset workbook provided with the project. The workbook contains the following sheets:

- `2060_Samples`: 2060 rows × 8 columns — columns: Sample_No, Time, Current (A), Sound_Intensity, Temperature ©, Vibration_Amplitude, Condition, Data_Status
- `500_Practical_Samples`: 500 rows × 7 columns — columns: Sample_No, Time, Current (A), Sound_Intensity, Temperature ©, Vibration_Amplitude, Condition

## System Workflow

```text
Sensors / Dataset
       |
       v
Data Acquisition
       |
       v
Preprocessing & Normalization
       |
       v
Health-State Inference
       |
       +--------------------+
       |                    |
       v                    v
Maintenance Decision    Digital Twin Dashboard
       |                    |
       +---------+----------+
                 |
                 v
      Performance Monitoring
                 |
        +--------+--------+
        |                 |
        v                 v
     CSV Log         ThingSpeak
```

## Sensor Parameters

The implementation uses the following parameters:

| Parameter | Code Key | Maximum Scale | Monitoring Limit |
|---|---|---:|---:|
| Temperature | `TEMP` | 80 | 60 |
| Vibration | `VIB` | 800 | 600 |
| Current | `CUR` | 5 | 3 |
| Sound | `SOUND` | 500 | 300 |

Critical conditions are triggered when:

- Vibration > 700
- Current > 4

The health classification logic first checks critical vibration/current conditions for **FAULT**. If critical conditions are not present but one or more monitoring limits are exceeded, the condition is classified as **DISTURBED**. Otherwise, it is classified as **HEALTHY**.

## Maintenance Decision Logic

The maintenance decision is based on the severity of sensor-limit violations.

| Condition | Maintenance Action |
|---|---|
| Critical vibration/current | Preventive Maintenance |
| Two or more limit violations | Schedule Inspection |
| Otherwise | Continue Operation |

The implementation also maintains a cumulative reward based on the selected maintenance action.

## Data Preprocessing

The four sensor parameters are normalized using their configured maximum scales:

```text
Temperature / 80
Vibration / 800
Current / 5
Sound / 500
```

The resulting normalized feature vector is passed to the inference stage.

## Model / Inference

The current implementation performs condition inference using the defined health-classification logic. The application is configured to display the following offline comparison values:

- Reported accuracy: **97.43%**
- CNN-LSTM comparison accuracy: **95.10%**

These values are configured in the application and should be interpreted according to the experimental methodology and evaluation procedure of the associated research work.

## Edge Deployment

The application supports two operating modes.

### Live Serial Mode

When a compatible serial device is available, the program attempts to read sensor values through:

```text
Port: /dev/ttyUSB0
Baud rate: 9600
```

The serial parser accepts sensor keys such as:

```text
TEMP / T
VIB / X
CUR / C
SOUND / S
```

### Demo / Simulation Mode

If a serial device is unavailable, the application automatically switches to Demo Mode and generates sensor readings for testing the monitoring workflow and dashboard.

## Digital Twin Dashboard

The GUI provides real-time visualization of:

- System health status
- Maintenance action
- Confidence
- Temperature gauge and trend
- Vibration gauge and trend
- Sound gauge and trend
- Current gauge and trend
- Offline accuracy
- CNN-LSTM comparison
- Mean latency
- P95 latency
- RMSE
- CPU utilization
- RAM utilization

The dashboard refreshes approximately every second.

## Cloud Connectivity

The implementation supports ThingSpeak communication through its update API.

The application uploads:

```text
Field 1 → Temperature
Field 2 → Sound
Field 3 → Vibration
Field 4 → Current
```

The configured upload interval is **15 seconds**.

Before using ThingSpeak, replace the placeholder API key in the Python file:

```python
WRITE_API_KEY = "YOUR_NEW_THINGSPEAK_WRITE_API_KEY"
```

**Security:** Do not commit a real ThingSpeak API key to a public GitHub repository. Use an environment variable or another secure secret-management method for public deployment.

## Performance Monitoring

The application measures:

- Preprocessing time
- Inference time
- Communication time
- End-to-end latency
- CPU utilization
- RAM utilization

Latency statistics include:

- Mean latency
- P50 latency
- P95 latency
- P99 latency

The application also provides RMSE and MAE calculation utilities.

## CSV Logging

During execution, the application creates:

```text
edge_ai_metrics.csv
```

The log contains:

```text
Timestamp
Temperature
Vibration
Current
Sound
Health
Confidence
Maintenance_Action
Preprocessing_ms
Inference_ms
Communication_ms
End_to_End_ms
CPU_Percent
RAM_Percent
Cumulative_Reward
```

This file can be used for subsequent performance analysis and visualization.

## Reinforcement Learning Parameters

The implementation includes configurable parameters for the maintenance-decision/reward component:

```text
Gamma (γ) = 0.95
Learning Rate = 0.001
```

The current code maintains a cumulative reward based on maintenance actions. It should not be described as a separately trained deep-RL policy unless a corresponding training procedure and trained model are added to the repository.

## Installation

### Requirements

Python 3.x is recommended.

The program uses or attempts to install/import:

- NumPy
- PySerial
- psutil
- requests
- tkinter
- ttkbootstrap

For manual installation:

```bash
pip install numpy pyserial psutil requests ttkbootstrap
```

Tkinter may need to be installed separately depending on the operating system.

## Running the Project

Clone the repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd <YOUR-REPOSITORY-NAME>
```

Run the application:

```bash
python edge_ai_code.py
```

If no serial device is detected, the program runs in Demo/Simulation Mode.

If a compatible serial device is connected at `/dev/ttyUSB0`, the application attempts to use Live Serial Mode.

## Dataset and Reproducibility

The repository includes `Edge_AI_Dataset.xlsx` as the project dataset workbook.

For reproducible research:

1. Keep the original dataset unchanged.
2. Document preprocessing and evaluation steps.
3. Clearly distinguish measured sensor data from Demo/Simulation data.
4. Record the hardware and sensor configuration used for live acquisition.
5. Record Python and dependency versions.
6. Keep API keys and credentials outside the repository.
7. Report experimental results only for the datasets and evaluation procedure actually used.
8. Keep generated runtime logs separate from the source dataset unless intentionally included as experimental results.

## Project Scope

This repository demonstrates an integrated Edge-AI / Industrial IoT predictive-maintenance workflow:

**Sensing → Edge Processing → Health Assessment → Maintenance Decision → Digital Twin Visualization → Cloud Connectivity → Performance Logging**

It is intended for research, academic demonstration, prototyping, and further development toward industrial predictive-maintenance systems.

## License

Add the license applicable to your project before publishing the repository.

For example:

```text
MIT License
```

Do not claim a license unless you have selected and applied it to the repository.

## Author

**Yogesh Bhirud**

M.Tech – Mechatronics Engineering

Project: **Edge-AI Predictive Maintenance with Industrial IoT and Digital Twin System**

