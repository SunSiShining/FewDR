# FewDR

The repository is about our PRICAI 2024 work **["Are Dense Retrieval Models Few-Shot Learners?"]()**. 


## Results

The results on FewDR benchmark can be found in [FewDR-Results.csv](./FewDR-Results.csv).

The checkps and logging files will be available soon.

## Requirements

The installation requirements here are the same as those for [ANCE-Tele](https://github.com/OpenMatch/ANCE-Tele/README.md).


## FewDR Dataset

The files of the FewDR dataset are as follows:

|Filename|Description|
|:---|:---|
|[tot_classes_pattern_60.txt](data/tot_classes_pattern_60.txt)|All classes' query template|
|[queries.tsv](data/queries.tsv)|All queries|
|[qid2num.json](data/qid2num.json)|The mapping file between qid and qid-num|
|[queries_answers.jsonl](data/queries_answers.jsonl)|All queries and corresponding answers|
|[tot-qid_query_answer_positive.jsonl](data/tot-qid_query_answer_positive.jsonl)|All queries, answers, and positive passages|
|[wikipedia-corpus-index.tar.gz](https://thunlp.oss-cn-qingdao.aliyuncs.com/PaperData/EMNLP2022/ANCE-Tele/wikipedia-corpus-index.tar.gz)|Wikipedia corpus|

[tot-qid_query_answer_positive.jsonl](data/tot-qid_query_answer_positive.jsonl) contains the entire dataset information. One samples per line, formatted as follows:
```
"class": "P17",
"qid": "P17_97",
"qid-num": "24597",
"question": "which country is sharm el sheikh international airport located in",
"answers": ["egypt"],
"positive_ctxs": [
  {"title": "Sharm El Sheikh International Airport",
  "text": "Sharm El Sheikh International Airport is an international airport located in Sharm El Sheikh, Egypt. It is the third-busiest airport ...",
  "score": 1000,
  "passage_id": "9581501"}
  ],
}
```
## FewDR Benchmark

The statistics of the FewDR dataset are as follows:

|Split|Classes|Queries|#Train Qry|#Test Qry|Corpus Size|
|:---|:---|:---|:---|:---|:---|
|All| 60| 41,420| 20,726| 20,694| 21,015,324|
|Base| 30 |20,668 |10,341 |10,327 |21,015,324|
|Novel |30 |20,752 |10,385 |10,367|21,015,324|

The splitation strategy is written in the [split-stg.json](data/split-stg.json) file:
```
{

"base":{
    "P20": { ## Base Class 1 ID
            "train": [qid-num_1, qid-num_2, ..., ], ## train qid-num list of P20
            "test":  [qid-num_3, qid-num_4, ..., ], ## test qid-num list of P20
           },
    "P22": { ## Base Class 2 ID
            "train": [qid-num_5, qid-num_6, ..., ], ## train qid-num list of P22
            "test":  [qid-num_7, qid-num_8, ..., ], ## test qid-num list of P22
           },
    ....

       },

"novel":{
    "P17": { ## Novel Class 1 ID
            "train": [qid-num_11, qid-num_12, ..., ], ## train qid-num list of P17
            "test":  [qid-num_13, qid-num_14, ..., ], ## test qid-num list of P17
            },
    "P19": { ## Novel Class 2 ID
            "train": [qid-num_15, qid-num_16, ..., ], ## train qid-num list of P19
            "test":  [qid-num_17, qid-num_18, ..., ], ## test qid-num list of P19
            },
    ......

        },
}

```


## Expriments


### Training


Before training, we should tokenize the training data. Here is a toy of tokenized FewDR base train data: [train.json](data/train.json). The format of the tokenized data is as follows:

```
{
  "qid-num": "query id number in string format",
  "query": [train-query tokenized ids],
  "positives": [[positive-passage-1 tokenized ids], [positive-passage-2 tokenized ids], ...],
  "negatives": [[negative-passage-1 tokenized ids], [negative-passage-2 tokenized ids], ...],
}
```

After data preprocessing, we can enter the folder `FewDR/shells` and run the corresponding shell script to train your model.


**For zero-shot train**, use only our base-train data as train data and run following command:

```shell
bash zero-shot-train.sh
```

**For full-shot train**, use both base-train and novel-train data and run following command:

```shell
bash full-shot-train.sh
```

**For few-shot train**, set your zero-shot trained model as pretrained model,  use both base-train and novel-train data and run following command:

```shell
bash few-shot-train.sh
```

**Different few-shot seeds can be set in the `few-shot-train.sh` shell script. Here we use 5 different seeds (41,42,43,44,45).**

Multi-GPU training is supported. Please keep the following hyperparameters unchanged and set `--negatives_x_device` when using multi-GPU setup.

| Hyperparameters                        | Augments                      | Single GPU | E.g., Two GPUs |
| :------------------------------------- | :---------------------------- | :--------- | :------------- |
| Qry Batch Size                         | --per_device_train_batch_size | 32         | 16             |
| (Positive + Negative) Passages per Qry | --train_n_passages            | 2          | 2              |
| Learning rate                          | --learning_rate               | 5e-6       | 5e-6           |
| Total training Epoch                   | --num_train_epochs            | 40         | 40             |


P.S. For more training & inference techniques, please see ANCE-Tele/README.md



### Inference and Evaluation

After model training, we can use our shell scripts to build inference and make evaluation for our checkpoints.
The format of test query and corpus used in our inference shell script is the same as ANCE-Tele (NQ):


(1) Zero-shot and Full-shot inference:

```shell
bash zero-full-shot-inference.sh
```

(2) Few-shot inference:

```shell
bash few-shot-inference.sh
```


## Contact Us

For any question, feel free to create an issue, and we will try our best to solve. If the problem is more urgent, you can send an email to me at the same time 🤗.

```
NAME: Si Sun
EMAIL: sunsi.shining@gmail.com
```
