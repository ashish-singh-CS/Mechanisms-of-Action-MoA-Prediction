# Drugs' Mechanism of Action Prediction

## Abstract
*Drug development process is replete with a lot of difficulties and obstructions.  
Towards drug discovery, it is a prudent course of action to first get hands on  
the understanding of how a drug works before any experiment or observation is  
started. In pursuit of achieving successful drug discovery, algorithms have been  
designed that classify drugs based on their biological activity. The goal of   
this project is to address a multilabel classification problem, “Mechanisms of   
Action (MoA) Prediction”, a competition hosted by Kaggle [1]. The core objective  
is to develop an algorithm that automatically labels each case in the test set  
as one or more MoA classes. This project deals with a unique imbalanced dataset  
that combines gene expression and cell viability data in addition to the MoA   
annotations for more than 5,000 drugs. To begin with the solution, Support   
Vector Machine (SVM) and hierarchical methods of computation were implemented   
along with the application of MultilabelStratifiedKFold cross validation   
technique and K-Means algorithmic approach. Towards meeting the competition’s   
computation time constraint, GPU boost algorithm was implemented with neural   
network computation system. This project used log loss as the evaluation metric.  
Prioritizing only important features on top of hyperparameter tuning and removal  
of highly similar samples provided better results. One of the challenges was   
the unavailability of such libraries that could implement oversampling of   
minority or undersampling of majority for multi labeled dataset. For improving  
the proposed model’s performance further, it would be helpful to have such   
libraries to handle the manipulation in overall noise for underrepresented classes.*


Problem Statement
=================

The goal of this project is to predict multiple targets of the MoA response(s)  
of different samples given various inputs. The project comes with a unique   
imbalanced dataset that combines gene expression and cell viability data along  
with MoA annotations for more than 5,000 unique drugs as mentioned earlier. The   
dataset describes the responses of 100 different types of human cells to various  
drugs. Those response patterns will be used to classify the MoA reponse. The  
evaluation metric is the mean column wise log loss. The dataset has been split  
into testing and training subsets. Hence, the task is to use the training   
dataset to develop an algorithm that automatically labels each case in the test  
set as one or more MoA classes. Since drugs can have multiple MoA annotations,  
the task is formally a multi-label classification problem.

Notations
---------

For every sample, represented by sig\_id, it is required to predict the   
probability that the sample had a positive response for each [MoA] target.   
For N sig\_id rows and M [MoA] targets, there should be N x M predictions  
and the prediction will be scored by the log loss formula mentioned in   
section 5.2 where,

-   N is the number of sig\_id observations in the test data (i=1,…,N)

-   M is the number of scored MoA targets (m=1,…,M)

-   Y<sub>i,m</sub> is the predicted probability of a positive MoA response for a sig\_id

-   <span>y</span><sub>i,m</sub> is the ground truth, 1 for a positive response, 0 otherwise

-   log() is the natural (base e) logarithm


Methods and Techniques
======================

Preparation and Data Overview
-----------------------------

We started our project with the basic preparations and analysis of the given  
dataset. We loaded necessary libraries to begin with the solution and loaded   
the data using Pandas. Then we had an overview of the provided dataset to  
decide what needs to be done during preprocessing.


### Data Overiew

| Dataset             | Samples       | Features     |
| ------------------- |:-------------:|:-------------:
| Train               | 23814         | 876          |
| Test                | 3982          | 876          |
| Scored Targets      | 23814         | 207          |
| Non-Scored Targets  | 23814         | 403      | 
| Sample Submissions  | 3982          | 207

Data Preprocessing
------------------

Preprocessing was a necessary step to get the data in a good shape. Since we   
were determined to implement neural networks, we ensured that the inputs were  
in a standard format. During preparations, we also ensured that there were no   
missing values. We removed the id column for the samples in the train dataset   
and test dataset which were represented as sig\_id. We extracted the gene   
expression features each denoted by \`\`g-“. and cell viability features each  
denoted by \`\`c-” separately. We also embedded the categorical features.

Model Building
--------------

### Used Libraries

-   NumPy

-   Pandas

-   SciKit-Learn

-   TensorFlow

-   XGBoost

### Methodology

We started building our model applying SVM and followed K-Means algorithmic   
approach. However these approaches exceeded the time limit set from Kaggle.   
We tried reducing features using principal component analysis (PCA) and then  
applied SVM and K-Means algorithms. Nonetheless, our methodology was not   
getting to the proper direction as per expected by Kaggle. We then tried to  
find some features of interest using t-distributed stochastic neighbor   
embedding (TSNE) to graph the data but we found nothing considerable. Then  
we moved on to applying the XGBoost method to extract important features   
and decided to implement a neural network. It reduced the features from 876  
to 821. At this point, we implemented the StratifiedKFold technique again.   
We designed a neural network in two hidden layers in between the input and   
output layer. We initially used Sigmoid as the activation function but however  
Relu gave us better results. We tried to optimize our results with   
hyperparameter tuning. We tested with varying epochs, dropouts and batch  
sizes. We used binary cross entropy as our log loss metric which was aligned  
with the evaluation metric set by Kaggle. Through multiple attempts we   
gradually developed our model that lowered the log loss and improved our  
rank on the Kaggle leaderboard.

Post Processing
---------------

We did a sanity check to ensure that the final submission file contains the   
prediction results as expected and all features and sample IDs are in good shape.

Discussion and Results
======================

Datasets
--------

-   **Training Dataset:** Features for the training dataset was provided in the   
    **train\_features.csv** file. This is a dataset with almost 900 columns. From   
the data description we came to know that features starting with \`\`g-" encode  
gene expression data and features starting with \`\`c-” implied cell viability   
data. There were total 772 gene expression and 100 cell viability features.   
Moreover, there were 3 more features. cp\_type feature incidated the sample   
treatment, cp\_time feature indicated the duration of treatment and cp\_dose   
feature encode dosage of the treatment. The sig\_id column was for   
identification of each sample.

-   **Scored Train Targets:** The binary MoA targets were provided in the   
    **train\_targets\_scored.csv** file that were scored. The target response  
    had over 200 different binary outputs giving a sparse matrix.

-   **Non-Scored Train Targets:** Additional non-scored binary targets, that  
    were neither predicted nor scored, were provided through   
    **train\_targets\_nonscored.csv** file. These additional targets contained  
    about 400 classes.

-   **Test Data:** The test data contained the same features as the train data,  
    and had about 3982 samples compared to the 23814 samples of the training data.  
The test dataset was provided through **test\_features.csv** file

Exloratory Data Analysis
------------------------

With reference to [6], we found that about 40% of samples had zeros in all   
columns and more than 50% have only one active target column shown in figure 1.   
Also, figure 2 shows that the biggest number of positive samples for 1 target  
column is 3.5%.

![Pie Chart](https://github.com/ashish-singh-CS/Mechanisms-of-Action-MoA-Prediction/tree/main/img/data2.png?raw=true)

[fig: Number of activations in targets for every sample (in percent)]

![Chart](https://github.com/ashish-singh-CS/Mechanisms-of-Action-MoA-Prediction/tree/main/img/data1.png?raw=true)

[fig: Percent of positive records for every column in target]

As it is clearly visible from the analysis, the dataset provided to us was really imbalanced.
