# NTU-AMMAI-CDFSL

The source code of NTU-AMMAI-CDFSL.

### 06/04 Update

The RAM usage issue: if your computer doesn't have enough ram for this project, you can first create a json file which contain all the image infomation (name, label etc.) and load the json file instead of all the images. If there is any further question, please let us know.

### Datasets
   * mini-ImageNet: https://drive.google.com/file/d/1zGGCKzspL0GSZhiDEp8rpVnB4i-jL2k_/view?usp=sharing
   * EuroSAT: https://drive.google.com/file/d/1DUj510w7726Iga34gmmhLA4meVYULrBe/view?usp=sharing
   * ISIC: https://drive.google.com/file/d/15jXP_rDEi_eusIK-kCz-DD5ZBFD3WeGF/view?usp=sharing

### Pretrained Model
   * ResNet10 Baseline/ProtoNet are provided in logs/checkpoints/miniImageNet.

## Description
   See https://docs.google.com/document/d/1rNuAb3D0dcXI776eKrj8iNpE2LQmCE63WU7lRTOQGIU/edit?usp=sharing

### Specific Tasks:

   **EuroSAT**

     • Shots: n = {5}

   **ISIC2018**

     • Shots: n = {5}


### Environment
   Python 3.7
   
   Pytorch 1.3.1

### Steps
   1. Download all the needed datasets via above links.
   2. Change configuration in config.py to the correct paths in your own computer.
   3. Train models on miniImageNet. (Note: You can only train your own model, other pretrained models are provided.)
   - **Standard supervised learning on miniImageNet**

       ```bash
           python ./train.py --dataset miniImageNet --model ResNet10  --method baseline --train_aug
       ```
   - **Train meta-learning method (protonet) on miniImageNet**
   
       The available method list: [protonet]

       ```bash
           python ./train.py --dataset miniImageNet --model ResNet10  --method protonet --n_shot 5 --train_aug
       ```
   4. Test
      You should know the following options:

      • --task: fsl/cdfsl, option for task 1(fsl) or task 2(cdfsl).

      • --model: ResNet10, network architecture.

      • --method: baseline/protonet/your-own-model.

      • --train_aug: add this if you train the model with this option.

      • --freeze_backbone: add this for inferring directly. (Do not add this if you want to fine-tune your model, you should only fine-tune models in task 2.)

      There are two meta-test files:

      * **meta_test_Baseline.py**:
      
        For Baseline, we will train a new linear classifier using support set.

        ```bash
            python meta_test_Baseline.py --task cdfsl --model ResNet10 --method baseline  --train_aug --freeze_backbone
        ```
         You can also train a new linear layer and fine-tune the backbone.

        ```bash
            python meta_test_Baseline.py --task cdfsl --model ResNet10 --method baseline  --train_aug
        ```

      * **meta_test_few_shot_models.py**:
      
        This method will apply the pseudo query set to the few-shot model you want to fine-tune with. 

        The available method list: [protonet]

        The available model list: [ResNet10]
        
        ```bash
            python meta_test_few_shot_models.py --task cdfsl --model ResNet10 --method protonet  --train_aug
        ```

   No matter which finetune method you chosse, a dataset contains 600 tasks.

   After evaluating 600 times, you will see the result like this: 600 Test Acc = 49.91% +- 0.44%.

### Results

| Models  | miniImageNet | EuroSAT | ISIC |
| ------------- | ------------- | ------------- | ------------- |
| Baseline | 68.10% ± 0.67% | 75.69% ± 0.66% / 79.08% ± 0.61% | 43.56% ± 0.60% / 48.11% ± 0.64% | 
| ProtoNet | 66.33% ± 0.65% | 77.45% ± 0.56% / 81.45% ± 0.63% | 41.73% ± 0.56% / 46.72% ± 0.59% |

For EuroSAT and ISIC, the result w/o and w/ fine-tuning are the first and second accuracy, respectively.

### TODOs
   1. Try to re-run all baseline models for both tasks. 

      ```bash
           python meta_test_Baseline.py --task fsl --model ResNet10 --method baseline  --train_aug --freeze_backbone
      ```

      ```bash
           python meta_test_Baseline.py --task cdfsl --model ResNet10 --method baseline  --train_aug 
      ```

      ```bash
           python meta_test_few_shot_models.py --task fsl --model ResNet10 --method protonet  --train_aug --freeze_backbone
      ```

      ```bash
           python meta_test_few_shot_models.py --task cdfsl --model ResNet10 --method protonet  --train_aug
      ```

   2. Design your own model, and report your results for both tasks.
      - You should inherit the template in meta_template.py, and design your own model.
      - For task 2, you can infer the query set directly, or you can also design your fine-tuning method (You can stil use pseudo query set to fine-tune).

       ```bash
           python meta_test_few_shot_models.py --task fsl --model ResNet10 --method your_method  --train_aug --freeze_backbone
      ```

      ```bash
           python meta_test_few_shot_models.py --task cdfsl --model ResNet10 --method your_method  --train_aug
      ```

      - Hint: large margin methods or feature generalization methods may be helpful to solve the problem.
      
### Contact Information
   H.T. Su (d06944009@ntu.edu.tw)

   Jia-Fong Yeh (jiafongyeh@ieee.org)
