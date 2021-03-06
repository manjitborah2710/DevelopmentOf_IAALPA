**Note: Please do not disrupt the directory structure while executing the codes.**

We may update this README in future. Please get an updated copy of the same using the following link.

For a clearer and updated view of this README file go to https://github.com/manjitborah2710/DevelopmentOf_IAALPA

**Requirements:**

`Python 3.5 or higher`
`Jupyter Notebook`

Libraries required:
`Networkx`
`Pandas`
`Numpy`
`Sklearn`
`Chefboost`
`Matplotlib`
`Pickle`
`Scipy`



# 1. Finding the AAIs for each network

Included in this folder are a series of **helper codes** for construction of training and testing datasets for both the Decision Tree 
as well as the Support Vector Machine Models. The scripts are not intended to function as standalone units, rather they are meant 
to be used in other driver functions. 


**computeAAI.py**

This contains the fuctions that compute the 8 different microstructure-based indices and 4 different macrostructure-based indices.
Each function takes in a _node pair_ and a _**networkx** graph_ as mandatory inputs.

>wa2 additionally takes as input the constant values _rho_ and _phi_
>rwa additionally takes as input the constant values _a_,_b_,_c_,_rho_,_phi_

All the inputs are entered in the order of mention


**getAAI.py**

The functions in this script are used to call the functions in *computeAAI.py* to generate the indices mentioned in the theory by 
passing the appropriate parameter values. For eg, 

>Index S22 is defined as _rwa_ with _a=1, b=1, c=1, rho=4, phi=1_ 
>This is computed by 
>
>```python
>def s22(pair,graph):
>   s = rwa(pair,graph,1,1,1,4,1)
>   return s
>```

All the functions in this file take a _node pair_ and a _**networkx** graph_ as mandatory inputs.


**auc.py**

This is the script that uses the above scripts to compute the AUC values used by the Decision Tree and the SVM. There are 3 primary
functions in the file with several other helper functions. 

>_getNetworkAUC(csvfile)_ takes in a CSV file _(.csv format)_ and returns a list of 25 AUC for 25 differrent AAI values.
>_getBestAUC(csvfile)_ takes in a CSV file _(.csv format)_ and returns the index of the best AAI based on the best AUC value.
>_getComplementaryAAI(csvfile, best_index)_ takes in a CSV file _(.csv format)_ and the _index of the best AAI_ and returns a list of 
the complementary AAIs based on the best AAI.

The file has comments that describe in detail what all the other functions do.


Apart from these scripts, there are two *IPYNB* files and a *Dataset* folder. _demo.ipynb_ demonstrates the working of the scripts and the above mentioned 
functionalities. _avg_auc_plot.ipynb_ has the plot of the avgerage AUC values and also the list of values. _Dataset_ has the datasets used
in these two IPYNB files. These files can be treated as the proof of execution. 
 

# 2. Finding optimal AAIs using decision tree

	1. The egonet training and test datasets are stored in "data/train" and "data/test" respectively. 
	2. "prepare_data.py" is used to build the dataset for training the Decision Tree. The train dataset prepared contains the 5 network features mentioned in the literature along with the best AAI decision as the target variable. The test dataset contains only the 5 network features. The target is to be predicted using the DT model built and validated against the actual results to calculate the accuracy.
	3. Run "C4.5_train.py" to train the DT model on the train dataset. Dataset is stored in test directory.
	4. Run "C4.5_test_prediction.py" for predicting the best AAI for the "sample_test.csv" file. A sample_test data is stored in "tests\" directory.
	Metrics like feature importance and accuracy are found. Per network prediction is also computed.  
	5. The formed decision tree is stored in "outputs/rules" directory.

The Decision Tree algorithm used for the above implementation is __C4.5 Decision Tree__. This is a very useful DT because it is well versed with both numerical and non-numerical features. It calculates entropy and gain ratio for determining feature importances. Thus, it performs much better than the traditional ID3 DT.

