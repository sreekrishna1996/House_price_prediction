## House price prediction

This is my personal project. I wanted the apply some of the classical machine learning algorithms I learnt on a dataset which seemed like realtime. Hence, I chose a dataset from the one of the kaggle competitions (https://www.kaggle.com/competitions/house-prices-advanced-regression-techniques/overview). 

The task here is to build a regression model which can accurately predict the sales price of a house based on some features like plot_area, house_location, year_built etc. Given below are the details on how I approached and built the regression model:

1. After checking the characteristics of the dataset, I started with the preprocessing. I dropped the features which I felt were unnecessary as per my domain knowledge. I sepererated continuos, nominal categorical and ordinal categorical features. I filled the missing values with Mean, median or mode imputation. I encoded the nominal categorical features with target encoding and ordinal categorical features with label encoding. At last, the features were scaled using the standard scaler as they are less susceptible to outliers.
2. Before removing outliers, I first built a baseline model using XGboost(GBDT algorithm). I could get a MAPE score of 10.5%. I was not satisfied with the score. 
3. I started troubleshooting my approach so that I could decrease my MAPE score. I started with dimentionality reduction to check the distribution of the dataset in 2 dimension. I used t-SNE algorithm to get the embeddings. From the scatterplot of these embeddings, I found that around 4-5 clusters existed in my dataset. 
4. Before I could start clustering I used few multivariate outlier detection techniques like Isolation forest and Local outlier factor to detect the outliers. I found that LOF was accurately detecting the outliers.
5. I was not able to get proper clusters with Kmeans or hierarchical algorithms. However, DBSCAN was very good in capturing 5 clusters. It also detected the outliers, which means I no longer need LOF's labels.
6. I further confirmed the existence of clusters by grouping each cluster label and performing mean aggregation on the top 4 features (using feature importances from the baseline model) and the target variable. I could see that wherever the saleprice mean is higher, other feature means are also reportedly higher as compared to other cluster means. In short words, I could observe High positive correlation between sale price and top features means for each cluster(0.85 avg pearson correlation coeff).
7.  After clustering, I first built a baseline model without outliers. My mape decreased from 10.5 to 10.4%. It was not that significant, but still an improved outcome. 
8.  Now, I started with building seperate GBDT models for each cluster. My combined MAPE score of 5 models decreased significantly to 9%. I also found 3 of the clusters to be almost similar. Hence, I merged those 3 clusters and built just 3 models instead of 5. My combined MAPE score stayed almost the same. 
9. As we know DBSCAN is a non deployable algorithm. I had to build a multiclass classification model using random forest. Target variable was the DBSCAN labels. I could achieve an accuracy of 0.99.
10. Hence we can use trained classification model to first classify the datapoint into one of the cluster and then predict the house price. We can also use models like LOF to first detect outliers. If the datapoint is an outlier then we can warn the user about this first and still go ahead with the prediction.

Thank you,

Krishna