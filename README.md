# Paralyzed Veterans of America (PVA)

![alt text](https://img1.wsimg.com/isteam/stock/14483/:/cr=t:0%25,l:0%25,w:100%25,h:100%25/rs=w:1280)

PVA is a national veteran’s organization. The organization has around 13 million donors in their database and is one of the largest direct mail fundraisers in the United States.


The data set had around 95K observations and 480 attributes. There were two target variables, namely TARGET_B and TARGET−D. Here, TARGET_B records the response of the donors (1 corresponds to responded, 0 corresponds to not responded) and TARGET_D records the 'donation amount' if the donor has donated.

---

## Goals

* Develop a data mining model to improve the cost-effectiveness of their direct marketing campaign. 
* Perform data cleaning and conduct Exploratory data analysis.
* Performing feature engineering and data reduction tasks along with the creation of some derived variables.
* Using Lasso and Ridge regression to model TARGET_B and compare both.
* Selecting the best model based on profitability.
* Using Random Forest, GBM, and OLS to model TARGET_D and compare them.
* Using the best model to predict the donation amount for future fundraising data.

---

I started by reducing the number of variables by manually going through each given in the input data set. Variables that were not related to the analysis were removed based on their description and our general understanding. The status of each variable after this first round was updated. With manual data exploration, I was able to reduce the variable count to 350 on which I performed further analysis.

After this, univariate analysis was conducted with the remaining set of variables. Variables with 0 or close to zero variance were omitted, along with that person's correlation test was conducted and only moderate to strong correlations with the target variable were kept for further analysis.

This followed by missing value treatment and converting variables such as  'LASTDATE' , 'ADATE', 'MAXADATE' to datetime format.

Some new variables were derived such as 'last gift to max gift ratio' and 'ratio of max to min gift amount'.

For the next part, a module called 'feature_selector' was used. Here, features with excessive missing values(>60%) were removed. Also, features with zero importance were removed. Only the features with cumulative feature importance of 0.9 were kept for further analysis. There were a total of 197 features required for the cumulative importance of 0.90 ( after one-hot encoding). 

Then the data set was split into test-train with a ratio of 70% training set and 30% testing set. Careful observation showed that the data is disproportionately distributed with large number of 0 values as compared to 1 values in TARGET_B. Thus, Oversampling was performed. Using the impalanced-learn module SMOTE( Synthetic minority oversampling technique) was performed. Later, the Random forest classifier model along with the linear model was implemented and trained using the training dataset.  Each method gave us a list of important variables on which we performed a union – 38 variables were thus kept from the 197 variables. Further, PCA(Principal Component Analysis) was implemented on those 38 features. In total 15 components were chosen that explained around 80% of the total variance.

I Used logistic regression to obtain the best value of lambda that could be used for the ridge and lasso models. The following are the results obtained for min and 1se values of lambda for 20% and 50% minority oversampling on the test dataset.



![alt text](https://img1.wsimg.com/isteam/ip/6accb018-0248-4224-8307-a1bd01733c4f/Screenshot_143.png/:/cr=t:0%25,l:0%25,w:100%25,h:100%25/rs=w:1280)

Next, I compared different models based on profitability as the deciding factor and for this process, I used different classification threshold values of 0.2, 0.3, and 0.5. Here the cost of mailing a donor is $0.68 and the average expected donation amount is $13.00. So, if mail is sent to a donor and donor donates, then it results in a net profit of $12.32 ($13.00-$0.68) while if they don't donate, it's a loss of $0.68 ($0-$0.68). Following were the results

![alt text](https://img1.wsimg.com/isteam/ip/6accb018-0248-4224-8307-a1bd01733c4f/Screenshot_143-0001.png/:/cr=t:0%25,l:0%25,w:100%25,h:100%25/rs=w:1280)


So, with a threshold value of 0.2, Lasso(lambda - 1se)  is the most profitable model. After this, the lift curve and decile chart were plotted. 

The next task is to create a model to predict the donation amount, I decided to include the probability values from our best model, Lasso (with lambda 1se) as an input in the model. While this one input came from the TARGET_B model, I decided to also use the 37 variables obtained by performing variable importance analysis using Random Forest and GBM in the earlier task. I also decided to omit the non-donors (i.e. those individuals for whom TARGET_B=0) from the data used to build the donation amount prediction model. However, I did not omit any large donation amounts from the model training data as I wanted to allow the model to be trained on all the available data as the percentage of TARGET_D=1 response individuals was already low. I created models using GBM, RF, and OLS. To understand the performance of each model, I decided to compute the R.M.S.E for each model as given in the table below:

![alt text](https://img1.wsimg.com/isteam/ip/6accb018-0248-4224-8307-a1bd01733c4f/Screenshot_143-0002.png/:/cr=t:0%25,l:0%25,w:100%25,h:100%25/rs=w:1280)


Random Forest model gives the best performance with the lowest R.M.S.E for both the training and test data.   

To obtain the expected donation amount, I multiplied the prediction from the RF models with the probability values from our lasso(lambda 1se) model in the previous task. As the cost of mailing is $0.68, we will target only those individuals whose expected donation amount as greater than $0.68. 

For predicting the response on the 'FutureFundRaising' dataset I computed the probability of each candidate being a donor and classified them as 0: Non-donor or 1: Donor, using the same threshold of 0.2. Using this computed probability as another input variable, I then used the Random Forest model and computed the donation amounts.

---
## Key outcomes

* The data set had around 95K observations and 480 attributes.
* Conducted exploratory data analysis (EDA) and answered critical business questions.
* Defined and computed various derived variables that can enhance model performance.
* Performed feature engineering and treated missing values.
* Developed a GBM model which resulted in top 198 features having cumulative feature importance of 0.9 which were considered for further analysis.
* Performed Synthetic Minority Over-sampling Technique (SMOTE) to balance the disproportionate dataset.
* Developed a random forest model that resulted in a train accuracy of 88.73% and a test accuracy of 85.06%
* Developed an OLS model for evaluating feature importance.
* Set of top 38 most important variable that are mutually inclusive to both Random forest and OLS model were selected
* Conducted principal component analysis(PCA) using these 38 variables which resulted in 15 components explaining about 80% of the total variance.
* Conducted hyperparameter tuning for the random forest model which resulted in the best performance of 95.01% accuracy
* Lasso and Ridge regularized linear models was also implemented.
* For different classification thresholds models were compared based on profitability.
* Most profitable model was Lasso(lambda - 1se) for a threshold of 0.2 giving a profit of $152,841.9
* Regression model to predict the donation amount was developed using GBM, RF, and OLS
* The best model turns out to be RF with a Train RMSE od 7.44 and a Test RMSE of 7.61 
