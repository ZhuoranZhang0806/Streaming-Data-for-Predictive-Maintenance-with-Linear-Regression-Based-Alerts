1. Project Summary

This project implements a real-time machine monitoring and predictive alert system for an industrial robot using Python and PostgreSQL.
The system simulates real-time operational data, analyzes machine behavior, and generates alerts when abnormal operating conditions are detected.

The goal is not only to build a regression model, but to design a complete machine learning engineering pipeline including:

Data ingestion (stream simulation)

Database storage (Neon PostgreSQL)

Work-period detection

Regression-based analysis

Threshold-based anomaly detection

Real-time visualization

Popup alert notifications

The system demonstrates a simplified version of predictive maintenance, where potential mechanical degradation can be detected before failure occurs.

2. System Architecture

The monitoring pipeline follows a streaming workflow:

Raw CSV → Streaming Simulator → PostgreSQL Database → Processing → Alert Logic → Live Visualization


Main components:

Component	Description
StreamingSimulator	Simulates real-time sensor data from a running machine
Neon PostgreSQL	Stores incoming machine data
Work Period Detector	Identifies when the robot is actively working
Regression Model	Learns normal operating behavior
Alert Engine	Detects abnormal conditions
Live Dashboard	Displays real-time force signals and alerts

3. Project Structure
project/
│
├── data/
│   ├── raw/
│   ├── processed/
│
├── configs/
│   └── experiment_config.yaml
│
├── src/
│   ├── stream_db.py
│   ├── stream_streaming.py
│   ├── stream_alert_thresholds.py
│   ├── alerts.py
│   ├── evaluation.py
│
├── notebooks/
│   ├── EDA.ipynb
│   ├── regression_analysis.ipynb
│
├── DataStreamVisualization_main.py
├── requirements.txt
└── README.md

4. Setup Instructions
4.1 Clone Repository
git clone <https://github.com/ZhuoranZhang0806/Streaming-Data-for-Predictive-Maintenance-with-Linear-Regression-Based-Alerts.git>
cd project

4.2 Create Virtual Environment (Python recommended)

Windows:

python -m venv venv
venv\Scripts\activate


Mac/Linux:

python -m venv venv
source venv/bin/activate

4.3 Install Dependencies
pip install -r requirements.txt

4.4 Configure Database (Neon PostgreSQL)

Create a database on Neon and copy your connection string.

Edit:

configs/experiment_config.yaml


Add:

database:
  connstr: "postgresql://neondb_owner:npg_Sh8bV3HjZvkd@ep-plain-scene-ahmzh8by-pooler.c-3.us-east-1.aws.neon.tech/neondb?sslmode=require&channel_binding=require"
  source_table: "robot_data"

4.5 Run the Streaming Dashboard

From the project root:

python DataStreamVisualization_main.py --config configs/experiment_config.yaml


The system will:

Connect to the database

Initialize the table from CSV (if empty)

Start streaming simulated machine data

Display live plots

Trigger alerts when abnormal behavior occurs



5. Regression Model

A linear regression model is used to learn the normal operating behavior of the robot during working periods.

The model estimates expected force behavior.
Abnormal behavior is detected when actual measurements deviate significantly from learned patterns.

The regression is not used alone for anomaly detection.
Instead, it supports trend analysis and helps identify gradual mechanical wear.

6. Alert Detection Rules

The system uses a hybrid anomaly detection strategy combining statistical thresholds and trend analysis.

An alert is generated only when both conditions are satisfied:

1) Level-based Condition

The machine force exceeds a statistically derived threshold:

Threshold=median+k×1.4826×MAD

Where:

median = typical operating level

MAD = Median Absolute Deviation (robust variability)

k = sensitivity factor

This detects unusually high load or resistance.

2) Trend-based Condition

The slope of force values increases beyond a limit, indicating progressive degradation or friction.

This prevents false alarms from short spikes.

Why Both?
Condition	Problem if Used Alone
Level only	many false alarms
Trend only	misses sudden faults

Combining both produces stable and reliable alerts.

7. Example Results
Live Streaming Dashboard

![Streaming Dashboard](img/StreamingDashboard.png)

Detected Anomaly

![Test Results](img/TestResults.png)

Work Period Detection

![Plot of first interval](data/models/plots/interval_1_mean.png)
Results of other intervals are located at data/models/plots

8. Key Engineering Insights

Machine learning systems depend more on data pipelines than model complexity.

Real-time monitoring requires state management (sliding windows & cooldown).

Hybrid statistical + rule-based detection is more reliable than pure ML.

Configuration files improve experiment reproducibility.

Visualization is part of the decision-support system, not just reporting.

9. Technologies Used

Python

NumPy / Pandas

Matplotlib

Scikit-learn

PostgreSQL (Neon)

YAML Configuration

10. Future Improvements

Replace simulation with real IoT sensor stream

Add multivariate anomaly detection

Deploy dashboard as a web application

Integrate automated maintenance scheduling