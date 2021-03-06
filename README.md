# PredictiveEngagement

This repository contains code for [Predictive Engagement](https://arxiv.org/pdf/1911.01456.pdf) paper. If you use it please cite it as: 
```
@inproceedings{
    Ghazarian2020, 
    title={Predictive Engagement: An Efficient Metric For Automatic Evaluation of Open-Domain Dialogue Systems}, 
    author={Sarik Ghazarian, Ralph Weischedel, Aram Galstyan and Nanyun Peng},
    year={2020} 
}
```

For any comments/issues/ideas please feel free to contact [me](mailto:sarikgha@usc.edu).

#### TODO

Pipeline for user-friendliness: create CSV, feed CSV to get embeddings, use embeddings to predict scores for generated query/reply pairs. 

## Steps to setup

### Install Requirements
Use any virtualenv manager to install all the packages mentioned in the requirements.txt file.

For example, using Anaconda: 
```
$ conda create -n predeng python=3.6
$ conda activate predeng
$ pip install -r requirements.txt
```


### Preprocess dataset
```
$ python preprocess.py 
``` 

Run `preprocess.py` in the `pytorch_src` directory. This script preprocesses the ConvAI dataset (train.json file taken from http://convai.io/2017/data/) to extract the dialogs with at least one turn (query and reply utterances). 
The outputs are:
* ConvAI_convs_orig.txt : includes all 2099 conversations from ConvAI dataset with at least one turn of dialog
* ConvAI_convs.txt : includes all conversations from ConvAI except the 50 dialogs used for AMT experiments (Table 2. of paper)
* ConvAI_convs_train, ConvAI_convs_test, ConvAI_convs_valid: include 60/20/20 percent of conversations from ConvAI dataset as train/test/valid sets
* ConvAI_utts_train.csv, ConvAI_utts_test.csv, ConvAI_utts_valid.csv: train/test/valid sets of utterances from ConvAI dataset containing queries, replies and their corresponding engagement labels used for utterance-level engagement classifier.


### Utterance embeddings
In order to train the engagement classifier or test the trained model, you need to have a set of embeddings files for queries and replies, where each utterance embedding is the mean or max pooling of its words embeddings. In this paper, we have used the Bert contextualized embeddings.

Make sure to download the [BERT-Base, Uncased](https://storage.googleapis.com/bert_models/2018_10_18/uncased_L-12_H-768_A-12.zip) for generating the word embeddings. This is necessary for using the pre-trained engagement classifier. Larger models with different embedding size will return an error.

```
$ python create_utt_embed.py
```

Run create_utt_embed.py with queries and replies files as input to create their embeddings by using [Han Xiao's](https://github.com/hanxiao) [BertClient and BertServer ](https://github.com/hanxiao/bert-as-service). 


### Train/test model
Run main.py in order to train the engagement classifier, test it, finetune it on Daily Dilaog dataset and predict engagement score for queries and replies of Daily Dialoge test set.
Model directory includes the engagement classifier trained on ConvAI dataset and finetuned on Daily Dialog set. The finetuned model is based on mean pooling of word embeddings.
cd into pytorch_src/ directory and specify the mode and all the parameter values that you need to run. As an example, the command below uses the finetuned model in the model directory to predict engagement scores for queries and replies from test set of Daily Dialog dataset. With pooling argument specify what kind of pooling strategy should be used for sentence embedding.

```
$ python main.py --mode predict --pooling mean 
```


