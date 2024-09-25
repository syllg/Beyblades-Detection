# Beyblade Battle Analysis: Detection and Application Development

## Overview
Bakuten Shoot Corp. is a leading company in the manufacturing of Beyblades, constantly aiming to enhance the performance of their products to stay ahead in the competitive market. To achieve this, they require an advanced tool to analyze Beyblade battles. The purpose of this application is to process real-life Beyblade battle videos, extract detailed data regarding Beyblade performance, and generate valuable insights that can be used by data analysts to refine the product.

As an AI developer, my task is to develop a program that reads Beyblade battle videos, detects key events (such as spinning, stopping, and other conditions), and generates a CSV file with relevant data. This data will aid in analyzing the performance and durability of the Beyblades, helping Bakuten Shoot Corp. improve the design and functionality of their products.

---

## 1. Model Selection and Training Data
The Beyblade detection model is based on the YOLO (You Only Look Once) architecture, specifically the YOLOv8 version, known for its real-time object detection capabilities. YOLOv8 balances accuracy and speed, making it an ideal choice for detecting fast-moving objects like Beyblades during a match.

### Model Architecture:
- **Layers**: 225
- **Parameters**: 3,011,043
- **Gradients**: 3,011,027
- **GFLOPs**: 8.2

### Data
The labeled images were obtained from the following sources:
- **Dataset**: [Beyblade Dataset](https://universe.roboflow.com/yolov8-kinetixpro-training/beyblade-gpafs)
- **Backup Dataset**: [Additional Dataset](https://drive.google.com/drive/folders/1uA2O0JTvouBUFI5GpY_gn8Rq8SJ6lONW?usp=drive_link)
- **Provided by**: A Roboflow user
- **License**: CC BY 4.0

---

## 2. YOLOv8 Training
The training process for the YOLOv8 model is conducted in the Google Colab titled training_beyblade.ipynb. This contains the necessary steps for preparing the dataset and training the model on the Beyblade dataset. The final trained model is saved in the file named best.pt.

**Training Summary:**
- **Epochs Completed**: 20 epochs completed in 0.030 hours.
- **Optimizer Stripped**: Stripped from runs/detect/train/weights/last.pt, 6.2MB
- **Optimizer Stripped**: Stripped from runs/detect/train/weights/best.pt, 6.2MB

---

## 3. Accuracy of the Beyblade Detection Model
The accuracy of the model is evaluated through various metrics such as Precision, Recall, and mAP. Below is a summary of the model's performance:

### Overall Performance:
- **Total Images**: 50
- **Total Instances**: 187
- **Box Precision (P)**: 0.994
- **Recall (R)**: 0.963
- **Mean Average Precision at IoU=0.50 (mAP50)**: 0.992
- **Mean Average Precision at IoU=0.50 to 0.95 (mAP50-95)**: 0.819

*Speed*: 0.2ms preprocess, 2.2ms inference, 0.0ms loss, 2.9ms postprocess per image.

*Note*: The high precision and recall values indicate the model performs well in detecting Beyblades during training.

---

## 4. Logic Behind the Detection Process
Once the model detects Beyblades in the video frames, the code performs the following key tasks:

### Video Capture and Frame Information
- The video file is opened using OpenCV's `cv2.VideoCapture`, allowing frame-by-frame access.
- Total number of frames and frames per second (FPS) are retrieved, which is crucial for understanding the video's duration and calculating time-related metrics.

### Frame Processing Loop
1. **Validating Starting Frame**: The specified starting frame is checked for validity.
2. **Detection**: For each frame, the `detect_beyblades` function utilizes the pre-trained YOLO model (`best.pt`) to identify Beyblades, returning:
   - Class IDs (representing the detected objects)
   - Confidence scores
   - Bounding box coordinates (x, y, width, height)

### Data Extraction
- The detected data is processed to extract:
  - Frame number
  - Condition of each Beyblade (spinning or stopped)
  - Bounding box dimensions
  - Width of the bounding box (to distinguish between Beyblades)

### Beyblade Classification
- The largest detected object is labeled as "Beyblade 1" and the smallest as "Beyblade 2." This classification is crucial for subsequent collision detection and determining the winner.

### Collision Detection
- **Overlap Check**: Collision detection logic checks if "Beyblade 1" and "Beyblade 2" are detected simultaneously and whether their bounding boxes intersect.
- **Collision Flag**: If a collision occurs while both Beyblades are spinning, a collision flag is set to "Yes," and a counter for total collisions is incremented.

### Determining Winner and Loser
- The code tracks the conditions of each Beyblade over the frames, counting how many times each has transitioned from spinning to stopped. The Beyblade with more "stop" occurrences is labeled as the loser.

### Time Tracking
- A cumulative time column tracks the elapsed time since the start of the battle, calculated using the frame index and FPS.

### Saving the Results
- Detection results are saved to a CSV file (`beyblade_battle_frame_report.csv`) including:
  - Frame number
  - Conditions of both Beyblades
  - Collision information
  - Additional statistics for each frame

---

## Summary Report Generation
The second part of the code analyzes the generated frame report to create a summary of the battle outcome:

### 1. Loading the Report
- The previously generated frame report is loaded into a new DataFrame for aggregation and analysis.

### 2. Winner and Loser Identification
- The code identifies the winner and loser based on the conditions recorded in the frame report.

### 3. Reason for Outcome
- **Stop Reason**: The code examines frame data for instances where one Beyblade spins while the other stops for five consecutive frames, triggering a game-ending condition.
- **Exiting the Arena**: If neither Beyblade is detected for ten consecutive frames, it assumes that one has exited the arena, leading to a game conclusion.

### Summary Saving
- The summary is saved to a new CSV file (`beyblade_battle_summary.csv`), providing an overview of the battle results.

The processed data is saved to a **CSV file**, which includes:
- **Frame Number**
- **Condition** (spinning or stopped)
- **Bounding Box Coordinates** (x1,x2,y1,y2)
- **Confidence Score**
- **Beyblade Type** (Beyblade 1 or Beyblade 2)
- **Status** (Winner, Loser, or Draw)
- **Match Duration** (in seconds)
- **Winner and Loser**
- **Game Over Reason**
- **Game Over Timing**
- **Total Cillition Count**
- **Video Analized Name**

The CSV file (beyblade_battle_report.csv) provides a summary of the battle that can be used for further analysis by Bakuten Shoot Corp.'s data analysts.

### Video Input and Output
For demonstration, you can access the input and output videos using the following link: [Input and Output Videos ![video icon](https://img.icons8.com/ios-filled/24/000000/video.png)](https://drive.google.com/drive/folders/1JFc2wyTudDbvRg3881mLQKiXAk8TzABj?usp=sharing).  
