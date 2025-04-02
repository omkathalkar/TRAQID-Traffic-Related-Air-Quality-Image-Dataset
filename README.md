
# TRAQID - Traffic-Related Air Quality Image Dataset

**Authors**: Om Kathalkar, Nitin Nilesh, Sachin Chaudhari, Anoop Namboodiri  
**Affiliation**: IIIT Hyderabad, India  
**Conference**: ICVGIP 2024  
**DOI**: 10.1145/3702250.3702260

## Overview

TRAQID (Traffic-Related Air Quality Image Dataset) is a novel dataset designed to estimate air quality through image-based methods. This dataset consists of **26,678 traffic scene images** captured over **70 hours** of data collection in Hyderabad and Secunderabad, India. Each image is paired with **co-located air quality sensor data** including PM2.5, PM10 levels, AQI values, and weather parameters (temperature and humidity). The dataset spans multiple seasons and times of day, providing a diverse collection of traffic imagery across different environmental conditions.

### Key Features:
- **26,678 samples** featuring both **front and rear views** of traffic.
- **Day and night images** captured across **Summer, Monsoon, and Winter** seasons.
- **Co-located sensor data** including PM2.5, PM10, temperature, humidity, and AQI.
- Images are categorized into **six AQI ranges** from "Good" to "Severe."
- **Sequential data** that can be used for developing robust air quality estimation models.

## Dataset Directory Structure

The TRAQID dataset consists of a CSV file containing all the data samples and 20 subdirectories, each representing data collected on different days. Here’s the structure:

```
TRAQID
│
├── TRAQID.csv          # CSV file containing all data samples, including air quality and weather parameters
└── Images
    ├── 1               # Data for Day 1
    │   ├── Front       # Front camera images for Day 1
    │   └── Rear        # Rear camera images for Day 1
    ├── 2               # Data for Day 2
    │   ├── Front       # Front camera images for Day 2
    │   └── Rear        # Rear camera images for Day 2
    └── ...             # Continue for remaining days
```

- **CSV file**: Contains PM2.5, PM10, AQI, temperature, humidity, and timestamps for each sample. The "Sequence" column indicates the specific data sequence for the day.
- **Front and Rear images**: For each sequence, two sets of images (front and rear views) are stored.

## Data Analysis

The supplementary analysis reveals key insights about the correlation between weather parameters and air quality:

- **PM2.5 and PM10**: Strongly correlated with AQI (0.92 and 0.89, respectively).
- **Temperature and Humidity**: Show moderate negative correlation with AQI, indicating that higher temperatures and humidity levels are associated with lower particulate matter levels.

Additionally, the dataset poses several challenges such as:
- **Low illumination** in nighttime images.
- **Headlight glare** affecting visibility during data collection.
- **Unstructured traffic** common on Indian roads, leading to erratic vehicle behaviors.

These factors impact image quality and require advanced processing techniques to accurately predict AQI.

## Tasks

The dataset is designed for the following tasks:
1. **PM2.5 Estimation**: Predict PM2.5 concentration from images and weather parameters.
2. **PM10 Estimation**: Predict PM10 concentration.
3. **AQI Value Estimation**: Predict the numerical AQI value.
4. **AQI Category Classification**: Classify the AQI into six categories (Good, Satisfactory, Moderate, Poor, Very Poor, Severe).

## How to Use

### Data Structure
- **Images Folder**: Contains front and rear images for each data sample.
- **Sensor Data File**: CSV file containing PM2.5, PM10, AQI values, temperature, humidity, and timestamps.

### Download

The dataset can be downloaded from [link](https://drive.google.com/drive/folders/1qkHjzeYPTlJiyBh-xCFq_fmSh0qq9UPV).

### Requirements

To use the dataset for AQI prediction tasks, you can use popular machine learning frameworks such as:
- Python 3.x
- TensorFlow or PyTorch
- OpenCV for image processing
- Pandas for handling CSV files

### Example Usage

```python
import pandas as pd
import cv2

# Load sensor data
data = pd.read_csv('TRAQID.csv')

# Load an image
img = cv2.imread('Images/1/Front/0001.jpg')

# Display image and corresponding AQI
print(f"AQI: {data['AQI'][0]}")
cv2.imshow('Traffic Scene', img)
cv2.waitKey(0)
```

## Benchmark Results

Benchmark results of various models on the TRAQID dataset are available in the paper. Methods such as CNNs, AQC-Net, and YOLOv5 were evaluated for PM and AQI estimation, with AQC-Net achieving the highest performance in AQI prediction.

## Citation

If you use the TRAQID dataset in your research, please cite:

```bibtex
@inproceedings{10.1145/3702250.3702260,
author = {Kathalkar, Om Rajendra and Nilesh, Nitin and Chaudhari, Sachin and Namboodiri, Anoop},
title = {TRAQID - Traffic-Related Air Quality Image Dataset},
year = {2025},
isbn = {9798400710759},
publisher = {Association for Computing Machinery},
address = {New York, NY, USA},
url = {https://doi.org/10.1145/3702250.3702260},
doi = {10.1145/3702250.3702260},
booktitle = {Proceedings of the Fifteenth Indian Conference on Computer Vision Graphics and Image Processing},
articleno = {10},
numpages = {10},
keywords = {Image Dataset, Air Quality Estimation, Vehicle-Induced Pollution, Environmental Data},
location = {
},
series = {ICVGIP '24}
}
```
