## Title
abstract:

## Instruction on running our model

### Direct inference
Colab demo for code inference with given protein and unmeasured ligands.

https://colab.research.google.com/drive/11Fx6mO51rRkPvq71qupuUmscfBw8Dw5R?usp=sharing

### Few-shot fine-tuning
Colab demo for few-shot fine-tuning with given protein, few measure ligands for fine-tuning and unmeasured ligands for testing.

https://colab.research.google.com/drive/1gf0HhgyqI4qBjUAUICCvDa-FnTaARmR_?usp=sharing

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
to speed up the active learning process, you should modify the unicore code 
1. find the installed dir of unicore (root-to-unicore)
```
python -c "import unicore; print('/'.join(unicore.__file__.split('/')[:-2]))"
```

2. goto root-to-unicore/unicore/options.py line 250, add following line
```
    group.add_argument('--validate-begin-epoch', type=int, default=0, metavar='N',
                        help='validate begin epoch')
```

3. goto root-to-unicore/unicore_cli/train.py line 303, add one line
```
    do_validate = (
        (not end_of_epoch and do_save)
        or (
            end_of_epoch
            and epoch_itr.epoch >= args.validate_begin_epoch # !!!! add this line
            and epoch_itr.epoch % args.validate_interval == 0
            and not args.no_epoch_checkpoints
        )
        or should_stop
        or (
            args.validate_interval_updates > 0
            and num_updates > 0
            and num_updates % args.validate_interval_updates == 0
        )
    ) and not args.disable_validation
```

4. run the active learning procedure
```
path1="path to checkpoint of pocket_ranking"
path2="path to checkpoint of protein_ranking"
result1="./result/pocket_ranking/TYK2"
result2="./result/protein_ranking/TYK2"

# run active learning cycle for 5 iters with pure greedy strategy
bash ./active_learning_scripts/run_al.sh 5 0 path1 path2 result1 result2
```