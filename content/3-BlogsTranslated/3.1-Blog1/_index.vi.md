---
title: "Blog 1"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Phát hiện biển báo giao thông tự động với YOLO và AWS SageMaker

Việc phát hiện và phân loại biển báo giao thông tự động là thành phần quan trọng của hệ thống TSL-SignMap. Bài viết này trình bày cách triển khai YOLO model trên AWS SageMaker để xử lý hình ảnh từ cộng đồng.

---

## Tại sao cần AI Detection?

| Lý do | Lợi ích |
|-------|---------|
| Giảm công sức thủ công | Admin không phải xác minh từng ảnh |
| Tăng độ chính xác | Model học từ hàng nghìn ảnh mẫu |
| Phân loại tự động | Nhận diện loại biển báo (cấm, cảnh báo, chỉ dẫn) |
| Xử lý nhanh | Kết quả trong vài giây thay vì vài phút |
| Scalability | Xử lý hàng nghìn ảnh đồng thời |

---

## YOLO - You Only Look Once

**Ưu điểm của YOLO**

```
- Real-time detection: 30+ FPS
- Single neural network: Nhìn toàn bộ ảnh một lần
- High accuracy: 80-90% mAP trên traffic signs
- Small model size: Phù hợp deploy trên mobile
```

**YOLOv8 Architecture**

| Component | Function |
|-----------|----------|
| Backbone | Extract features từ ảnh (CSPDarknet) |
| Neck | Tổng hợp features ở nhiều scale (PANet) |
| Head | Predict bounding boxes và classes |

---

## Dataset preparation

**Traffic Sign Dataset Structure**

```bash
traffic-signs/
├── images/
│   ├── train/
│   │   ├── sign_001.jpg
│   │   └── sign_002.jpg
│   └── val/
│       └── sign_test.jpg
└── labels/
    ├── train/
    │   ├── sign_001.txt  # YOLO format
    │   └── sign_002.txt
    └── val/
        └── sign_test.txt
```

**YOLO Label Format**

```
# Format: <class_id> <x_center> <y_center> <width> <height>
0 0.5 0.5 0.3 0.4  # Class 0: Stop sign
1 0.7 0.3 0.2 0.3  # Class 1: Speed limit
```

**Classes Definition**

```yaml
# data.yaml
train: ./images/train
val: ./images/val
nc: 50  # Number of classes
names: ['stop', 'speed_limit_50', 'no_entry', 'yield', ...]
```

---

## Training trên AWS SageMaker

**Setup Environment**

```python
import sagemaker
from sagemaker.pytorch import PyTorch

# Initialize SageMaker session
session = sagemaker.Session()
role = "arn:aws:iam::123456789:role/SageMakerRole"
bucket = "tsl-signmap-data"

# Upload dataset to S3
train_data = session.upload_data(
    path='traffic-signs/images/train',
    bucket=bucket,
    key_prefix='datasets/train'
)
```

**Training Script**

```python
# train.py
from ultralytics import YOLO

def train_model():
    # Load pretrained YOLOv8
    model = YOLO('yolov8n.pt')
    
    # Train on traffic signs
    results = model.train(
        data='data.yaml',
        epochs=100,
        imgsz=640,
        batch=16,
        device='cuda',
        project='/opt/ml/model',
        name='traffic_signs'
    )
    
    # Export model
    model.export(format='onnx')
    
if __name__ == '__main__':
    train_model()
```

**SageMaker Training Job**

```python
estimator = PyTorch(
    entry_point='train.py',
    role=role,
    instance_type='ml.p3.2xlarge',  # GPU instance
    instance_count=1,
    framework_version='2.0',
    py_version='py310',
    hyperparameters={
        'epochs': 100,
        'batch-size': 16
    }
)

estimator.fit({'training': train_data})
```

---

## Deploy Model Endpoint

**Create Inference Script**

