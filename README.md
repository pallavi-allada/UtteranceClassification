# Utterance Classification

## Problem Statement

Assignment: 

- Create a user utterance identifier where any utterance from a user needs to be identified as one of the target labels. 
- The dataset for the problem statement is provided in the attached file "utterance_id.csv".
- The dataset needs to be analysed properly before starting on the training process.
- The solution should be able to read from csv and provide the inference results.
- The solution will be evaluated on the user dataset. The inference time of each request should be in the range of ~50ms on CPU.
 
## Analysis of Data: 

- There are 4597 records with 2 columns of interest - question and tags.
- Data provided has 7 target labels - Contract, Email, Calendar, Contact, Document, Employee, Keyword.
- There is a high imbalance in the data, as classes - Document, Keyword and Employee have very few samples. <IMG SRC="./images/Target Distribution.png">
- To treat the imbalance, downsampling the majority classes like Contract, Email, Calendar and Contact is not a good idea as the total number of samples are very few already. We can upsample the minority classes instead to create some synthetic data using random insertion or back-translate techniques and experiment later. For now, proceeding with the data as-is.
- Some of the words like "email" appear in clouds for Contact and Email, "number" appears in both Document and Contact.Likewise,, if we go through Keyword cloud there seems to be noise (scratching my head!!) like Hjd, krjhgkfjh, Xxdddd, Hggggjkkkm, fdsjfdlk. Should we ignore the keyword class, and proceed with just 6 classes? For now, proceeding with the data as-is. <IMG SRC="./images/Word Clouds.png">

- Length of the longest question is 28 words. Most of the sentences are within 20 words. <IMG SRC="./images/Word Count.png">

## Alternatives
 
The data provided was split into train and test (used for validation), and written to 2 CSV files - 4137 records for training and 460 records for validation, to be able to benchmark all models on same test data. Actual test accuracy can be identified at inference.

Pointers - 
- Word2vec was used for embeddings as the sentences are small, also, the language matches the regular English. So, no additional domain language fine tuning was done.
- Many of the words with missing vector representations, were actually names of people, and some stop words. Since stop-word removal was intentionally not done and it anyways does not make much difference as we add 0-vectors for them.
- To make the embeddings more congtext aware, we used the transformers based approach - BERT. 
- DistilBertModel is a relatively lighter model compared to BertForSequenceClassification and inference time is comparitively faster too, with equally great accuracy. 

|Model|Accuracy| Model size(MB)|Inference time(ms)|
|---|---|---|---|
| word2vec with KNN | 0.94 | 10 MB | 43 ms|
| word2vec with RandomForest | 0.90 | 9 MB | 154 ms|
| word2vec with GradientBoosting | 0.93 | 0.861 MB | 30 ms|
| BertForSequenceClassification |  0.96 | 417 MB | 182 ms|
| DistilBertModel | 0.97 | 251 MB | 129 ms|
  
Confusion Matrix for models using Word2Vec</BR>
  
<IMG SRC="./images/Word2Vec CM.png">
<BR>
Confusion Matrix for models using BERTForSequenceClassification
<BR>
<IMG SRC="./images/BERTSeqClass CM.png">
<BR>
Confusion Matrix for models using DistilBERT
<BR>
<IMG SRC="./images/DistilBERT CM.png">
<BR>
  
