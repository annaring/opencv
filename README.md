# Inverted Pendulum Vision-Based Control

This repository contains two Python scripts that together implement a control system for an inverted pendulum using both sensor and camera data. It also includes a state estimator that visually estimates the pendulum's state from event camera data.

## state_estimator.py

### Functionality
The `state_estimator.py` script visually estimates the state of an inverted pendulum using data from an event camera. Specifically, it calculates the pendulum's pitch (alpha) and azimuth (psi) angles based on the camera feed.

### How It Works

1. **Event Camera Data**  
   The script captures event-based visual data from an event camera, which records only changes in the scene, unlike traditional frame-based cameras.

2. **Polarity Filtering**  
   The script filters the events based on polarity. Events are classified as positive or negative depending on the change in pixel intensity. The polarity filter separates these events and processes them independently to identify the lines corresponding to the pendulum’s motion.
   - **Positive Polarity**: Represents events where the pixel intensity increased.
   - **Negative Polarity**: Represents events where the pixel intensity decreased.

3. **Line Detection**  
   After applying the polarity filter, the script performs edge detection using the Canny edge detector and applies the Hough Line Transform to detect lines in the event data. For each polarity, the script identifies the longest detected line.

4. **State Estimation**  
   The longest lines from each polarity are averaged to calculate the pendulum’s pitch and azimuth. This allows the script to accurately estimate the pendulum’s orientation and track its motion over time.

---

## combined_control.py

### Functionality
The `combined_control.py` script provides control for the inverted pendulum in two phases:
- **Sensor-based Control**: Initially, the pendulum's position and orientation are controlled entirely using data from sensors.
- **Camera-based Control**: Over time, the system transitions to using event camera data for control. The slewing process ensures a smooth shift from sensor to camera data.

A moving average filter is applied to the azimuth and pitch angles to reduce noise and ensure more stable control.

### How It Works
The script uses a slewing mechanism to smoothly transition control from sensor data to event camera data and applies a moving average filter to smooth out noisy data from both sources.

#### Slewing Mechanism
- The script starts by controlling the pendulum using 100% sensor data.
- Over time, the script gradually shifts control from sensor data to camera data by adjusting the weight given to each data source.
- The "weight" parameter is incremented slowly (by 0.0001 per loop iteration), allowing for a smooth transition from sensor-based control to camera-based control over the course of about 17 seconds.
- Both the pitch (alpha) and azimuth (psi) angles are slewed, meaning the control system uses a weighted combination of sensor and camera data until it fully transitions to camera data.

#### Moving Average Filter
The script uses a moving average filter to smooth out the azimuth and pitch angle data. The moving average filter reduces short-term fluctuations in the data by averaging the most recent `n` data points:
- **Azimuth (psi) Filter**: Averages the last `n` azimuth values from the event camera data, where `n` is 30.
- **Pitch (alpha) Filter**: Averages the last `n_pit` pitch values from the event camera data, where `n_pit` is 90.

The moving average filter helps provide more stable control inputs by reducing noise in the measured angles. It is applied to both the sensor data and camera data to ensure smooth control during the transition.