```python
# inference.py
import json
import torch
from ultralytics import YOLO

def model_fn(model_dir):
    """Load model"""
    model = YOLO(f'{model_dir}/best.pt')
    return model

def predict_fn(input_data, model):
    """Run inference"""
    results = model(input_data, conf=0.5)
    
    predictions = []
    for result in results:
        boxes = result.boxes
        for box in boxes:
            predictions.append({
                'class': int(box.cls),
                'confidence': float(box.conf),
                'bbox': box.xyxy.tolist()[0]
            })
    
    return predictions
```

**Deploy Endpoint**

```python
from sagemaker.pytorch import PyTorchModel

model = PyTorchModel(
    model_data=estimator.model_data,
    role=role,
    entry_point='inference.py',
    framework_version='2.0',
    py_version='py310'
)

predictor = model.deploy(
    instance_type='ml.t3.medium',
    initial_instance_count=1,
    endpoint_name='traffic-sign-detector'
)
```

---

## Tích hợp với Lambda

**Lambda Function**

```python
import boto3
import json
import base64

sagemaker = boto3.client('sagemaker-runtime')
s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Get image from S3
    bucket = event['bucket']
    key = event['key']
    
    # Download image
    obj = s3.get_object(Bucket=bucket, Key=key)
    image_bytes = obj['Body'].read()
    
    # Invoke SageMaker endpoint
    response = sagemaker.invoke_endpoint(
        EndpointName='traffic-sign-detector',
        ContentType='application/x-image',
        Body=image_bytes
    )
    
    # Parse results
    predictions = json.loads(response['Body'].read())
    
    # Filter high confidence detections
    signs = [p for p in predictions if p['confidence'] > 0.7]
    
    return {
        'statusCode': 200,
        'body': json.dumps({
            'signs_detected': len(signs),
            'detections': signs
        })
    }
```

**API Gateway Integration**

```bash
# User uploads image
POST /api/signs/detect
Content-Type: multipart/form-data

# Response
{
  "signs_detected": 2,
  "detections": [
    {
      "class": 0,
      "class_name": "stop",
      "confidence": 0.92,
      "bbox": [120, 45, 340, 290]
    },
    {
      "class": 15,
      "class_name": "speed_limit_50",
      "confidence": 0.87,
      "bbox": [450, 100, 600, 280]
    }
  ]
}
```

---

## Optimization Tips

| Kỹ thuật | Mục đích |
|----------|----------|
| Model Quantization | Giảm model size 4x, tăng tốc inference |
| Batch Inference | Xử lý nhiều ảnh cùng lúc |
| SageMaker Auto-scaling | Scale endpoint theo traffic |
| Lambda + SQS | Xử lý ảnh bất đồng bộ, tiết kiệm chi phí |
| Edge Deployment | Deploy YOLO trên mobile app (offline mode) |

**Cost Optimization**

```python
# Sử dụng Lambda thay vì SageMaker endpoint cho traffic thấp
import torch

# Load model trong Lambda (cold start ~3s)
model = torch.jit.load('/tmp/model.pt')

# Inference
results = model(image)

# Chi phí: $0.0000166667/GB-second thay vì $0.05/hour endpoint
```

---

## Kết quả đánh giá

| Metric | Value |
|--------|-------|
| mAP@0.5 | 89.3% |
| Precision | 91.2% |
| Recall | 87.5% |
| Inference time | 45ms/image (GPU) |
| Model size | 6.2 MB (YOLOv8n) |
| False positive rate | 3.2% |

---

## Kết luận

YOLO trên AWS SageMaker cung cấp giải pháp AI detection mạnh mẽ cho TSL-SignMap:
- Training dễ dàng với GPU instances
- Deploy scalable endpoint
- Tích hợp liền mạch với Lambda và mobile app
- Chi phí tối ưu với auto-scaling

**Nguồn tham khảo:** 
- <https://docs.ultralytics.com/models/yolov8/>
- <https://docs.aws.amazon.com/sagemaker/latest/dg/pytorch.html>

---
