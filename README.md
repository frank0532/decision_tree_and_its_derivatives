# Illustrate decision tree and its derivatives

## 1. Decision tree, code in "decisionTree.py"

### 1.1 What is decision tree? Build an "if-elif-else" condition tree according to some certain data which contains useful information that is really used to predict for future new data;
>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/data2decision_tree.png)

### 1.2 How to build it, i.e. how to arange features(columns' names of "Data") on the tree, some features at first layers and others later? 

When coming to the decision tree in Fig_1, why is "outlook" on the top rather than "Humidity" or "Wind"? In the fact the top one is selected by certain algorithms, such as ID3, C4.5, Cart and so on.

#### 1.2.1 ID3
>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/ID3.png)
#### 1.2.2 C4.5
>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/C4.5.png)
#### 1.2.3 Cart
>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/Cart.png)

## 2. Random Forest, code in "randomForest.py"

Select n_r lines and n_c columns randomly from training data matrix to re-build a new data matrix; doing like this n times, n new data matrixes can be there, that is D1~Dn;Take for example, this random forest is formed by just 3 decision trees as below.
>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/randomForest.png)

Each new data matrix "Di" (and its related label) can be used to train a decision tree, and at last n decision trees are there; these decision trees are grouped into a random forest; Now this random forest can be used to predict a new sample which would be predicted by each decision tree in the random forest independently; and the final predict of random forest is decision trees' majority vote result, i.e. decision trees' predicts are [1,1,-1], then the final predict is '1'; and if [-1,1,-1], the final predict is '-1';

## 3. Adaboost, code in "adaboost.py"

As random forest, adaboost also has many decision trees but difference is that these decision trees are dependent one by one in order because of the dependent sample weights for different decision trees. Take for example, this adaboost is formed by just 3 decision trees as below.

>> ![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/adaboost.png)

Training(mainly used for classifier, label is '1' or '-1'): the first decision tree is trained just as common decision tree because weights of all samples are the same (that is w1), and then get α1（details would be explained later）; the second decision tree is trained with sample weights:w2 which is updated by w1, label, predict1(the fisrt decision tree's predict) and α1, and then also get α2; the third decision tree is trained like the second one, and also get α3.

Predicting: the first decision tree predicts a new sample as predict1, the second decision tree predicts it as predict2 and also predict3; then if α1*predict1+α2*predict2+α3*predict3 >0, the label for this new sample should be '1',  or it should be '-1'.

Then what is α and how to get it?
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/alpha.png)

At last why α and w should be like that?
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/deduce-alpha-w.png)

## 4. GBDT, code in "GBDT.py" and "GBDT_LR.py"

As both random forest and adaboost, GBDT also has many decision trees but the special is that the labelS for each tree are different and different labels for these decision trees are dependent in order. Take for example, this GBDT is formed by just 3 decision trees as below.
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/GBDT.png)

Training(mainly used for regression): the first decision tree is trained just as common decision tree because "y1" of all samples are the same as label(that is y), after that "predict1" can be gotten by the first decision tree, then α1 can be gotten by linear regression on y and "predict1"; the second decision tree is trained with y2(that is y2=y-α1*predict1),after that "predict2" and "α2" can be gotten too; at last "predict3" and "α3".

Predicting: the first decision tree predicts a new sample as predict1, the second decision tree predicts it as predict2 and also predict3; then final predict is "α1*predict1+α2*predict2+α3*predict3".

Then why each label of decision tree should be like that?

Because of square loss function and gradient.
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/square-gradient.png)

## 5. Merge random forest,adaboost and GBDT, code in "merge_model_simplified.py" and "merge_model_complicated.py"

### 5.1. Simplified:

>> Create “random forest” which includes n_1 “big Trees”;

>> 	Each “big Tree” in “random forest” is created by n_2 trees which are combined by “GBDT”;

>> Each tree in “GBDT” is trained by “sample_weight” according to “Adaboost”;

Note: this method seems to run well until now;

### 5.2. Complicated: 

>> Create “random forest” which includes n_1 “big Trees”;

>> Each “big Tree” in “random forest” is created by n_2 “big Trees” which are combined by “Adaboost”;

>> Each “big Tree” in “Adaboost” is created by n_3 trees which are combined by “GBDT”;

>> Each tree in “GBDT” is trained according to “GBDT” completely;

Note: this method seems to have some problems in “Adaboost” stage.

## 6. XGBoost

XGBoost has much similarity with GBDT, especially on object function which is also the most important modification for it; XGBoost also have a list decision trees in order (in training stage) and its final predict is also sum the predicts of all these decision trees, which is very similar with GBDT;
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/xgboost-0.png)

### 6.1. object function

Besides the item calculating loss like GBDT, object function also add a item which calculates number of leaves of tree and each value of leaf(using regularization L1 and L2 respectively), which can decrease overfitting in training course.

>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/object-function.png)

then get 'w' of object function by minimizing object function value.

>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/resolve-XGB-object-function.png)

### 6.2. Shrinkage and Column Subsampling

>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/shrinkage-subsample.png)

### 6.3. Others

Approximate Algorithm, Sparsity-aware Split Finding, Column Block for Parallel Learning and so on.

## 7. lightGBM

The basic structure is all most the same as XGBoost, but there are some difference between them for some details; and because of update of XGBoost and lightGBM, they learn from each other.

### 7.1. growing the tree, leaf wise, used by both
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/leaf-wise.png)

### 7.2. Histogram-based method, used by both
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/histogram.png)

### 7.3. Ignoring sparse inputs, used by both
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/sparse.png)

### 7.4. Subsampling the data, Gradient-based One-Side Sampling, used by lightGBM only

Don't use all samples but samples that have big loss on before decision tree and a few small-loss sampels are used to train the new decision tree, like adaboost method a little.

### 7.5. Exclusive Feature Bundling, used by lightGBM only

Merge two features into one when they have little relationship as below picture.
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/feature-bundling.png)

## 8. CatBoost

CatBoost are similar with XGBoost and lightGBM, but also make many modification to improve the model, the most famous modifying is about Categorical features.

The value of categorical feature is calculated according to label values(that is Y) by below formula.
>>![](https://github.com/frank0532/decision_tree_and_its_derivatives/blob/master/figs/catboost.png)

