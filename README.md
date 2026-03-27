# Restormer-Detection-Classification

This repository contains our Deep Learning course term project on robust traffic sign recognition under real-world visual corruptions.

Our main goal is to improve traffic sign detection and classification when the input images are degraded by conditions such as blur, rain, and exposure changes. These corruptions are common in real driving scenarios and can reduce the reliability of traffic sign recognition systems used in ADAS and autonomous driving applications.

The project follows a three-stage pipeline:

1. Image Restoration / Enhancement  
2. Traffic Sign Detection  
3. Traffic Sign Classification  

In our experiments, we compare performance on:
- RAW corrupted images
- RESTORED images after enhancement

This allows us to measure how much restoration improves downstream tasks.

Project idea

Modern vehicles increasingly include traffic sign recognition systems. However, in real driving, camera images are often degraded:
- rain can hide sign details
- fast motion can create blur
- lighting changes can cause over-exposure or under-exposure

Because of this, both detection and classification performance may drop.  
This project studies whether an image restoration stage can recover useful visual information and improve the full recognition pipeline.

Pipeline

Stage 1 — Restoration  
A Restormer-based image restoration model is used to improve corrupted traffic scene images.

Stage 2 — Detection  
A YOLOv8-based detector is used to localize traffic signs in the full frame.

Stage 3 — Classification  
A compact CNN (MiniCNN) is used to classify cropped traffic signs into 14 categories.

Repository structure

notebooks/
- data_preparation/
  - Cure_TSD_Subset.ipynb
- restoration/
  - Restormer_Exposure_Train.ipynb
  - Expert_FineTuner(Blur).ipynb
  - Expert_FineTuner(Exposure).ipynb
  - Expert_FineTuner(Rain).ipynb
- detection_classification/
  - DetectionClassification.ipynb
  - DetectionClassification(Restoration).ipynb
- demo/
  - Demo.ipynb

data/
- documentation for dataset format
- sample metadata / annotation examples if included

results/
- summaries/
  - results_raw_summary.csv
  - results_restored_summary.csv
- details/
  - results_raw_details.csv
  - results_restored_details.csv
- qualitative_examples/
  - selected GT / RAW / RESTORED visual comparisons

assets/
- figures/
  - metric graphs used in the report/presentation
- demo/
  - demo preview media if included
- presentation/
  - presentation files

models/
- contains documentation about external model weights
- actual weight files are provided through Google Drive

docs/
- reserved for report and project documents

Dataset

We use the CURE-TSD dataset, but we do not use it directly in video form.  
Instead, we extract the videos into frame-level image data and reorganize them into a structure suitable for our pipeline.

Original project data structure

subset/
  classification_labels.csv
  frames/
    train/
    test/
  labels/
    train/
    test/

Each frame name encodes metadata such as:
- sequence type
- sequence number
- corruption type
- corruption level

Each annotation file contains:
- traffic sign class
- four corner coordinates of the sign region

The dataset preparation process is documented in:
notebooks/data_preparation/Cure_TSD_Subset.ipynb

Corruptions used in evaluation

We evaluate the system on subset-based test groups:
- clean
- lens blur level 1 / level 2
- gaussian blur level 1 / level 2
- exposure level 1 / level 2
- rain level 1 / level 2

This gives us a controlled robustness benchmark where the same evaluation logic is applied across multiple corruption types and severity levels.

Models

Detection model
- YOLOv8
- single-class traffic sign detector

Classification model
- custom MiniCNN
- 14 traffic sign classes

Restoration model
- Restormer-based image restoration model
- fine-tuned separately for specific corruption types

Training design

A key experimental decision in this project is that both the detector and the classifier are trained only on clean data.

Why?
Because we want a fair and controlled robustness test.

This means:
- the models learn from clean images
- performance is tested on corrupted images
- then tested again on restored images

So the measured improvement comes from restoration, not from corruption-aware retraining.

Evaluation metrics

We use three main metrics:

YOLO-only
- mAP50

CNN-only
- classification accuracy on GT crops

End-to-End
- E2E score combining detection and classification success

This gives us stage-level and pipeline-level performance analysis.

Results

The project shows that restoration improves performance under difficult visual conditions.

Main observations:
- clean performance stays similar
- restoration provides clear gains under heavy corruptions
- the strongest improvements are observed under severe rain and blur
- end-to-end performance improves when restoration reduces missed detections and improves crop quality

The repository includes:
- detailed result CSV files
- subset-level summary CSV files
- qualitative triplet examples
- graphs comparing RAW vs RESTORED performance

Model weights

The trained model weights are not stored directly in this repository because they are large files.

All weights can be downloaded from this Google Drive folder:

https://drive.google.com/drive/folders/16ue6vpwAWVbvb13ZNynCP_i-uTJ_HwXw?usp=drive_link

The Drive folder includes:
- Fine-tuned Restormer weights
- Pretrained Restormer weights
- CNN weights
- YOLO weights

More information is available in:
models/README.md

How to run

1. Install dependencies

pip install -r requirements.txt

2. Open the notebooks depending on the stage you want to reproduce

Data preparation
- notebooks/data_preparation/Cure_TSD_Subset.ipynb

Restoration training / fine-tuning
- notebooks/restoration/Restormer_Exposure_Train.ipynb
- notebooks/restoration/Expert_FineTuner(Blur).ipynb
- notebooks/restoration/Expert_FineTuner(Exposure).ipynb
- notebooks/restoration/Expert_FineTuner(Rain).ipynb

Detection / classification / evaluation
- notebooks/detection_classification/DetectionClassification.ipynb
- notebooks/detection_classification/DetectionClassification(Restoration).ipynb

Demo
- notebooks/demo/Demo.ipynb

3. Download the model weights from the Google Drive link and update local notebook paths if needed.

Notes

This repository keeps the notebooks, result files, figures, and documentation organized for reproducibility.

Large files such as:
- model checkpoints
- full dataset
- large zip outputs
are intentionally stored outside the repository.

Credits

- CURE-TSD dataset
- Ultralytics YOLOv8
- PyTorch / Torchvision
- Restormer-based restoration approach
