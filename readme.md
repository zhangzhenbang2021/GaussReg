# A Gaussian Filter-Based 3D Registration Method for Series Section Electron Microscopy （AAAI 2025）

**GaussReg** is a frequency-aware 3D registration method designed for Series Section Electron Microscopy (ssEM) data.  
It aims to eliminate nonlinear distortions introduced during sectioning while preserving natural biological deformations.

GaussReg reformulates 3D registration from a frequency-domain perspective, modeling the deformation field as a superposition of high-frequency nonlinear distortions and low-frequency natural deformations. By extending 1D Gaussian filtering to 3D image stacks and integrating it with optical flow networks, GaussReg consolidates deformation fields within the receptive field and enables effective frequency decoupling.


# Installation

### Step 1: Create conda environment and activate
```bash
conda create -n Gaussreg python=3.9
conda activate Gaussreg
```

### Step 2: Install dependencies
```bash
pip install -r requirements.txt
```

---

# Walkthrough

### Testing
Gaussreg supports two forms of 3D elastic registration. For small-sized images (around 1024 pixels), you can run the following code to achieve 3D elastic registration.

```bash
cd src/elastic 
python single_process.py --input_dir /path/to/img_folder --output_dir /path/to/output_folder --model_path /path/to/model 
```

### Training
Gaussreg estimates the displacement field between slices using an optical flow neural network and integrates the displacement field with a Gaussian filter. Here, we outline the preparation of training data and the training for the network.

#### Data Preparation
The optical flow network in Gaussreg is trained on the CREMI[^1] dataset and fine-tuned on the dataset provided by OpenOrganelle[^2]. Download the training data on the CREMI website. Then, run the following code:
```bash
cd src/utils
python aug_data.py --input_file /path/to/sample_A_padded_20160501.hdf --output_dir /path/to/train_data/a_padded --size 1024 --border 80
python deform_serial.py --input_file /path/to/train_data/a_padded --output_dir /path/to/train_data/a_padded_warp --alpha 4.0 --sigma 0.08
```
> Apply the same procedure to the other two data files provided by CREMI, resulting in the training data.

For the data provided by OpenOrganelle, run the following code to generate the training dataset.
```bash
cd src/utils
sh download.sh
```

#### Train
Run the following code to train the model:
```bash
cd src/elastic
python train.py --dataset cremi --root_dataset /path/to/train_data --base_path /path/to/result
```

[^1]: [CREMI Dataset](https://cremi.org/)
[^2]: [OpenOrganelle Dataset](https://openorganelle.janelia.org/)

# Related Work

We extend **GaussReg** to large-resolution ssEM datasets and integrate it with a rigid alignment pre-processing module to form a complete reconstruction pipeline.  
Please refer to our latest work for more details:

- **vEMRec**: https://github.com/zhangzhenbang2021/vEMRec
