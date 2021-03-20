# FactExsum-coling2020
Code for Fact-level Extractive Summarization with Hierarchical Graph Mask on BERT (coling 2020)

The CNN/DaliyMail dataset we use is directly from the chunked data in https://github.com/JafferWilson/Process-Data-of-CNN-DailyMailv, Download FINISHED FILES.
The chunked data is put in /data/DMCNN/...

If you are interested in the fact-level CNN/DaliyMail dataset described in our paper, you can download them here: https://drive.google.com/file/d/1ma0uuXd5b2EgMUslRIGGF6pVPFHBCIs-/view?usp=sharing.

-----------------------------------------------------------------------------------------------------------------------------

Introduction for the files:

  /data/DMCNN/...: use to store the chunked CNN/DaliyMail dataset.

  /data/raw_data_loader.py: use to extract article-summary pair from the chunked data.

  /data_file/DMCNN/...: use to store the pickle files that contain processed data generated by make_data.py, and there are some examples in the folder. You can obtain the    complete organized fact-level data with the link above.

  /model/BERT.py: it contains BERT encoder with Hierarchical Graph Mask and the classifier for extractive summarization.

  /utility/pyrougex.py: use to evaluate the result with ROUGE.

  /utility/utility.py: it contains some functions used in make_data.py.

  call_rouge.py: use to evaluate the result with ROUGE.

  data_loader.py: data loader for training and testing the model, and it convert the data in pickle files into the form that used for BERT. It also construct the mask matrix.

  make_data.py: split the chunked data into fact level and process the data. The output are pickle files stored in data_file.

  run.py: use to train and test the model. 

---------------------------------------------------------------------------------------------------------------------------
Dependency:

stanford_openie           1.0.3

stanfordcorenlp           3.9.1.1

pyrouge                   0.1.3

rouge                     1.0.0

pytorch                   1.6.0    

pytorch-pretrained-bert   0.6.2

transformers              2.11.0


The use of the code:

(1) Create the processed pickle files to train the model: 
```
python make_data.py --nlp_path /home/ziqiang/stanfordnlp_resources/stanford-corenlp-full-2018-10-05 --data_path 'data/DMCNN/train*' --output_path 'data_file/DMCNN/train_file/'
```
nlp_path: the path for the stanfordnlp.

data_path: where you store the chunked CNN/DaliyMail dataset, for training files, 'path.../train*', for val files , 'path.../val*', for test files, 'path.../test*'.

output_path: where you store the processed pickle file.

The process will take a relatively long time (one to several days).

(2) Train the model:
```
python run.py --do_train --device 0 --train_size 32 --checkmin 60000 --checkfreq 6000
```
device: the gpu used for the training, no support for multiple gpus.

train_size: the batch size.

checkmin: the minimum step for saving the checkpoint.

checkfreq: for every checkfreq step, save a checkpoint.

The checkpoint will be saved in save_model folder.

(3) Test the model:
```
python run.py --do_test --device 0 --test_model 1---1-126000-0.2923-0.3453.pth.tar --block_trigram 1 --ext_num 4
```
device: the gpu used for the testing, no support for multiple gpus.

test_model: the name of the checkpoint used for testing, the checkpoint should be in the save_model folder.

block_trigram: whether to use the block trigram to reduce the redundancy.

ext_num: the number of the sentences that composed of the summary.

The program will output the result in result folder, checkpoint_cand.txt and checkpoint_gold.txt.


---------------------------------------------------------------------------------------------------------------------------

The output summary of our model "our s+f" is in result folder, the our s+f_cand refers to the standard setting described in our paper and our s+f 6_cand represents the result that extract 6 facts rather than 4 facts.

