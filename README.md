# Beyblade Battle Analysis: Detection and Application Development
# Beyblade Battle Analysis Application Development

## Overview
Bakuten Shoot Corp. is a leading company in the manufacturing of Beyblades, constantly aiming to enhance the performance of their products to stay ahead in the competitive market. To achieve this, they require an advanced tool to analyze Beyblade battles. The purpose of this application is to process real-life Beyblade battle videos, extract detailed data regarding Beyblade performance, and generate valuable insights that can be used by data analysts to refine the product.

As an AI developer, your task is to develop a program that reads Beyblade battle videos, detects key events (such as spinning, stopping, and other conditions), and generates a CSV file with relevant data. This data will aid in analyzing the performance and durability of the Beyblades, helping Bakuten Shoot Corp. improve the design and functionality of their products.

---

## 1. Model Selection and Training Data
The Beyblade detection model is based on the YOLO (You Only Look Once) architecture, specifically the YOLOv8 version, known for its real-time object detection capabilities. YOLOv8 balances accuracy and speed, making it an ideal choice for detecting fast-moving objects like Beyblades during a match.

### Model Architecture:
- **Layers**: 168
- **Parameters**: 3,006,038
- **Gradients**: 0
- **GFLOPs**: 8.1

### Data
The labeled images were obtained from the following sources:
- **Dataset**: [Beyblade Dataset ![dataset icon](https://img.icons8.com/ios-filled/24/000000/dataset.png)](https://universe.roboflow.com/ds-oruoc/beyblade-nttrl)
- **Backup Dataset**: [Additional Dataset ![backup icon](https://img.icons8.com/ios-filled/24/000000/dataset.png)](https://drive.google.com/drive/folders/1CD5QARBdz6SvEcQH5dciN0iEZyei3PTt?usp=sharing)
- **Provided by**: A Roboflow user
- **License**: CC BY 4.0

---

## 2. YOLOv8 Training
The training process for the YOLOv8 model is conducted in the Jupyter notebook titled `training_beyblade.ipynb`. This notebook contains the necessary steps for preparing the dataset, configuring the model parameters, and training the model on the Beyblade dataset. The final trained model is saved in the file named `best.pt`.

---

## 3. Accuracy of the Beyblade Detection Model
The accuracy of the model is evaluated through various metrics such as Precision, Recall, and mAP. Below is a summary of the model's performance:

### Overall Performance:
- **Total Images**: 39
- **Total Instances**: 78
- **Box Precision (P)**: 0.984
- **Recall (R)**: 0.861
- **Mean Average Precision at IoU=0.50 (mAP50)**: 0.941
- **Mean Average Precision at IoU=0.50 to 0.95 (mAP50-95)**: 0.684

### Class-Specific Performance:
#### Beyblade:
- **Images**: 35
- **Instances**: 67
- **Precision**: 1.000 (indicating potential overfitting)
- **Recall**: 0.995
- **mAP50**: 0.995
- **mAP50-95**: 0.868

#### Hand:
- **Images**: 7
- **Instances**: 11
- **Precision**: 0.969
- **Recall**: 0.727
- **mAP50**: 0.887
- **mAP50-95**: 0.500

*Note*: The Precision value of 1.000 for Beyblades suggests potential overfitting, meaning that the model performs extremely well on the training data but may struggle when generalizing to unseen data.

---

## 4. Logic Behind Additional Data Generation

Once the model detects Beyblades in the video frames, the code performs the following key tasks:

### Frame Processing
- The video is first loaded using OpenCV, and key information such as the **total number of frames** and **frames per second (FPS)** are retrieved.
- The **match duration** is calculated by dividing the total number of frames by the FPS.

### Object Detection
- For each frame in the video, the program detects Beyblades using the YOLOv8 model. If objects labeled as **"beyblade"** (indicating spinning) or **"stop"** (indicating non-spinning) are detected, relevant data such as bounding box coordinates, confidence scores, and widths of detected objects are extracted.
  
### Detection Data Storage
- The program stores detection data such as the **frame number**, **detected condition** (spinning or stopped), **bounding box coordinates**, and **confidence score**.
  
### Beyblade Type Assignment
- If two Beyblades are detected in a single frame, their bounding box **widths** are compared:
  - The Beyblade with the **larger width** is assigned as **Beyblade 1** (usually the larger or closer one).
  - The Beyblade with the **smaller width** is assigned as **Beyblade 2**.

### Match Outcome Logic
- The match outcome is determined based on the number of **"stop"** detections for each Beyblade:
  - The Beyblade with the **fewest "stop" conditions** is declared the **winner**.
  - If both Beyblades have the same number of stops, the result is declared a **draw**.

### Final Output
The processed data is saved to a **CSV file** which includes:
- **Frame Number**
- **Condition** (spinning or stopped)
- **Bounding Box Coordinates**
- **Confidence Score**
- **Beyblade Type** (Beyblade 1 or Beyblade 2)
- **Status** (Winner, Loser, or Draw)
- **Match Duration** (in seconds)

The CSV file (`beyblade_battle_report.csv`) provides a summary of the battle that can be used for further analysis by Bakuten Shoot Corp.'s data analysts.

### Video Input and Output
For demonstration, you can access the input and output videos using the following link: [Input and Output Videos ![video icon](https://img.icons8.com/ios-filled/24/000000/video.png)](https://drive.google.com/drive/folders/1JFc2wyTudDbvRg3881mLQKiXAk8TzABj?usp=sharing).
