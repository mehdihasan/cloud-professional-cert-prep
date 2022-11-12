# Measuring, Monitoring, and Troubleshooting Machine Learning Models

`machine learning algorithms` ___->___ use `data` to identify `patterns` ___->___ predict values, classify objects, recommendation engine.

## Types of machine learning algorithm

1. __Supervised learning__
   1. `classification model`: predict a discrete value.
      1. `binary classifier`: answers YES or NO.
      2. `multiclass classification model`: can have many different or a set of answers.
      3. `support vector machines (SVMs)`: represent instances in a multidimensional space.
      4. `decision trees`: builds a model as a series of decisions about features.
      5. `logistic regression`: a statistical model based on the logistic function, which produces an S-shaped curve known as a sigmoid and is used for binary classification.
   2. `regression model`: predict a continuous/numeric value.
      1. `Simple linear regression models`: use one feature to predict a value.
      2. `Multiple linear regression models`: use two or more features to predict a value.
      3. `Simple nonlinear regression models`: use a single feature but fit a non-straight line to data.
2. __Unsupervised learning__: find patterns in data without using predefined labels.
   1. `Clustering` or cluster analysis, is the process of grouping instances together based on common features.
   2. `K-means clustering`: a technique used for partitioning a dataset into k partitions and assigning each instance to one of the partitions. It works by minimizing the variance between the instances in a cluster.
   3. `K-nearest neighbors algorithm`: takes as input the k closest instances and assigns a class to an instance based on the most common class of the nearest neighbors.
   4. `Anomaly detection`
      1. `Point anomalies`
      2. `Contextual anomalies`
      3. `Collective anomalies`
3. Reinforcement learning algorithm

|             | SL                                                      | UL                                                                                                                      | RL                                                                               |
| ----------- | ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| usage       | classification and regression                           | find patterns                                                                                                           | learning and adapting                                                            |
| learn from  | labeled examples                                        | starts with unlabeled data and identifies salient features, such as groups or clusters, and anomalies in a data stream. | by interacting with its environment and receiving feedback on the decisions that |
| it makes.   |
| application | fraudulent detection, medical, categorize news, insight | clustering, anomaly detection, collaborative filtering                                                                  | robotics, game playing                                                           |

## Deep learning / Neural Network

Deep learning is not a kind of problem but a way to solve ML problems. It can be used with any of the three types of ML problems. Successfully used on problems that do not lend themselves to solutions with linear models.

- `activation function`: function that maps from inputs to outputs.
  - `rectified linear unit (ReLU)`:  weighted sum of inputs is equal to or less than zero, the ReLU outputs a 0. When the weighted sum of inputs is greater than zero, the ReLU outputs the weighted sum.
  - hyperbolic tangent (TanH)
  - sigmoid function
- vanishing gradient problem

## Engineering machine learning models

- Model Training & Evaluation
  - Data Collection & Prepration
  - Feature Engineering: process of identifying which features are useful for building models.
  - Training Models
  - Evaluating Models
    - Confusion Matrix: True Positive (TP), False Positive (FP), False Negative (FN), True Negative (TN). True Positive & True Negative are always welcome. False Positive or False Negative need to be accepted based on the nature and requirement of the system.
  
    | Algorithm Type | Performance Metrics | Description                                                                                         |
    | -------------- | ------------------- | --------------------------------------------------------------------------------------------------- |
    | Regression     | R-Squared Value     | 0 to 1. The bigger (near to 1) the better accuracy.                                                 |
    | Classification | Accuracy            | accuracy = (TP + TN) / (TP + FP + FN + TN)                                                          |
    |                | Precision           | P = Real Positive / (TP + FP). Answers how many right identifications made. Near to 100% - less FP  |
    |                | Recall              | R = TP / (Real Positive + FN). Quantify how many we have failed to identify. Near to 100% - less FN |
    |                | F1 Score            | Harmonic Mean of Precision and Recall                                                               |
    |                | Specificity         | the bigegr the better.                                                                              |
    |                | ROC Curve           | how better the model is to differiencite to different classes                                       |
- Operationalizing ML Models
  - Deploying Models
  - Serving Models
  - Monitoring Models
  - Retraining Models

## Common sources of error in machine learning models

- Data Quality
  - Missing Data
  - Invalid Data
  - Inconsistent use of codes and categories
  - Data that is representative of the population at large
- Unbalanced training sets
- Bias in training data
  - Automation bias
  - Reporting bias
  - Group attribution bias

## Questions

1. What is labeled and unlabeled data?
   > If you wanted to build an ML model to predict anything, you could use tabular data. Supervised learning algorithms use one of the columns of data to represent the value to be predicted. In ML terminology, this column is called a label.

## References

1. [Andrew Ng’s “Machine Learning” course on Cousera](www.coursera .org/learn/machine-learning)
2. [Eric Beck, Shanqing Cai, Eric Nielsen, Michael Salib, and D. Sculley, “The MLTest Score: A Rubric for ML Production Readiness andTechnical Debt Reduction,”](https://research.google/pubs/pub46555/)
3. [supervised ML](https://en.wikipedia.org/wiki/Supervised_learning)
4. [unsupervised ML](https://en.wikipedia.org/wiki/Unsupervised_learning)
5. [reinforcement learning](https://en.wikipedia.org/wiki/Reinforcement_learning)
6. [Jianqing Fan, Cong Ma, and Yiqiao Zhong, see “Selective Overview of Deep Learning,”](https://arxiv.org/pdf/1904.05526.pdf)