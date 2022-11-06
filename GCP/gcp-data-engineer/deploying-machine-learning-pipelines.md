# Deploying Machine Learning Pipelines

Machine learning projects begin with a problem definition - e.g.

1. improve sales revenue by making product recommendations to customers,
2. evaluate medical images to detect tumors,
3. or how to identify fraudulent financial transactions.

## Structure of ML Pipelines

1. Data Ingestion (Cloud Storage / Cloud PubSub)
   1. batch - cloud storage (data lake)
   2. streaming - cloud pub-sub
2. Data Preparation (DataProc / DataFlow / DataPrep): process of transforming data from its raw form into a structure and format
    1. Data Exploration (DataPrep, Cloud Data Fusion)
       - distribution of data: descriptive stastics, histogram, cardinality
       - quality of data: missing or invalid values
    2. Data Transformation (Cloud DataPrep, Cloud DataFlow): mapping data from its raw form into data structures and formats that allow for machine learning
    3. Feature Engineering: process of adding or modifying the representation of features to make implicit patterns more explicit
3. Data segregation: splitting a data set into three segments
   1. Training Data
   2. Validation Data
   3. Test Data
4. Model Training: fitting a model to the data.
   1. Feature Selection: process of evaluating how a particular attribute or feature contributes to the predictiveness of a model.
   2. Underfitting, Overfitting, and Regularization
5. Model Evulation
   1. Individual evaluation metrics: Accuracy, Precision, Recall, F1 Score
   2. K-fold cross validation: K-fold cross validation is a technique for evaluating model performance by splitting a data set into k segments, where k is an integer. For example, if a dataset were split into five equal-sized subsets, that would be a fivefold cross-validation dataset.
   3. Confusion matrices: Confusion matrices are used with classification models to show the relative performance of a model.
   4. Bias and variance
      - Bias: Bias is the difference between the average prediction of a model and the correct prediction of a model.
      - Variance: helps to understand how model prediction differs across multiple training datasets.
6. Model Deployment
   1. Cloud AutoML: AutoML Vision, AutoML Video Intelligence, AutoML Natural Language, AutoML Translation,  AutoML Tables
   2. BigQuery ML: BigQuery ML enables users of the analytical database to build machine learning models using SQL and data in BigQuery datasets.
      - Linear regression for forecasting
      - Binary logistic regression for classification with two labels
      - Multiple logistic regression for classification with more than two labels
      - K-means clustering for segmenting datasets
      - TensorFlow model importing to allow BigQuery users access to custom TensorFlow models
   3. Kubeflow: help run TensorFlow jobs on Kubernetes - multicloud.
   4. Spark Machine Learning:
7. Model Monitoring

ML ppelines are more `cyclic` than liner (dataflow).

## Further Exploration

1. [Python for Data Science](https://courses.cognitiveclass.ai/courses/course-v1:Cognitiveclass+PY0101EN+v2/course/)
2. [Data Analysis with Python](https://courses.cognitiveclass.ai/courses/course-v1:CognitiveClass+DA0101EN+2017/course/)
3. [Data Visualization with Python](https://courses.cognitiveclass.ai/courses/course-v1:CognitiveClass+DV0101EN+v1/course/)
4. [Understanding Pattern Recognition in Machine Learning](https://www.section.io/engineering-education/understanding-pattern-recognition-in-machine-learning/)
