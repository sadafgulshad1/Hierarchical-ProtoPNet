# Hierarchical-ProtoPNet
This repository contains the official implementation of our work [Hierarchical Explanations for Video Action Recognition](https://arxiv.org/pdf/2301.00436.pdf).

**Abstract:** *We propose Hierarchical ProtoPNet: an interpretable network that explains its reasoning process by considering the hierarchical relationship between classes. Different from previous methods that explain their reasoning process by dissecting the input image and finding the prototypical parts responsible for the classification, we propose to explain the reasoning process for video action classification by dissecting the input video frames on multiple levels of the class hierarchy. The explanations leverage the hierarchy to deal with uncertainty, akin to human reasoning: When we observe water and human activity, but no definitive action it can be recognized as the water sports parent class. Only after observing a person swimming can we definitively refine it to the swimming action. Experiments on ActivityNet and UCF-101 show performance improvements while providing multi-level explanations.* 

<img src="https://github.com/sadafgulshad1/Hierarchical-ProtoPNet/blob/main/Architecture_HProtoPNet.png"  />

## Dataset Preparation
### UCF-101
* Download videos and train/test splits [here](http://crcv.ucf.edu/data/UCF101.php).
* Convert from avi to jpg files using ```util_scripts/generate_video_jpgs.py```

```bash
python -m util_scripts.generate_video_jpgs avi_video_dir_path jpg_video_dir_path ucf101
```

* Generate annotation file in json format similar to ActivityNet using ```util_scripts/ucf101_json.py```
  * ```annotation_dir_path``` includes classInd.txt, trainlist0{1, 2, 3}.txt, testlist0{1, 2, 3}.txt

```bash
python -m util_scripts.ucf101_json annotation_dir_path jpg_video_dir_path dst_json_path
```
### Hierarchical UCF-101
* We define hierarchy for UCF-101 with the number of classes at level one, two, and three being 5, 20, and 101 respectively. The classes at the third level of the hierarchy are the 101 original classes of the dataset. The full hierarchy is included in the file `` UCF-101_hierarchy.csv `` 

## Pre-trained Models
### ResNet-3D Backbone
Pre-trained 3D-ResNet models are available [here](https://drive.google.com/open?id=1xbYbZ7rpyjftI_KCk6YuL-XrfQDz7Yd4). We used ```r3d18_K_200ep.pth ``` trained on kinetics 700 (K) and finetuned it on the respective datasets in our experiments.
### Hierarchical Action Embeddings
We computed the hierarchical action embeddings for the hierarchy defined for UCF-101 in `` UCF-101_hierarchy.csv `` following [Teng.et.al](https://openaccess.thecvf.com/content_CVPR_2020/papers/Long_Searching_for_Actions_on_the_Hyperbole_CVPR_2020_paper.pdf). The precomputed hyperbolic action embeddings are uploaded in the file ``UCF101_two_level_emb.pth`` 

## Running the Code 
* For training the model ```bash
python main.py --root_path ~/data --video_path ~/UCF-101-JPEG --annotation_path ucf101_01.json \
--result_path results --dataset ucf101 --model resnet \
--model_depth 18 --n_classes 101 --batch_size 128 --n_threads 4 --checkpoint 5
```
* Continue Training from epoch 101. (/save_100.pth is loaded.)

```bash
python main.py --root_path ~/data --video_path ~/UCF-101-JPEG --annotation_path ucf101_01.json \
--dataset ucf101 --resume_path results/save_100.pth \
--model_depth 18 --n_classes 101 --batch_size 128 --n_threads 4 --checkpoint 5
```
## BibTeX
If you found this work useful in your research, please consider citing
```
@article{gulshad2023hierarchical,
  title={Hierarchical Explanations for Video Action Recognition},
  author={Gulshad, Sadaf and Long, Teng and van Noord, Nanne},
  journal={arXiv preprint arXiv:2301.00436},
  year={2023}
}
```
## Acknowledgements
* We used the baseline ResNet-3D code from [3D ResNets for Action Recognition](https://github.com/kenshohara/3D-ResNets-PyTorch).
* We used the hyperbolic embeddings code from [Hyperbolic Action Recognition](https://github.com/Tenglon/hyperbolic_action).
* We used the baseline ProtoPNet code from [ProtoPNet](https://github.com/cfchen-duke/ProtoPNet).
