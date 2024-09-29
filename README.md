# ConceptVectors Benchmark

This repository contains the data for the ConceptVectors Benchmark and the code for the experiments in our paper titled **[Intrinsic Evaluation of Unlearning Using Parametric Knowledge Traces]**


## Quick Links
- [ConceptVectors Benchmark](#conceptVectors-benchmark)
  - [Quick Links](#quick-links)
  - [Overview](#overview)
  - [1. Requirements](#1-requirements)
  - [2. Training and Forgetting](#2-training-and-forgetting)
  - [3. Evaluate Forgetting Effectiveness](#3-evaluate-forgetting-effectiveness)
  - [4. Concept Validation Experiments](#4-concept-Validation-experiments)
  - [5. Jailbreaking Experiments](#5-jailbreaking-experiments)
  - [6. Knowledge Editing Testing](#6-knowledge-editing-testing)

## Overview
You can reproduce the experiments in our paper.

> **Abstract**
> The task of "unlearning'' certain concepts in large language models (LLMs) has attracted immense attention recently, due to its importance for mitigating undesirable model behaviours, such as the generation of harmful, private, or incorrect information. Current protocols to evaluate unlearning methods largely rely on behavioral tests, without monitoring the presence of unlearned knowledge within the model's parameters. This residual knowledge can be adversarially exploited to recover the erased information post-unlearning. We argue that unlearning should also be evaluated internally, by considering changes in the parametric knowledge traces of the unlearned concepts. To this end, we propose a general methodology for eliciting directions in the parameter space (termed ''concept vectors'') that encode concrete concepts, and construct ConceptVectors, a benchmark dataset containing hundreds of common concepts and their parametric knowledge traces within two open-source LLMs. Evaluation on ConceptVectors shows that existing unlearning methods minimally impact concept vectors, while directly ablating these vectors demonstrably removes the associated knowledge from the LLMs and significantly reduces their susceptibility to adversarial manipulation. Our results highlight limitations in behavioral-based unlearning evaluations and call for future work to include parametric-based evaluations.


**Instance Structure Example**:

```python
  {
      "ID": "",
      "Concept": "Harry Potter",
      "Layer": 20,
      "Dim": 10513,
      "QA": ["Who is the author of the Harry Potter book series?",
            "What is the name of the first book in the Harry Potter series?"..],
      "text_completion": [{
                "First_half": "In contrast Emily Griesinger...",
                "Second_half": "his encounter with the Sorting Hat..."
            }..],
      "unrelated_QA": ["When was Costa Coffee founded?",
                      "Where is Costa Coffee headquartered?"..], 
      "wikipedia_content": "Harry Potter is a series of seven fantasy novels written by British author J. K. Rowling...",
  }
```



## 1. Requirements
To install the required packages for our baselines testing on ConceptVectors, please run the following command.
```sh
conda create -n conceptvectors python=3.9.5
conda activate conceptvectors
conda install pytorch==2.2.0 torchvision==0.17.0 torchaudio==2.2.0 pytorch-cuda=11.8 -c pytorch -c nvidia
pip install -r requirements.txt
```

## 2. Training and Forgetting


```sh
CUDA_VISIBLE_DEVICES=0 bash all_forget_llama.sh
or
CUDA_VISIBLE_DEVICES=0 bash all_forget_olmo.sh
```
Before run the command, please make sure to update your data_path and model_path in the ./config/forget.yaml :)

[//]: # (**Adjustable Hypeparameters**:)

[//]: # (- **`forget_loss`**: grad_ascent, grad_diff, npo, npo_grad_diff, npo_KL, dpo)

[//]: # (- **`ft_type`**: Full, MEMIT, all_value_vectors, Neddle,)

[//]: # (- **`set`**: test, dev)

[//]: # (- **`lr`**: ..&#40;learning rate&#41;)

[//]: # (- **`epoch`**: ..&#40;training epoch&#41;)

[//]: # (- **`batch_size`**: ..)

[//]: # (- **`gradient_accumulation_steps`**: ..)

[//]: # (- **`loss_threshold`**: ..)

| Important Tunable hyperparameters | Choices                                                                           |
|-----------------------------------|-----------------------------------------------------------------------------------|
| **`forget_loss`**                 | [grad_ascent, grad_diff, npo, npo_grad_diff, npo_KL, dpo]                         |
| **`ft_type`**                     | [Full, all_value_vectors, Neddle] (see point.6 for memit)                         | 
| **`set`**                         | [test, dev]                                                                       |
| **`lr`**                          | [1e-1,2e-1,3e-1,5e-1] for Needle, [1e-5,2e-5,3e-5,5e-5] for others(learning rate) |
| **`num_epochs`**                  | [1,2,3,5,10] (training epoch)                                                     |
| **`batch_size`**                  | .. (set it based your gpu memory)                                                 |
| **`gradient_accumulation_steps`** | .. (set it based your gpu memory)                                                 |
| **`loss_threshold`**              | 0 for NPO and DPO (loss_threshold for training early stop)                        |


## 3. Evaluate Forgetting Effectiveness

```sh
python evaluat_llama.py
or
python evaluat_olmo.py
```

## 4. Concept Validation Experiments
Please run ./Concept_Validation_Experiments/Concept_Validation_Experiments.ipynb

## 5. Jailbreaking Experiments
Please run ./Jailbreak/jailbreak.ipynb

## 6. Knowledge Editing Testing

For the use of knowledge editing methods, we provide triplets_to_templates pairs in ./ConceptVectors_data/relation_for_KE/relation_to_template.json and relations for every concept in ./ConceptVectors_data/relation_for_KE.

Please run the following commands for MEMIT unlearning testing:

```sh
cd memit
python3 -m experiments.evaluate \
   --model_name=your_model_path \
   --hparams_fname=llama2-7b.json or olmo-7b.json \
```

Please set args.dummy_string to False if you want to run MEMIT+Entropy

Please set args.dummy_string to True if you want to run MEMIT+Empty

