# 🚶‍♂️ StrideSense: Real-Time Step Counter (DSP-Based Peak Detection)

Welcome to **StrideSense**! This is a Python-based implementation of a walking step counter that processes raw tri-axial accelerometer data (`gFx`, `gFy`, `gFz`) sampled at a relatively low frequency of **10Hz** (tested on walking data from `Person30`).

The algorithm successfully cuts through the noise of raw sensor movement and achieves around **85% accuracy** in detecting actual steps.

---

## 🧐 The Challenge with Raw Accelerometer Data
When you hold a phone or a wearable device while walking, the raw sensor data is incredibly messy. Gravity, sudden hand tilts, and minor shakes create a lot of high-frequency noise. If you try to count peaks directly on raw data, you'll end up with hundreds of false steps. 

To solve this, this project applies a proper **Digital Signal Processing (DSP)** pipeline to clean the signal before counting the steps.

---

## 🛠️ How the Algorithm Works (The Pipeline)

Here is the step-by-step breakdown of how the code transforms messy sensor readings into accurate step counts:

### 1. Vector Magnitude Calculation
Instead of relying on just one axis (like X or Y, which can change depending on how the phone is oriented), we calculate the **Euclidean Norm (Magnitude)** of all three axes:

$$Magnitude = \sqrt{gFx^2 + gFy^2 + gFz^2}$$

This gives us a single, orientation-independent acceleration force.

### 2. Butterworth Bandpass Filter
Human walking frequency typically falls between **0.5 Hz and 3.0 Hz**. 
- We apply a 4th-order **Butterworth Bandpass Filter** with a low cutoff of 0.3 Hz (to remove static gravity and slow tilts) and a high cutoff of 3.0 Hz (to eliminate sudden shakes and electronic noise).

### 3. Moving Average Smoothing
Even after filtering, the signal can have sharp edges. We apply a **Rolling Mean (Smoothing Window)** of roughly `0.3 seconds` to smooth out the curve, making the peaks distinct and well-defined.

### 4. Adaptive Peak Detection
Instead of using a hardcoded threshold (which fails if a person walks slower or faster), the algorithm calculates an **adaptive prominence** based on the standard deviation of the signal ($\sigma$):

$$\text{Prominence} = 0.15 \times \sigma$$

*(Where $\sigma$ represents the standard deviation of the smoothed signal).*

It also enforces a minimum distance constraint (`fs / 3` samples) between peaks to ensure that a single step isn't counted twice.

---

## 📊 Performance & Results
- **Dataset Tested:** "Data/80steps.csv"
- **Sampling Rate:** 10 Hz
- **Final Accuracy:** **~85%** closely matching the actual manual step count.

The repository includes an automatic `matplotlib` visualization that plots the cleaned signal and highlights exactly where every single step was registered.

---

## 🚀 Getting Started

### Prerequisites
Make sure you have Python installed on your system. Then, clone this repository and navigate into the project directory.

### 1. Install Dependencies
Install the required signal processing and data science libraries using pip:
```bash
pip install -r requirements.txt
