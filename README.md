# Project-Submission
A repository built to deliver the final byproduct of the team project conducted during CS470, 2023S.

There are two repositories in this organization directly related to our project.
- *oneformer-scripts*
- *panoptic-lifting*

Here, the repository *oneformer-scripts* contains a script file that we implemented to generate panoptic masks for RGB images under the given directory and compile the outputs that can be fed to the training script of *panoptic-lifting*.
We provide the detailed guideline to reproduce our experiment(s) below.

## Generating Panoptic Masks for Images

To prepare training data for the later steps, please follow the steps below:

1. Download the preprocessed one of HyperSim, Replica, or ScanNet scene by referring to the README.md @ [*panoptic-lifting*](https://github.com/KAIST-CS470-P10/panoptic-lifting). Note that this repository is merely a private fork of [the official implementation](https://github.com/nihalsid/panoptic-lifting).
2. Taking ScanNet as an example, the dataset directory is organized as follows (details may vary across datasets, but their structures are similar):
  ```
  scannet/
    scene0423_02/
      color/        # Directory containing RGB images captured from multiple viewpoints
      depth/        # Directory containing depth maps captured from multiple viewpoints
      instance/     # Directory containing instance masks created during the preprocessing step
      intrinsic/    # Directory containing text files storing camera intrinsic matrices
      panoptic/     # IMPORTANT: Directory containing outputs of Mask2Former inference
      pose/         # Directory containing text files storing camera extrinsic matrices
      and more!
  ```
