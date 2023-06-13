# Project-Submission
A repository built to deliver the final byproduct of the team project conducted during CS470, 2023S.

There are two repositories in this organization directly related to our project.
- *oneformer-scripts*
- *panoptic-lifting*

Here, the repository *oneformer-scripts* contains a script file that we implemented to generate panoptic masks for RGB images under the given directory and compile the outputs that can be fed to the training script of *panoptic-lifting*.
We provide the detailed guideline to reproduce our experiment(s) below.

## Generating Panoptic Masks for Images

To prepare training data for the later steps, please follow the steps below:

1. Download the preprocessed one of HyperSim, Replica, or ScanNet scene by referring to the README.md @ [*panoptic-lifting*](https://github.com/KAIST-CS470-P10/panoptic-lifting). Note that this repository is merely a private fork of [the official implementation](https://github.com/nihalsid/panoptic-lifting). Taking ScanNet as an example, the dataset directory is organized as follows (details may vary across datasets, but their structures are similar):
  ```
  scannet/
    scene0423_02/
      color/        # Directory containing RGB images captured from multiple viewpoints
      depth/        # Directory containing depth maps captured from multiple viewpoints
      instance/     # Directory containing instance masks created during the preprocessing step
      intrinsic/    # Directory containing text files storing camera intrinsic matrices
      panoptic/     # IMPORTANT: Directory containing outputs of Mask2Former inference
      pose/         # Directory containing text files storing camera extrinsic matrices
      # and so on...
  ```
  **Copy the entire scene dataset with different names to prevent overwritting the original data. For instance, name the copy as 'scene0423_02_oneformer'**

2. Run the script [*run_panoptic_scannet.py*](https://github.com/KAIST-CS470-P10/oneformer-scripts/blob/main/scripts/run_panoptic_scannet.py). You may need to install the dependencies specified in the [*environment.yml*](https://github.com/KAIST-CS470-P10/oneformer-scripts/blob/main/environment.yml) file. Particularly, issue the following command from the root directory of *oneformer-scripts*:
  ```
  python scripts/run_panoptic_scannet.py --image-dir ${Path to color directory} --tag ${tag_for_this_run}
  ```
  This will create a directory `outputs/${tag_for_this_run}` directory under the root of *oneformer-scripts*. Check whether the directory `panoptic` is successfully created under the directory.
  
3. Replace the directory `panoptic` under the dataset with the one created by running the script in the previous step.

4. Change the current working directory to the root directory of *panoptic-lifting*. Clone the code and setup the `conda` environment if necessary.

5. Go through the rest of the preprocessing pipeline. When using ScanNet dataset, the corresponding preprocessing script is [*preprocess_scannet.py*](https://github.com/nihalsid/panoptic-lifting/blob/main/dataset/preprocessing/preprocess_scannet.py). The scripts for another datasets can be found under the same directory. **You might want to set the variable `dest` to point the dataset directory where we put our own `panoptic` directory. Also, comment-out all the lines before the call to function `map_panoptic_coco`**:
```[python]
# This is the main body of preprocess_scannet.py.
if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='scannet preprocessing')
    parser.add_argument('--sens_root', required=False, default='/cluster/gimli/ysiddiqui/scannet_val_scans/scene0050_02', help='sens file path')
    parser.add_argument('--n', required=False, type=int, default=1, help='num proc')
    parser.add_argument('--p', required=False, type=int, default=0, help='current proc')
    args = parser.parse_args()


    sens_root = Path(args.sens_root)
    dest = Path("data/scannet/", sens_root.stem)
    dest.mkdir(exist_ok=True)
    
    """
    COMMENT OUT FROM HERE...
    print('#' * 80)
    print(f'extracting sens from {str(sens_root)} to {str(dest)}...')
    extract_scan(sens_root, dest)
    extract_labels(sens_root, dest)
    print('#' * 80)
    print('subsampling...')
    subsample_scannet_blur_window(dest, min_frames=900)
    visualize_labels(dest)
    print('#' * 80)
    print('mapping labels...')
    fold_scannet_classes(dest)
    visualize_mask_folder(dest / "rs_semantics")
    print('#' * 80)
    print('renumbering instances...')
    renumber_instances(dest)
    visualize_mask_folder(dest / "rs_instance")
    print('#' * 80)
    print('please run the following command for mask2former to generate machine generated panoptic segmentation')
    print(
        f'python demo.py --config-file ../configs/coco/panoptic-segmentation/swin/maskformer2_swin_large_IN21k_384_bs16_100ep.yaml --input {str(dest.absolute())}/color --output {str(dest.absolute())}/panoptic --predictions {str(dest.absolute())}/panoptic --opts MODEL.WEIGHTS ../checkpoints/model_final_f07440.pkl')
    print('#' * 80)
    
    COMMENT OUT TO HERE!
    """
    
    print('mapping coco labels...')
    map_panoptic_coco(dest)
    visualize_mask_folder(dest / "m2f_semantics")
    visualize_mask_folder(dest / "m2f_instance")
    visualize_mask_folder(dest / "m2f_segments")
    print('creating validation set')
    print('#' * 80)
    print('creating validation set')
    create_validation_set(dest, 0.20)
    print('#' * 80)
```

6. After running the script, you can start training Panoptic Lifting model using the newly created dataset. Please refer to the README.md of [*panoptic-lifting*](https://github.com/KAIST-CS470-P10/panoptic-lifting) for the detailed instruction.
