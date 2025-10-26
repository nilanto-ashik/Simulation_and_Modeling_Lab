# 🚥 Adaptive Traffic Signal Control Simulation with SimPy

## Project Overview

This project implements and compares two fundamental traffic signal control strategies—**Fixed-Time Control** and a **Queue-Length Adaptive Control**—using the **SimPy** library for **Discrete-Event Simulation (DES)** in Python. The simulation runs entirely within a Google Colaboratory environment, focusing on the core logic of queue management and dynamic phase timing to reduce congestion in an isolated, four-way intersection under imbalanced traffic load.

### 🎯 Key Objective

Demonstrate that a simple, queue-based adaptive algorithm can significantly reduce average vehicle queue length and, by extension, waiting time, compared to a static, fixed-cycle signal program.

---

## ⚙️ Model Setup and Parameters

The intersection is modeled as a set of competing queues (North, South, East, West) and a traffic light process that services these queues at a fixed rate.

| Parameter | Value | Description |
| :--- | :--- | :--- |
| **Simulation Duration** | $3600$ s (1 Hour) | Total simulation time. |
| **Heavy Flow (N/S)** | `NS_ARRIVAL_RATE` = $0.1$ | High arrival rate (approx. 1 car every 10s). |
| **Light Flow (E/W)** | `EW_ARRIVAL_RATE` = $0.3$ | Low arrival rate (approx. 1 car every 3.3s). |
| **Service Rate** | `SERVICE_RATE` = $2$ cars/s | Cars cleared per second of green time. |
| **Phase Times** | `MIN_GREEN`= $15$s, `MAX_GREEN`= $45$s | Constraints for the traffic light controller. |

---

## 🧠 Core Adaptive Logic

The `traffic_light_controller` function implements the core logic that differentiates the adaptive system from the fixed-time baseline:

```python
# Adaptive Logic within traffic_light_controller(env, queues, is_adaptive=True)

if is_adaptive:
    # 1. Calculate current queue pressures on the Red and Green approaches
    # (green_queue and red_queue variables are determined here)
    
    # --- Adaptive Logic: Extension/Switching Rule ---
    
    # Prioritize switching quickly if red-light demand is high and green-light demand is met
    if red_queue > 20 and green_queue < 5: 
        green_duration = MIN_GREEN
    
    # Extend green time if current green phase still has high demand and hasn't hit max time
    elif green_queue > 10 and green_duration < MAX_GREEN:
        # Add 1 second for every 5 cars waiting on the green phase, up to MAX_GREEN
        extension = (green_queue // 5) * 1
        green_duration = min(MAX_GREEN, MIN_GREEN + extension)
        
# If not adaptive, green_duration remains MIN_GREEN (15s)
yield env.timeout(green_duration)

# Simulate queue clearance based on (green_duration * SERVICE_RATE)
# (Queue variables are updated here)

yield env.timeout(YELLOW_TIME)

# 📈 Expected Outcomes

The generated plot will show that the Fixed-Time control exhibits large, oscillating queue peaks, especially on the heavy (NS) approaches, as it rigidly sticks to the $15$s green phase even when the queue is long. The Adaptive Control plot will show queue lengths maintained at a lower, more stable level, demonstrating the efficiency gains achieved by dynamically allocating more green time to the dominant traffic flow.