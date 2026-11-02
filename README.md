<div align="center">

# TRAQID: Traffic-Related Air Quality Image Dataset

**Om Kathalkar · Nitin Nilesh · Sachin Chaudhari · Anoop Namboodiri**

International Institute of Information Technology, Hyderabad, India

*ICVGIP 2024, the 15th Indian Conference on Computer Vision, Graphics and Image Processing, Bengaluru*

[![Paper](https://img.shields.io/badge/Paper-ACM%20DL-blue)](https://doi.org/10.1145/3702250.3702260)
[![Dataset](https://img.shields.io/badge/Dataset-india--data.org-green)](https://india-data.org/dataset-details/4965db32-3676-427f-86f0-c8ed678dad2b)
[![Sample](https://img.shields.io/badge/Sample-Google%20Drive-yellow)](https://drive.google.com/drive/folders/1qkHjzeYPTlJiyBh-xCFq_fmSh0qq9UPV)
[![License: MIT](https://img.shields.io/badge/License-MIT-lightgrey.svg)](LICENSE)

</div>

---

> [!IMPORTANT]
> **Note on the `Timestamp` column (read this before using the CSV)**
>
> Match images to measurements **by ID**. Do not match them by timestamp.
>
> - For any image `N.png`, the CSV row with `ID = N` holds the air-quality and weather readings for that image. This image–measurement pairing is correct.
> - During preprocessing, the sensor streams (logged at different native rates) were resampled and interpolated onto a common 5-second grid so that each image frame could be assigned a measurement. In that step, the `Timestamp` column was regenerated as a uniform index for the resampled series. **It does not hold the original capture time of each image.**
> - As a result, CSV timestamps will not match the date and time overlaid on the images, and the gap between them is not constant across the dataset.
> - Use the timestamp overlaid on each image as the actual capture time. Do not use the CSV `Timestamp` column for time-based analysis.

---

## Overview

TRAQID is a dataset for **estimating air quality from images**. It contains **26,678 traffic scenes**, each captured by a **front and a rear** dashboard camera, paired with **co-located sensor readings** of PM2.5, PM10, AQI, temperature and relative humidity.

The data comes from **70+ hours** of driving (about 2,000 km) through the twin cities of **Hyderabad and Secunderabad, India**. Collection took place on 20 days across 6 months between October 2022 and July 2024. It covers day and night, three seasons, and all six CPCB AQI categories, from *Good* to *Severe*.

<div align="center">

| | |
|---|---|
| Samples | 26,678 (front + rear image pairs) |
| Day / Night | 13,789 / 12,889 |
| Seasons | Monsoon, Winter, Summer |
| Collection days | 20 (Oct 2022 to Jul 2024) |
| Image resolution | 640 × 360 |
| Sensor rate | 5 s |
| AQI categories | Good, Satisfactory, Moderate, Poor, Very Poor, Severe |

</div>

### Highlights

- **First dataset with front and rear views** of traffic paired with co-located air-quality data.
- **Day and night** imagery, including low illumination and headlight glare.
- **Seasonal diversity** across Monsoon, Winter and Summer.
- **Sequential samples**: each collection day forms a continuous sequence along the vehicle's route, which suits sequence models.
- **Privacy-preserving**: number plates and faces are blurred. Green EV number plates keep their colour.

## Data Collection

| Component | Details |
|---|---|
| Front camera | DDPAI Mola N3, 2688 × 1944, 30 fps |
| Rear camera | DDPAI X2 Pro, 120° lens, 1920 × 1080, 30 fps |
| AQ Node (custom) | ESP32 MCU, Nova SDS011 (PM2.5/PM10), BME280 (temperature/humidity), 5 s sampling |
| Reference device | Aeroqual S500, used to calibrate the low-cost PM sensors |

**Preprocessing**

- Frames were sampled from the video every 5 seconds to match the sensor rate.
- Outlier images (severe glare, extremely low light) were removed by hand.
- Images were resized to 640 × 360.
- PM sensors were calibrated against the Aeroqual S500 using linear regression, and outliers were removed with the IQR method.

AQI is computed from PM2.5 and PM10 following the [CPCB, India](https://airquality.cpcb.gov.in/AQI_India/) standard:

| Category | Good | Satisfactory | Moderate | Poor | Very Poor | Severe |
|---|---|---|---|---|---|---|
| AQI range | 0–50 | 51–100 | 101–200 | 201–300 | 301–400 | > 400 |

## Dataset Structure

```
TRAQID
├── TRAQID.csv          # One row per sample: ID, PM2.5, PM10, AQI, AQI category,
│                       # temperature, humidity, season, sequence, timestamp*
└── Images
    ├── 1               # Collection day 1
    │   ├── Front       # Front-camera images
    │   └── Rear        # Rear-camera images
    ├── 2
    │   ├── Front
    │   └── Rear
    └── ...             # 20 collection days in total
```

\* See the note on the `Timestamp` column at the top of this page.

Each sample contains:

1. A front image and a rear image
2. Temperature (°C)
3. Relative humidity (%)
4. Season
5. PM2.5 (µg/m³)
6. PM10 (µg/m³)
7. AQI value
8. AQI category

The `Sequence` column identifies the collection day each sample belongs to.

## Download

| | Link |
|---|---|
| Complete dataset | [india-data.org](https://india-data.org/dataset-details/4965db32-3676-427f-86f0-c8ed678dad2b) |
| Sample subset | [Google Drive](https://drive.google.com/drive/folders/1qkHjzeYPTlJiyBh-xCFq_fmSh0qq9UPV) |

## Quick Start

```python
import pandas as pd
import cv2

df = pd.read_csv("TRAQID.csv")

# Pair images with readings by ID, not by timestamp (see the note at the top).
row = df.iloc[0]
sample_id = row["ID"]

front = cv2.imread(f"Images/{row['Sequence']}/Front/{sample_id}.png")
rear  = cv2.imread(f"Images/{row['Sequence']}/Rear/{sample_id}.png")

print(f"ID {sample_id}: PM2.5={row['PM2.5']}, PM10={row['PM10']}, AQI={row['AQI']}")
```

> Column names and file extensions may differ slightly between releases. Check `df.columns` and the image folder after you download.

## Tasks

| # | Task | Type | Metrics |
|---|---|---|---|
| 1 | PM2.5 estimation | Regression | RMSE, MAE |
| 2 | PM10 estimation | Regression | RMSE, MAE |
| 3 | AQI value estimation | Regression | RMSE, MAE |
| 4 | AQI category estimation (6 classes) | Classification | Accuracy, F1 |

Inputs are the front and rear images, temperature, relative humidity and season. The benchmark uses an 80/10/10 train/validation/test split.

## Benchmark Results

| Method | PM2.5 RMSE ↓ | PM2.5 MAE ↓ | PM10 RMSE ↓ | PM10 MAE ↓ | AQI RMSE ↓ | AQI MAE ↓ | Acc ↑ | F1 ↑ |
|---|---|---|---|---|---|---|---|---|
| Mondal et al. | 32.81 | 19.50 | 55.65 | 30.62 | 60.34 | 36.69 | 0.64 | 0.61 |
| Kalajdjieski et al. | 33.90 | 21.47 | 51.47 | 27.39 | 68.28 | 41.07 | 0.60 | 0.56 |
| Nilesh et al. | **22.62** | **13.68** | 46.25 | 22.57 | 54.24 | 33.19 | 0.73 | 0.71 |
| AQC-Net | 23.31 | 14.45 | **44.23** | **21.23** | **52.21** | **31.67** | **0.75** | **0.74** |

The paper also reports seasonal and day/night splits, front-only and rear-only ablations, and GradCAM analysis.

## Known Challenges

- **Low illumination** in night-time images
- **Headlight glare** from oncoming vehicles
- **Unstructured traffic** typical of Indian roads
- **Class imbalance**: few *Good* samples, since most traffic scenes fall between *Satisfactory* and *Moderate*

## Citation

If you use TRAQID in your research, please cite:

```bibtex
@inproceedings{10.1145/3702250.3702260,
  author    = {Kathalkar, Om Rajendra and Nilesh, Nitin and Chaudhari, Sachin and Namboodiri, Anoop},
  title     = {TRAQID - Traffic-Related Air Quality Image Dataset},
  booktitle = {Proceedings of the Fifteenth Indian Conference on Computer Vision Graphics and Image Processing},
  series    = {ICVGIP '24},
  year      = {2025},
  articleno = {10},
  numpages  = {10},
  publisher = {Association for Computing Machinery},
  address   = {New York, NY, USA},
  location  = {Bengaluru, India},
  isbn      = {9798400710759},
  doi       = {10.1145/3702250.3702260},
  url       = {https://doi.org/10.1145/3702250.3702260},
  keywords  = {Image Dataset, Air Quality Estimation, Vehicle-Induced Pollution, Environmental Data}
}
```

## Contact

For questions about the dataset, open an [issue](../../issues) or contact **Om Kathalkar** at om_kathalkar@mail.ntua.gr.

## License

This repository is released under the [MIT License](LICENSE).
