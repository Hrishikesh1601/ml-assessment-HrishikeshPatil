Part B: Business Case Analysis:

B1. Problem Formulation
(a)Formulate this as a machine learning problem. State clearly: what is the target variable, what are the candidate input features, and what type of ML problem is this? Justify your choice of problem type.

The goal of the business is to understand how different promotions impact sales across stores and to choose the best promotion for each store every month.
The target variable in this case is the number of items sold, as it directly reflects customer response to promotions.

The input features would include:
1.Store-related features: store size, location type (urban/semi-urban/rural)
2.Promotion-related features: type of promotion being applied
3.External factors: competition density
4.Time-based features: month, weekends, festivals

This is a regression problem because we are predicting a continuous numerical value. Instead of directly predicting the best promotion, the model estimates expected sales for each promotion, and the business can choose the promotion that gives the highest predicted sales.


(b)The company currently measures performance using total sales revenue. Explain why using items sold is a more reliable target variable for this problem. What broader principle does this illustrate about target variable selection in real-world ML projects?

Using total revenue as a target can sometimes give a misleading picture of promotion effectiveness. For eg, a heavy discount might increase the number of items sold but reduce revenue per item. On the other hand, selling fewer expensive items might increase revenue but does not necessarily mean the promotion was effective in driving customer engagement.
Since the objective is to measure how promotions influence customer buying behavior, items sold is a more reliable metric.
The target variable should be closely aligned with the actual business objective and should not be distorted by external factors like pricing.


(c)A junior analyst suggests running one single global model across all 50 stores. Propose and justify an alternative modelling strategy that accounts for the fact that stores in different locations respond very differently to the same promotion.

Using a single global model for all 50 stores assumes that customer behavior is similar across all locations, which is not realistic.
A better approach would be to account for store-level differences. This can be done in a few ways:

1.Segment stores into groups (urban, semi-urban, rural) and build separate models
2.Include interaction features between store type and promotion type
3.Use a hierarchical (multi-level) model that captures both overall trends and store-specific variations

This ensures that the model does not ignore important differences in how customers respond to promotions in different locations.




B2. Data and EDA Strategy

(a)The raw data arrives in four separate tables: transactions, store attributes, promotion details, and a calendar. Describe how you would join these tables. What is the grain of the final modelling dataset, and what aggregations would you perform before modelling?

The data comes from multiple tables, so they need to be combined carefully:

1.Use store_id to join the transactions table with store attributes
2.Use transaction_date to join with the calendar table
3.Join promotion details based on promotion type and date

The final dataset should have a grain of one row per store per month, since decisions about promotions are made monthly at the store level.

Before modelling, I would aggregate the data to this level by calculating:

1.Total items sold
2.Total number of transactions
3.Average basket size
4.Promotion type applied
5.Indicators for weekends and festivals

This aggregation helps align the data with the business decision-making process.



(b)Describe the EDA you would perform before building a model. Specify at least four analyses or charts, what you would look for in each, and how the findings would influence your feature engineering or modelling decisions.

Before building a model, I would perform exploratory data analysis to understand patterns and relationships in the data:

Sales by Promotion Type (bar chart)
To see which promotions generally perform better and whether some promotions consistently outperform others.
Sales by Location Type (box plot or grouped bar chart)
To understand how customer behavior differs across urban, semi-urban, and rural stores.
Monthly Sales Trends (line chart)
To identify seasonality, such as spikes during festive months or end-of-season sales.
Competition Density vs Sales (scatter plot)
To check whether higher competition negatively impacts sales.
Correlation Heatmap
To identify relationships between numerical features and detect multicollinearity.
Feature Distributions (histograms)
To detect skewness and outliers, which may require transformation or scaling.

These insights help guide feature engineering, model selection, and whether segmentation is needed.


(c)You notice that 80% of transactions in the dataset occurred without any promotion. Describe how this imbalance could affect your model and what steps you would take to address it.

If 80% of transactions occur without promotions, the dataset is imbalanced. This can cause the model to underlearn the effect of promotions and bias predictions toward non-promotion scenarios.

To address this, I would:

1.Ensure promotion-related data is properly represented during training
2.Use sample weighting or resampling techniques to give more importance to promotion cases
3.Evaluate model performance separately for promotion and non-promotion scenarios

This ensures the model correctly captures the impact of promotions instead of ignoring them.




B3. Model Evaluation and Deployment

(a)You have monthly store-level data spanning three years across 50 stores. Describe how you would set up the train-test split. Why is a random split inappropriate here? Which evaluation metrics would you use, and how would you interpret each in the context of this business problem?

Since the data is time-based, I would split it chronologically:

Use earlier data for training
Use the most recent months as the test set

A random split is inappropriate because it mixes past and future data, leading to data leakage and overly optimistic performance estimates.

For evaluation, I would use:

MAE (Mean Absolute Error): easy to interpret, shows average prediction error
RMSE (Root Mean Squared Error): penalises large errors more, which is important in business scenarios

Together, these metrics provide a balanced understanding of model performance.


(b)After training, the model recommends the Loyalty Points Bonus for Store 12 in December and the Flat Discount for Store 12 in March. Using the concept of feature importance, explain how you would investigate and communicate to the marketing team why the model makes different recommendations for the same store in different months.

If the model recommends different promotions for the same store in different months, it indicates that the model is capturing seasonal and contextual patterns.

To understand this, I would:

Analyze feature importance to identify which features influence predictions the most
Use techniques like SHAP values to explain individual predictions

For eg:

In December, factors like festivals and higher demand might make loyalty rewards more effective
In March, lower demand might make discounts more effective
The model adapts recommendations based on changing customer behavior across time, rather than applying a one-size-fits-all strategy.


(c) The trained model needs to generate recommendations at the start of every month for all 50 stores without being retrained each time. Describe the end-to-end deployment process: how you would save the model, how new monthly data would be prepared and fed in, and what monitoring you would put in place to detect when the model's performance has degraded and retraining is needed.

For deployment, the process would work as follows:

Model Saving
After training, the model can be saved using tools like joblib or pickle.
Monthly Prediction Pipeline
Each month:
New data is collected
Features are engineered in the same way as training
The model predicts expected sales for each promotion
The best promotion is selected for each store
Automation
This process can be automated using a scheduled pipeline (e.g., monthly batch job).
Monitoring
To ensure the model continues to perform well:
Track prediction errors (MAE, RMSE) over time
Compare predicted vs actual sales
Monitor changes in data distribution (data drift)
Retraining
If performance degrades significantly, retrain the model using recent data to keep it updated.