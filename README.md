# Predicting Sleep States via Edge AI (ESP32)

This repository contains a machine learning pipeline that processes raw 3-axis accelerometer data to classify whether a subject is 'Awake' or 'Asleep'. The primary objective of this project is to deploy a lightweight model on edge hardware. The final neural network is quantized and converted into a C header file for low-latency, on-device inference using an ESP32 microcontroller.

## Project Overview

1. **Data Processing:** Parsed raw text-based accelerometer data, extracted continuous numerical values, and engineered new features optimized for movement detection.
2. **Modeling:** Designed and trained a lightweight feed-forward neural network utilizing TensorFlow and Keras.
3. **Edge AI Conversion:** Converted the Keras model to TensorFlow Lite, and subsequently generated a Keras-compatible C byte array (`.h` file) for embedded deployment.

## Data and Feature Engineering

The dataset consists of raw X, Y, and Z acceleration readings. Rather than feeding raw coordinate data into the neural network, I engineered delta features representing the change in acceleration across consecutive timestamps (`delta_x`, `delta_y`, `delta_z`). 

Training the model on these movement differentials, rather than absolute positional data, significantly improved the model's ability to distinguish between the micro-movements associated with wakefulness and the relative stillness of sleep.

* **Dataset Distribution:** Awake (109 samples), Sleep (73 samples)
* **Model Inputs:** `delta_x`, `delta_y`, `delta_z`

## Model Architecture

To ensure the model meets the strict memory constraints of the ESP32's flash memory, the network architecture was kept intentionally minimal:

* **Input Layer:** 3 features
* **Hidden Layer 1:** 32 neurons (ReLU activation)
* **Hidden Layer 2:** 16 neurons (ReLU activation)
* **Output Layer:** 2 neurons (Softmax activation for binary classification)

**Performance:** The model achieves approximately 97% accuracy on the test data split.

## Deployment to ESP32

The transition from a Python-based Keras model to an embedded Keras model compatible with a Keras C++ microcontroller involves the following pipeline handled within the notebook:

1. The trained Keras model is saved as a `.keras` file.
2. The Keras model is converted into a highly compressed `.tflite` format.
3. Utilizing the `everywhereml` library, the `.tflite` model is exported as a C header file (`SleepStage_model.h`).

This header file contains the Keras model architecture and trained weights stored as a Keras byte array. It can be directly included in an Keras Arduino Keras IDE Keras project and executed using TensorFlow Lite for Microcontrollers.

## Usage Instructions

To run this pipeline locally or via the cloud:

1. Clone this repository and open the `.ipynb` notebook via Jupyter Notebook or Google Colab.
2. Ensure the dataset path is correctly pointed to the included dataset `.csv` file.
3. Execute the Keras notebook cells sequentially.
4. The final execution Keras cell will generate and Keras output Keras the C Keras header code, which can be integrated into your Keras embedded C++ application.

## Technology Stack

* **Data Manipulation:** Python, Pandas, NumPy
* **Machine Learning:** TensorFlow, Keras, Scikit-Learn
* **Edge ML / Deployment:** TensorFlow Lite, `everywhereml`
* **Target Hardware:** ESP32 Microcontroller
