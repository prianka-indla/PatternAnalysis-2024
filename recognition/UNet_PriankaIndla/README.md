# UNet_PriankaIndla

## U-Net for MRI Image Segmentation

### 1.  Description of Model

The U-Net model is a convolutional neural network designed for biomedical image segmentation. It addresses the challenge of segmenting complex medical images, such as MRI scans, by using an encoder-decoder structure that combines high-resolution and low-resolution feature maps through skip connections. The U-Net architecture is particularly well-suited for tasks where precise locating is essential.

**Problem Solved**: In this project, the U-Net model is applied to prostate regions in MRI scans as part of the HipMRI Study on Prostate Cancer. Accurate segmentation of prostate tumors within MRI scans aids clinicians in identifying regions of interest, which is crucial for diagnosing and planning treatments for prostate cancer. This project aims to achieve high segmentation accuracy, targeting a Dice similarity coefficient of at least 0.75 on the test set. This metric indicates how well the model's segmented regions match the ground truth, reflecting the model’s reliability in identifying tumor boundaries accurately. 

### Dataset, Pre-processing and Data Splits

**Dataset**: The dataset consists of pre-processed 2D slices in Nifti file format, which must be read and processed in a specific way to enable accurate analysis. Each 2D slice represents a cross-section of the MRI scan, and these slices are handled individually to produce segmentation masks labelling critical prostate regions.

**Data Splits**:
The data is split into training, validation, and testing sets. The training set enables the model to learn, the validation set is used for hyper parameter tuning and avoiding overfitting, and the test set evaluates the model’s abaility to identify the tumour segment.

**Pre-processing**:

1. **Resizing**: Images are resized to a fixed shape of 256x128 to standardise input dimensions.
2. **Normalisation**: Pixel values are normalised to a range of [0,1] b. This helps improve model convergence and stabilises training.

### 2. Algorithm Workflow

The U-Net architecture consists of a symmetric encoder-decoder structure:

- **Encoder (Downsampling)**: Extracts high-level features from the input image using convolution and pooling layers. The encoder gradually reduces the spatial dimensions, capturing important information.
- **Bottleneck**: Optimises loss function, therefore, increases the efficiency of the model without losing much important information.
- **Decoder (Upsampling)**: Recovers spatial details and upscales the feature maps to the original input size using transpose convolutions. Skip connections between corresponding encoder and decoder layers help in retaining smaller details.

### **1. modules.py**

This file defines the the U-Net architecture.

**Core Components**:

1. **U-Net Architecture Overview**:
    - **Encoder (Downsampling)**: Extracts high-level features from the input image using convolution and pooling layers. The encoder gradually reduces the spatial dimensions, capturing important information.
    - **Bottleneck**: Optimises loss function, therefore, increases the efficiency of the model without losing much important information.
    - **Decoder (Upsampling)**: Recovers spatial details and upscales the feature maps to the original input size using transpose convolutions. Skip connections between corresponding encoder and decoder layers help in retaining smaller details.
2. **Helper Function: double_conv**:
    - The double_conv method is used repeatedly in the U-Net architecture. It consists of two convolutional layers, each followed by batch normalisation and ReLU activation.

### 2. dataset.py

This file is responsible for **data loading and preprocessing**. It defines a custom dataset class called ProstateMRIDataset, which is used by PyTorch Dataloader to efficiently manage loading batches of images during training and prediction.

**Core Components**:

- **Data Paths**: The dataset class is initialised with paths to the image and segmentation files.
- **Preprocessing**: Images and labels are loaded and resized to the U-Net model’s input dimensions.
- **Tensor Conversion**: Each image and label pair is converted to tensors so they’re compatible with PyTorch.

### 3. train.py

This file is where the U-Net model is trained. It handles loading data, initialsing the model, training, validating, and saving the best-performing model.

**Core Workflow**:

- **Data Splitting**: Images and segmentation mask datasets are defined.
- **Training Loop**:
    - **Forward Pass**: Sends images through the U-Net model.
    - **Loss Calculation**: Measures the difference between predictions and ground truth using a loss function, specifically BCELogitsLoss for binary segmentation.
    - **Backpropagation**: Optimises the model weights to minimise the loss.
- **Validation**: After each epoch, the model’s performance on the validation set is evaluated using Dice score, which is a metric used to measure segmentation quality.
- **Saving the Best Model**: The model with the best Dice score on the validation set is saved as best_model.pth.

### 4. predict.py

This file is designed for model inference on new data, applying the trained U-Net model to unseen images.

**Core Workflow**:

- **Load Model**: Loads the saved model weights from best_model.pth.
- **Data Loading**: Uses the dataset and dataloader but here it only loads the image and segmentation test dataset.
- **Prediction**:
    - **Forward Pass**: Generates a segmentation prediction for each test image.
    - **Postprocessing**: Applies thresholding to generate binary predictions and optionally resizes the output to match the input dimensions.
- **Visualisation**: Saves original image and ground truth segmentation, providing a visual assessment of the model's accuracy. This is useful for evaluating the model’s segmentation quality.

### 3. Dependencies and Reproducibility

To ensure reproducibility, the following dependencies must be installed to create the correct environment (NOTE: environment name is ***torch_env)***

```

- Python == 3.8
- torch == 2.0.1
- torchvision == 0.15.2
- nibabel == 5.1.0
- numpy == 1.24.3
- scikit-image == 0.21.0
- tqdm == 4.66.1
- matplotlib == 3.7.2

```

To install the dependencies, you can run:

```bash

pip install torch==2.0.1 torchvision==0.15.2 nibabel==5.1.0 numpy==1.24.3 scikit-image==0.21.0 tqdm==4.66.1 matplotlib==3.7.2

```

### 4. Results

Plots and images were saved using the terminal path:

```r
scp s4749392@rangpur.compute.eait.uq.edu.au:/home/Student/s4749392/loss_plot.png ~/Desktop/

```