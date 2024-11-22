# Installation Guidance

This repository contains the modified version of the MMDetection3D framework. To reserve the anonymity of the review process, we remove the original README file and the official logo of MMDetection3D.

You can follow the steps outlined in this document to install the mo and replicate our work. The code is based on [MMDetection3D](https://github.com/open-mmlab/mmdetection3d) framework. However, it's important to note that the framework has been modified to suit our specific requirements. Therefore, to reproduce our results accurately, please follow the instructions provided in this repository, even if you've previously installed the official version of [MMDetection3D](https://github.com/open-mmlab/mmdetection3d). Additionally, we also modified the [nuScenes-devkit](https://github.com/nutonomy/nuscenes-devkit) to suit our specific requirements  for nuScenes dataset . Therefore, we recommend using the modified version of the dataset provided in this repository to ensure that you can replicate our results accurately.

## Training env setup

1. Create a python environment for training:

    ```bash
    conda create -n  det3d_train python=3.8 -y
    conda activate det3d_train
    ```

2. Install Pytorch-1.13.1 based on your CUDA version. You can find more instruction about pytorch [here](https://pytorch.org/). If you have CUDA 11.7, you can install Pytorch using the following command:

    ```bash
    pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117
    ```

3. Install the dependencies of MMDection3D:

    ```bash
    pip install -U openmim
    mim install mmengine
    mim install 'mmcv>=2.0.0rc4'
    mim install 'mmdet>=3.0.0'
    ```

4. Install the modified version of MMDectection3D:

    ```bash
    mkdir ./libs && cd ./libs
    git clone https://github.com/WaterHyacinthInNANHU/mmdetection3d.git
    cd mmdetection3d/
    git checkout EvalTrain
    pip install -v -e .
    # "-v" means verbose, or more output
    # "-e" means installing a project in edtiable mode,
    ```

5. [Optional] Verify MMDection3D installation by running the following command:

    ```bash
    # download the config and checkpoint file
    mim download mmdet3d --config pointpillars_hv_secfpn_8xb6-160e_kitti-3d-car --dest .
    # verify the inferece demo
    python demo/pcd_demo.py demo/data/kitti/000008.bin pointpillars_hv_secfpn_8xb6-160e_kitti-3d-car.py hv_pointpillars_secfpn_6x8_160e_kitti-3d-car_20220331_134606-d42d15ed.pth --show
    ```

    The expected output is a 3D visualization of the point cloud data. If you run the code on a server without a display, you can remove the `--show` flag to suppress the visualization. The result wiil be saved at ```./outputs``` folder.

## Training preparation for nuScenes dataset

We primarily adhere to the workflow outlined in the official MMDetection3D documentation for [dataset preparation](https://mmdetection3d.readthedocs.io/en/latest/advanced_guides/datasets/nuscenes.html). However, we have made some modifications to the official nuScenes-devkit to suit our specific requirements. Therefore, we recommend using the modified version of the dataset provided in this repository to ensure that you can replicate our results accurately.

Before you start, please make sure you have prepare the nuScenes dataset as the following structure:

```bash
mmdetection3d
├── mmdet3d
├── tools
├── configs
├── data
│   ├── nuscenes
│   │   ├── maps
│   │   ├── samples
│   │   ├── sweeps
│   │   ├── lidarseg (optional)
│   │   ├── v1.0-test
|   |   ├── v1.0-trainval
```

And it's recommended to symlink the dataset root to `$MMDETECTION3D/data`. You can use the following command to create the symlink:

```bash
# assume the current path is ./libs/mmdetection3d
python ../../scripts/cp_symlink.py <path to nuScenes dataset> ./data/nuscenes -d 1
```

1. Install the modified version of the nuScenes-devkit:

    ```bash
    # first uninstall the official version of nuScenes-devkit
    pip uninstall nuscenes-devkit
    # then install the modified version
    cd ../
    git clone https://github.com/WaterHyacinthInNANHU/nuscenes-devkit.git
    cd nuscenes-devkit/
    git checkout train
    pip install .
    ```

2. Generate the dataset information file. This process usually takes about hours to complete., 

    ```bash
    # assume the current path is ./libs/mmdetection3d
    python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag nuscenes
    ```

After running the above command, the folder structure should look like this:

```bash
mmdetection3d
├── mmdet3d
├── tools
├── configs
├── data
│   ├── nuscenes
│   │   ├── maps
│   │   ├── samples
│   │   ├── sweeps
│   │   ├── lidarseg (optional)
│   │   ├── v1.0-test
|   |   ├── v1.0-trainval
│   │   ├── nuscenes_gt_database
│   │   ├── nuscenes_infos_train.pkl
│   │   ├── nuscenes_infos_val.pkl
│   │   ├── nuscenes_infos_test.pkl
│   │   ├── nuscenes_dbinfos_train.pkl
│   │   ├── NuScenes_v1.0-trainval.pkl
```

## Evaluation env setup

We need to use a different version of ```nuscenes-devkit``` to generate the dataset information file for evaluation. Therefore, we need to maintain another environment for testing. You can follow the steps below to create a new environment for evaluation:

1. Create a python environment for evaluation:

    ```bash
    conda create -n  det3d_eval python=3.8 -y
    conda activate det3d_eval
    ```

2. Install Pytorch-1.13.1 based on your CUDA version. You can find more instruction about pytorch [here](https://pytorch.org/). If you have CUDA 11.7, you can install Pytorch using the following command:

    ```bash
    pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117
    ```

3. Install the dependencies of MMDection3D: 

    ```bash
    pip install -U openmim
    mim install mmengine
    mim install 'mmcv>=2.0.0rc4'
    mim install 'mmdet>=3.0.0'
    ```

4. Install the modified version of MMDectection3D:

    ```bash
    cd ./libs/mmdetection3d/
    git checkout EvalTrain
    pip install -v -e .
    # "-v" means verbose, or more output
    # "-e" means installing a project in edtiable mode,
    ```

    **Note**: If you have not follow the training env setup, you need to install the modified version of the nuScenes-devkit as well. You can follow the instructions provided in the training env setup to install the modified version of the nuScenes-devkit.

5. Install the modified version of the nuScenes-devkit for evaluation:

    ```bash
    # first uninstall the official version of nuScenes-devkit
    pip uninstall nuscenes-devkit
    # then install the modified version
    cd ../nuscenes-devkit/
    # clean the build files generated by the previous installation
    git reset --hard
    git clean -fd
    # switch to the eval branch
    git checkout eval
    pip install .
    ```

    **Note**: If you have not follow the training env setup, you need to install the modified version of the nuScenes-devkit as well. You can follow the instructions provided in the training env setup to install the modified version of the nuScenes-devkit.

You can also follow the instructions provided in the *training env setup* to verify the installation of MMDection3D.

## Evaluation preparation for nuScenes dataset

1. Generate the dataset information file:
    For the evaluation, you can use the following command to generate the dataset information file. We modified the MMDection3D to support the evaluation of specific slices of scenes. You can use the following command to generate the dataset information file for evaluation:

    ```bash
    cd ./libs/mmdetection3d
    python tools/create_data.py nuscenes --root-path ./data/nuscenes --out-dir ./data/nuscenes --extra-tag nuscenes\
     --scene-starts-at 0 --scene-ends-at 4

    # --scene-starts-at 0 --scene-ends-at 4 indicates how to cut the sample in the scene
    # for example, with the above command, we will only use the first 4 scenes for evaluation
    ```

    **Note**: The dataset information file used for evaluation is different from the one used for training. Therefore, you need to generate a new dataset information file for evaluation. And runing the above command will generate a new dataset information file and overwrite the old one. If you want to keep the old dataset information file, you should back up the old one before running the above command.
