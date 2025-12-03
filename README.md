# Digital Twin: Li-Ion Battery SoC Estimation (EKF vs. LSTM)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-Deep%20Learning-red)
![Domain](https://img.shields.io/badge/Domain-Battery%20Systems-green)

## 📌 Project Overview
This project implements a **Digital Twin** for a Lithium-Ion battery to estimate the **State of Charge (SoC)**, a critical parameter for Battery Management Systems (BMS) in Electric Vehicles (EVs).

Because SoC cannot be measured directly, it must be estimated. This repository implements and compares two industry-standard approaches using the **NASA Randomized Battery Usage Dataset**:

1.  **Physics-Based Approach:** A 1st-Order Thevenin Equivalent Circuit Model (ECM) fused with an **Extended Kalman Filter (EKF)**.
2.  **Data-Driven Approach:** A **Long Short-Term Memory (LSTM)** Neural Network trained on temporal discharge profiles.

## 📊 Simulation Results
The project demonstrates that while Physics-based models (EKF) are robust and explainable, Data-Driven models (LSTM) can capture non-linearities without explicit parameterization.

1.  **First 5000 points (Room Temp Random Walk)**
   <img width="1191" height="539" alt="image" src="https://github.com/user-attachments/assets/7a663313-32d9-4f72-a5c6-6394a8d366f4" />

2. **Model Pulse Response**
<img width="1385" height="582" alt="image" src="https://github.com/user-attachments/assets/4e235741-8dc7-4907-a55e-10d31f8c001e" />

3.  **EKF vs True SoC (Convergence Test)**
<img width="989" height="544" alt="image" src="https://github.com/user-attachments/assets/d24c9c7a-bf94-4b08-8c4a-8dde4a716113" />

4. **LSTM Training Loss**
<img width="835" height="384" alt="image" src="https://github.com/user-attachments/assets/15dafd09-f1e7-4ec4-a40f-d18ce139d3f7" />

6. The LSTM model learns the complex temporal dependencies of voltage relaxation and load dynamics.
<img width="1004" height="536" alt="image" src="https://github.com/user-attachments/assets/35fb3424-89a7-40f4-bcef-9c2a2df31ba8" />
*The LSTM model predicts SoC with high accuracy, effectively learning the OCV-SoC relationship purely from data.*

---

## 🛠️ Technical Implementation

### Stage 1: The Physics Model (Plant)
I implemented a **1RC Thevenin Equivalent Circuit** to simulate battery dynamics.
* **State Equations:**Modeled Coulomb Counting and Polarization Voltage ($V_{c1}$) decay.
* **Parameters:** $R_0$, $R_1$, $C_1$ estimated for generic Li-Ion chemistry.
* **OCV Curve:** Linearized Open Circuit Voltage lookup table.

### Stage 2: Extended Kalman Filter (EKF)
To handle the non-linear OCV curve and measurement noise, I implemented a first-order EKF.
* **Jacobian Linearization:** Calculated $C_k = [dOCV/dSoC, -1]$ dynamically at every timestep.
* **Covariance Tuning:** Tuned Process Noise ($Q$) and Measurement Noise ($R$) to balance trust between the physics model and the voltage sensor.

### Stage 3: Deep Learning (LSTM)
Implemented a PyTorch LSTM to treat SoC estimation as a time-series regression problem.
* **Features:** Voltage, Current, Temperature.
* **Windowing:** Used a sliding window (N=50) to capture historical context.
* **Architecture:** Single-layer LSTM (Hidden dim=64) + Fully Connected Output.

---
📂 Dataset

This project uses the NASA PCoE Randomized Battery Usage Data Set.

    Source: NASA Ames Research Center

    Preprocessing: The raw .mat files are parsed to extract Voltage, Current, and Temperature profiles under Random Walk (RW) loading conditions.
---
## 👨‍💻 Author

**[Luiz Rosa]** *Power Systems Engineer | Python Developer* [[LinkedIn](https://www.linkedin.com/in/luiz-gustavo-rosa-12407536b/)]
