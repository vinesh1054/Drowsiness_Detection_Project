
# Drowsiness Detection using YOLOv5

## Overview
This project implements a **drowsiness detection system** using **YOLOv5**, a state-of-the-art object detection model. The system detects whether a person is **awake** or **drowsy** in real-time using a webcam feed. The model is trained from scratch with custom-labeled images and can be used for **driver safety applications** or **fatigue monitoring systems**.

## Features
- **Real-time drowsiness detection** using a webcam.
- **Custom-trained YOLOv5 model** for accurate classification.
- **Automatic image collection** for dataset creation.
- **Model training and fine-tuning** using YOLOv5.
- **Integration with OpenCV for real-time video processing**.

---

## Installation and Setup

### 1. Install Dependencies
Ensure you have Python installed, then install the required libraries:

```bash
pip install torch==1.8.1+cu111 torchvision==0.9.1+cu111 torchaudio===0.8.1 -f https://download.pytorch.org/whl/lts/1.8/torch_lts.html
git clone https://github.com/ultralytics/yolov5
cd yolov5 && pip install -r requirements.txt
```

### 2. Import Required Libraries
```python
import torch
from matplotlib import pyplot as plt
import numpy as np
import cv2
import os
import time
import uuid
```

---

## Model Loading and Testing

### Load Pre-trained YOLOv5 Model
```python
model = torch.hub.load('ultralytics/yolov5', 'yolov5s')
model
```

### Perform Object Detection on an Image
```python
img = 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e4/Cars_in_traffic_in_Auckland%2C_New_Zealand_-_copyright-free_photo_released_to_public_domain.jpg/800px-Cars_in_traffic_in_Auckland%2C_New_Zealand_-_copyright-free_photo_released_to_public_domain.jpg'
results = model(img)
results.print()

plt.imshow(np.squeeze(results.render()))
plt.show()
```

---

## Real-Time Drowsiness Detection

### Detect Drowsiness from Webcam
```python
cap = cv2.VideoCapture(0)
while cap.isOpened():
    ret, frame = cap.read()
    
    results = model(frame)
    
    cv2.imshow('YOLO', np.squeeze(results.render()))
    
    if cv2.waitKey(10) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

## Training the Custom Model

### 1. Data Collection
Capture images for **awake** and **drowsy** states:
```python
IMAGES_PATH = os.path.join('data', 'images')
labels = ['awake', 'drowsy']
number_imgs = 5

cap = cv2.VideoCapture(0)

for label in labels:
    print(f'Collecting images for {label}')
    time.sleep(5)

    for img_num in range(number_imgs):
        print(f'Collecting images for {label}, image number {img_num}')
        
        ret, frame = cap.read()
        imgname = os.path.join(IMAGES_PATH, label + '.' + str(uuid.uuid1()) + '.jpg')
        
        cv2.imwrite(imgname, frame)
        cv2.imshow('Image Collection', frame)
        
        time.sleep(2)
        
        if cv2.waitKey(10) & 0xFF == ord('q'):
            break

cap.release()
cv2.destroyAllWindows()
```

### 2. Label the Images
Use **LabelImg** to annotate the dataset:
```bash
git clone https://github.com/tzutalin/labelImg
pip install pyqt5 lxml --upgrade
cd labelImg && pyrcc5 -o libs/resources.py resources.qrc
```
Run the labeling tool:
```bash
python labelImg.py
```

### 3. Train the Custom YOLOv5 Model
Modify `dataset.yml` to specify paths and classes, then run:
```bash
cd yolov5
python train.py --img 320 --batch 16 --epochs 500 --data dataset.yml --weights yolov5s.pt --workers 2
```

---

## Loading the Custom Model
```python
model = torch.hub.load('ultralytics/yolov5', 'custom', path='yolov5/runs/train/exp15/weights/last.pt', force_reload=True)
```

### Test the Custom Model on an Image
```python
img = os.path.join('data', 'images', 'awake.c9a24d48-e1f6-11eb-bbef-5cf3709bbcc6.jpg')
results = model(img)
results.print()

plt.imshow(np.squeeze(results.render()))
plt.show()
```

### Test the Custom Model on a Live Webcam Feed
```python
cap = cv2.VideoCapture(0)
while cap.isOpened():
    ret, frame = cap.read()
    
    results = model(frame)
    
    cv2.imshow('YOLO', np.squeeze(results.render()))
    
    if cv2.waitKey(10) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

## Results and Performance
- The custom model successfully detects **awake** and **drowsy** states.
- Training on **high-quality labeled images** improves accuracy.
- Further **hyperparameter tuning** and **data augmentation** can enhance results.

---

## Future Improvements
- Optimize model performance using **higher-resolution images**.
- Train with a **larger dataset** for better accuracy.
- Implement an **alert system** (e.g., sound alarm) for real-time drowsiness detection.
- Deploy as a **mobile or web application** for accessibility.

---

## Acknowledgments
- [Ultralytics YOLOv5](https://github.com/ultralytics/yolov5)
- [LabelImg](https://github.com/tzutalin/labelImg)
- OpenCV for real-time video processing

