# Virtual Try-On System - High-Level Design

## 1. System Overview
AI-powered clothing visualization system using FitDiT deep learning model to generate photorealistic renderings of garments on person images. It handles segmentation, pose estimation, and image synthesis to create a seamless try-on experience.

## 2. Architecture Overview

### 2.1 System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                           Client Applications                        │
│  (Web Interface, Mobile App, E-commerce Integration, Python Client)  │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                            REST API Layer                            │
│                    (Request Handling, Authentication)                │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        ComfyUI Integration Layer                     │
│                  (Workflow Management, Job Processing)               │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         Core Processing Pipeline                     │
├─────────────────┬─────────────────────────┬─────────────────────────┤
│  FitDiT Model   │  Mask Generator Module  │  Try-On Processor Module│
│   (AI Engine)   │  (Segmentation & Pose)  │  (Image Synthesis)      │
└─────────────────┴─────────────────────────┴─────────────────────────┘
```

## 3. Data Flow

### 3.1 Primary User Flow
1. User uploads person image and garment image
2. System validates image formats and dimensions
3. FitDiT model processes the person image to generate segmentation and pose information
4. Try-On processor combines all inputs to render the garment on the person
5. System returns final image and any requested intermediate outputs
6. Results are displayed to the user or returned via API

### 3.2 Data Processing Pipeline

```
┌─────────────┐    ┌─────────────┐    ┌──────────────────┐
│ Person Image│    │Garment Image│    │   Model Loading  │
└──────┬──────┘    └──────┬──────┘    └────────┬─────────┘
       │                  │                     │
       ▼                  │                     ▼
┌──────────────┐          │           ┌──────────────────┐
│    Pose      │          │           │   FitDiT Model   │
│  Extraction  │◄─────────┼───────────┤    Inference     │
└──────┬───────┘          │           └────────┬─────────┘
       │                  │                    │
       ▼                  │                    ▼
┌──────────────┐          │           ┌──────────────────┐
│  Segmentation │          │           │   Mask Creation  │
│     Mask     │◄─────────┼───────────┤                  │
└──────┬───────┘          │           └────────┬─────────┘
       │                  │                    │
       │                  ▼                    │
       │           ┌──────────────┐            │
       └──────────►│   Try-On     │◄───────────┘
                  │  Processing   │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  Final Image │
                  │   Rendering  │
                  └──────────────┘
```

## 4. API Specifications

### 4.1 REST Endpoints

#### 4.1.1 Try-On Request
- **Endpoint**: `/api/v1/tryon`
- **Method**: POST
- **Parameters**:
  - `person_image`: Base64-encoded person image
  - `garment_image`: Base64-encoded garment image
  - `options`: JSON object with rendering parameters
- **Response**: JSON object with result URLs and processing metadata

#### 4.1.2 Status Check
- **Endpoint**: `/api/v1/status/{job_id}`
- **Method**: GET
- **Response**: JSON object with job status and progress information

## 5. Sample
Model Input | Garment Input | Output
--- | --- | --- 
![mode image](https://github.com/manthanchauhan/virtual-try-on/blob/main/model2.png) | ![garment image](https://github.com/manthanchauhan/virtual-try-on/blob/main/04_dress.png) | ![output](https://github.com/manthanchauhan/virtual-try-on/blob/main/ComfyUI_output_00002_.png)
