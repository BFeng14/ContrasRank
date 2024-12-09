## Title
abstract:

## Instruction on running xxxx

### Direct inference
Colab demo for code inference with given protein and unmeasured ligands.


### Few-shot fine-tuning
Colab demo for few-shot fine-tuning with given protein, few measure ligands for fine-tuning and unmeasured ligands for testing.


## Reproduce results in our paper

### Reproduce results on virtual screening benchmarks

```
# run pocket/protein and ligand encoder model
path2weight="path to checkpoint of pocket_ranking"
path2result="./result/pocket_ranking"
CUDA_VISIBLE_DEVICES=0 bash test.sh ALL pocket_ranking ${path2weight} ${path2result}

path2weight="path to checkpoint of protein_ranking"
path2result="./result/protein_ranking"
CUDA_VISIBLE_DEVICES=0 bash test.sh ALL protein_ranking ${path2weight} ${path2result}

# run H-GNN model


# get final prediction of our model
python ensemble_result.py DUDE PCBA DEKOIS
```


### Reproduce results on FEP benchmarks (zero-shot)
```
# run pocket/protein and ligand encoder model
path2weight="path to checkpoint of pocket_ranking"
path2result="./result/pocket_ranking"
CUDA_VISIBLE_DEVICES=0 bash test.sh FEP pocket_ranking ${path2weight} ${path2result}

path2weight="path to checkpoint of protein_ranking"
path2result="./result/protein_ranking"
CUDA_VISIBLE_DEVICES=0 bash test.sh FEP protein_ranking ${path2weight} ${path2result}

# get final prediction of our model
python ensemble_result.py FEP
```

### Reproduce results on FEP benchmarks (few-shot)
```
# run few-shot fine-tuning
path2weight="path to checkpoint of pocket_ranking"
path2result="./result/pocket_ranking/FEP_fewshot"
support_num=0.6
CUDA_VISIBLE_DEVICES=0 bash test.sh FEP pocket_ranking support_num ${path2weight} ${path2result}

path2weight="path to checkpoint of protein_ranking"
path2result="./result/protein_ranking/FEP_fewshot"
CUDA_VISIBLE_DEVICES=0 bash test.sh FEP protein_ranking support_num ${path2weight} ${path2result}


# get final prediction of our model
python ensemble_result_fewshot.py FEP_fewshot support_num
```

### Reproduce results on prospective study
```
# run few-shot fine-tuning
path2weight="path to checkpoint of pocket_ranking"
path2result="./result/pocket_ranking/TIME"
support_num=8
CUDA_VISIBLE_DEVICES=0 bash test.sh TIME pocket_ranking support_num ${path2weight} ${path2result}

path2weight="path to checkpoint of protein_ranking"
path2result="./result/protein_ranking/TIME"
CUDA_VISIBLE_DEVICES=0 bash test.sh TIME protein_ranking support_num ${path2weight} ${path2result}


# get final prediction of our model
python ensemble_result_fewshot.py TIME support_num
```

### Reproduce results on unseen assay type
```
# run few-shot fine-tuning
path2weight="path to checkpoint of pocket_ranking"
path2result="./result/pocket_ranking/OOD"
support_num=8
CUDA_VISIBLE_DEVICES=0 bash test.sh OOD pocket_ranking support_num ${path2weight} ${path2result}

path2weight="path to checkpoint of protein_ranking"
path2result="./result/protein_ranking/OOD"
CUDA_VISIBLE_DEVICES=0 bash test.sh OOD protein_ranking support_num ${path2weight} ${path2result}


# get final prediction of our model
python ensemble_result_fewshot.py OOD support_num
```

### Reproduce results on active learning
```
path1="path to checkpoint of pocket_ranking"
path2="path to checkpoint of protein_ranking"
result1="./result/pocket_ranking/TYK2"
result2="./result/protein_ranking/TYK2"

# run active learning cycle for 5 iters with pure greedy strategy
bash ./active_learning_scripts/run_al.sh 5 0 path1 path2 result1 result2
```