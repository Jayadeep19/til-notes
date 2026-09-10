---
layout: post
title: "Building a smarter Cobot"
description: "Engineering an AI Vision System for Dynamic Tool Handovers between human and robot"
image:
---

- In Industry 4.0, collaborative robots (cobots) cannot rely on the rigid, hardcoded coordinates of traditional pick-and-place systems. To safely work alongside humans, cobots must perceive their environment, adapt to unstructured setups, and make real-time decisions. My *Master thesis*, **Optimal Grasping Strategy for Work-tool Handover by Cobots**, bridges the gap between static automation and dynamic human-robot collaboration by developing an end-to-end AI vision pipeline for autonomous tool retrieval.

- my research uses a model-free, data-driven pipeline integrating multiple AI models to compute stable grasp orientations entirely from visual input.

## Hardware stack used:
- Manipulator : Universal Robot 5(UR5 e-series)
- Vision System : ZED2i passive stereo vision camera (Mounted 1.5m above the workspace)
- A Ubuntu system 22.04 operating system with GTX 1070Ti GPU.
- A multi gpu cluster for training the AI models 

## Software stack and theoritical concepts:
- AI Models: Yolov5, Yolo8 models. Generative Grasping Convolutional Neural Network (GGCNN)
- Python 3.0
- Human-Robot Handover
- Object detection
- Instance Segmentation
- Transfer Learning
- Grasp Synthesis
- Coordinate Transformation

## Perception Pipeline fro Pixels to Poses:
- The pipeline can be better illustrated by a diagram
- ![Approach]({{"/assets/img/projects/thesis/Approach.PNG" | relative_url}}){: style="max-width: 70%;"}

### Step 1: Image recognition and Segmentation:
- The pipeline leverages YOLOv5 and YOLOv8 models to accurately identify eight specific workspace tools: Allen keys, files, hammers, knives, pliers, scissors, screwdrivers, and wrenches.
- Two models were choosen to balance the inference speed and accuracy of the models. YOLO v8 is more accurate but slower, while YOLOv5 is faster with a little lose of accuracy.\
- The detection accuracy of the setup highly depended on the lighting conditions, so on a low light or a cloudy day, v8 could be used for better detection acuuracy. v5 is generally used most of the time for faster inference speeds.
- I prepared custom datasets with data agumentation with noise and distortion introduced to the dataset deliberately to reproduce real world conditions and annotated accordingly.
- several models with several hyper parameters are trained. However after readig a few reasearch articles, I found that the default optimiser is the best performing optimiser for the training results. so the default optimiser (SGD) is left unchanged.


### Step 2: Instance segmentation:
- A custom-trained YOLOv8 segmentation model isolates the detected tools from background noise, mapping precise pixel boundaries to distinguish between a tool's metal and plastic parts
- Again, a custom dataset is prepared as in the case of step 1.
- This step ensures that we know which pixels belong to the tool and which pixels belong to the background.
    > To ensure human safety while handover operation, both the metal part and plastic part of the tool are annotated seperately. Since we want the robot to hold the tool on the sharp edges(metal part) and hand the plastic handle towards the human hand.
- At the end of this step, we have the following knowledge about the tool in question:
    1. Type of tool
    2. pixels that belong to the tool
    3. Specific pixels which belong to the metal and plastic part.
- with the obtained information, I cropped the pixels of the tool and filterd it for any missing values and normalised the values.
- A few more filters and image processing has to be performed on the cropped images to match the input dimensions of the next model

### Step 3: Grasp Synthesis:
- After an extensive research and reviewing several technical papers, I choose GGCNN for grasp generation.
- Grasp generation is generating a stable and suitable grasp position (grasp rectangle) and angle for each specific tool for the robot to pick it up.
    - ![grasp rectangle]({{"/assets/img/projects/thesis/grasp_rectangle.PNG" | relative_url}}){: style="max-width: 50%;"}
- The selected model is choosen because of several reasons, the important reasons are as follows:
    - The model comes pretrained on a huge dataset of around 11000 synthetic images with around 1.1M grasps called Jacquard dataset. This increases the accuracy of the model output drastically.
- A grasp is defined in jacquard dataset as a set of several values say $$g = x,y,h,w,\theta{}$$. 
    - x,y are the center of the grasp
    - h,w are the height and width of the grasp rectangle
    - $\theta{}$ is the angle with which the rectangle is inclined for the best possible grasp position.
- This pertrained model is then fine tuned to our dataset. Each instance of the data is a tensor comprising the RGB and depth image. A dataset is prepared with best grasps and the loss is an Intersection over Union(IOU):
    - $$\text{IOU Loss} = \frac{\text{Number of Correct Grasps}}{\text{Total Number of Grasps Generated}} 
    $$
    - A correct grasp is defined as a grasp with more than 25\% IOU to the ground truth.
- several GGCNN's were trained with different batch sizes for comparision. Based on the IOU loss, train loss and validation loss. A batch size of 16 suited the data better.

### step 4: Coordinate transformation:
- The output of the GGCNN is then transformed back to the Original image space for coordinate transformation.
- The coordinate transformation is the process of transforming a pixle coordinate in Image space to the Robot space called robot coordinates.
- Using Kabsch Algorithm, correlation between the camera and the robot is established. 
- Using a point cloud the depth information can be extracted and used to find the translation and rotation matrices.
- Once both the rotation and translation matrices are calculated these are then stores as binary files and can be used during the real time inferance.

## Implementation:
- The whole pipeline is written in python and the Flow of the image can be visualised by the following image.
- ![Implementation]({{"/assets/img/projects/thesis/implementation.png" | relative_url}})