__prepare_data.py__ : This is the code for preparation of Decision Tree training and testing datasets. It takes in the files(with .csv extension) mentioned in 1 and computes network features for each file separately. Note that each file represents a specific network, so per network features are computed. The features computed are: _Average Degree_, _Average Clustering Coefficient_, _Average Shortest Path_, _Average Node Betweenness_ and _Average Link Betweenness_. These features act as independent variables for the Decision tree.
This file also takes in the ouput of the **auc.py** file for finding the best AAI for each network. The best AAI is the one having the highest AUC value (explained in detail in the first section). The AAI value is the dependent variable for the Decision tree. 
_For preparation of training dataset_, the AAI value is incorporated to train the DT. _For preparation of testing dataset_, remove the last column that is the AAI value, so that the DT can predict the best AAI. 
After running prepare_data.py, a training and testing dataset is generated for the DT. Also, note that in the datasets, the best AAI values are under the column _Decision_.

__C4.5_train.py__ : This is the code for training the DT. Metrics like feature importance of the independent variables are computed (while training the DT).

__C4.5_test_prediciton__ : This is the code for training the DT. The DT predicts the best AAI for a given network. We have tested the DT against a sample test file stored as "_tests/sample_test.csv"_ file. 

The built Decision Tree is stored in the _"outputs/rules"_ directory as _"rules.py"_ file, in the form of python if-statements. 
The proof of execution is provided in the _"Results\"_ directory.


# 3. Finding complementary AAIs using SVM

Go to **"3 Finding complementary AAIs with SVM\output\"** and carry out the following operations:

	(a) Run "1 GenerateDataFrame.py" and provide the path to the edgelist (prepared in CSV format) as the command-line argument. This generates a file "Dataset.csv". A sample dataset has been provided in the folder.

	(b) Run "2 prepare_test_sample_for_svm.py" and provide as the command-line arguments the path to the dataset produced as the output of (a) (Dataset.csv) and the optimal AAI index (starting from 0) for this network obtained from the results of the Decision Tree. This generates a file "test_sample.csv".

	(c) Run "3 find_comp_aais.py" providing the CSV, produced as a result of step (b) (test_sample.csv), as the command-line input.

**Note: The edgelist used in step (a) should correspond to the network for which we are finding the optimal and complementary AAIs.**

After the completion of step (c), we get pairs of AAI and a value 1 or -1 alongside them representing whether they are complementary or not respectively.

The training dataset for the SVM is located in **"3 Finding complementary AAIs with SVM\svm_train\"**. The file **svm_train.csv** is used for the training and has been carried out in the notebook **svm.ipynb**. To retrain the model, replace **svm_train.csv** with your own training set and execute all the cells of the IPython Notebook **svm.ipynb**. The model after exeution will be saved as **svm.pkl**.

The code for preparing the training dataset is placed inside **"3 Finding complementary AAIs with SVM\dataset_preparation\"**. There are two files:

**auc_new.py** - This is a slight alteration of the code **auc.py** mentioned in section _1. Finding the AAIs for each network_. This file is imported in the next code.

**prepare_train_data_for_svm.py** - This is the main code that will generate a training sample for the SVM given a network. This preparation is done based on random sampling from the node pairs of the network edgelist provided in .csv format. To specify the network from which the training sample is to be prepared, before running the code, replace the path to the csv edgelist in the following line of this file:

>```python
> res=auc_new.main("sample.csv")
>```

Please note that the **auc_new.py** file must be in the same directory as **prepare_train_data_for_svm.py**. There is no need to run **auc_new.py**, this file is imported in **prepare_train_data_for_svm.py** as a module and acts as a helper code for its successful execution. After the execution of this file, a file **train_sample.csv** is produced. Run this code a few times on different networks to generate train samples. As mentioned in the report, to balance the number of complementary and non-complementary AAI pairs for unbiased training of the SVM, each of the generated train samples are filtered manually and finally merged. SVM is then trained on this merged dataset.

The proof of execution has been provided inside folders **output** and **svm_train**. Since the **dataset_preparation** folder consists only of helper code and won't generate any console I/O, the execution of the files is not shown.

**Important**

The index for the different AAIs are mentioned in the following table:

| AAI | Index|
| --- | ---|
| S1  | 0  |
| S2  | 1  |
| .   | .  |
| .   | .  |
| S25 | 24 |


Please refer to the literature followed for this project to have a full understanding of the algorithms **S1,S2,S3...,S25**.

Also please note that the edgelist in csv format must contain only 2 columns the **Source** and **Target** respectively.

# 4. Finding composite AAI

**composite.py** has a function _getCompositeAAI_ which takes input parameters _node pair, networkx graph, best index value, complementary 
indices list,_ and,_all the AUC values for the AAIs_. This function can be used to compute the composite index.

_composite.ipynb_ is the Jupyter notebook implementation. 
