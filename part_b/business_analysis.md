Part B: Business Case Analysis:

B1. Problem Formulation
(a) Formulate this as a machine learning problem. State clearly: what is the target variable, what are the candidate input features, and what type of ML problem is this? Justify your choice of problem type.

The main objective here is to understand how different promotions affect sales across stores. So naturally, the target variable is the number of items sold.
The input features would include store-related factors like size and location, the type of promotion being used, competition in that area, and time-based factors like month, weekends, or festivals.
This is clearly a regression problem because we are trying to predict a number. Instead of directly selecting a promotion, the model estimates expected sales for each promotion, and then the best option can be chosen based on that.


(b) The company currently measures performance using total sales revenue. Explain why using items sold (sales volume) is a more reliable target variable for this problem. What broader principle does this illustrate about target variable selection in real-world ML projects?

Using revenue might seem logical at first, but it can actually be misleading. For example, if we give a big discount, we might sell more items but earn less revenue per item. On the other hand, selling fewer high-priced items could increase revenue but doesn’t really show whether the promotion worked.
Since the goal is to measure how promotions drive customer buying behavior, items sold gives a much clearer picture.

(c)A junior analyst suggests running one single global model across all 50 stores. Propose and justify an alternative modelling strategy that accounts for the fact that stores in different locations respond very differently to the same promotion.

Using a single model for all 50 stores assumes that customer behavior is the same everywhere, which is not realistic.
For eg, urban stores might respond better to loyalty programs, while rural stores may react more to discounts.
A better approach would be to segment the stores. 
We could,
Build separate models for urban, semi-urban, and rural stores, or
Use a model that captures interactions between store type and promotion
This way we don’t lose important differences by averaging everything into one model.

B2. Data and EDA Strategy

(a) The raw data arrives in four separate tables: transactions, store attributes, promotion details, and a calendar (with weekend and festival flags). Describe how you would join these tables. What is the grain of the final modelling dataset (one row = what?), and what aggregations would you perform before modelling?

The data comes from multiple tables, so we need to combine them properly:
Use store_id to join store-related information
Use transaction_date to bring in promotions and calendar data
After joining, the dataset should be structured as:
One row per store per month
Before modelling, I would aggregate:
Total items sold
Number of transactions
Average basket size
Promotion applied
Weekend and festival indicators
This makes sense because decisions are made monthly, not at the transaction level.


(b)Describe the EDA you would perform before building a model. Specify at least four analyses or charts, what you would look for in each, and how the findings would influence your feature engineering or modelling decisions.

Before building any model, I would first try to understand the data:
Sales by Promotion Type
Helps identify which promotions are generally performing better
Sales by Location Type
Shows whether urban and rural stores behave differently
Monthly Trends
Helps identify seasonality or festive spikes
Competition vs Sales
Gives an idea of how external factors affect performance
These insights are useful because they guide feature selection and help decide whether segmentation is needed.

(c)You notice that 80% of transactions in the dataset occurred without any promotion. Describe how this imbalance could affect your model and what steps you would take to address it.

If 80% of transactions happen without promotions, the model might assume that promotions don’t really matter.
This can lead to biased predictions where the model underestimates the impact of promotions.
To handle this, I would:
Make sure promotion cases are properly represented
Add a clear feature indicating whether a promotion is applied
Evaluate the model separately for promotion and non-promotion scenarios


B3. Model Evaluation and Deployment

(a)You have monthly store-level data spanning three years across 50 stores. Describe how you would set up the train-test split. Why is a random split inappropriate here? Which evaluation metrics would you use, and how would you interpret each in the context of this business problem?

Since the data is time-based, I would split it chronologically instead of randomly:
Train on earlier data
Test on the most recent months
A random split would mix past and future data, which leads to data leakage and unrealistic results.
For evaluation:
MAE gives the average error, which is easy to understand
RMSE highlights larger errors, which are more critical from a business perspective
So together, they give a balanced view of performance.

(b)After training, the model recommends the Loyalty Points Bonus for Store 12 in December and the Flat Discount for Store 12 in March. Using the concept of feature importance, explain how you would investigate and communicate to the marketing team why the model makes different recommendations for the same store in different months.

If the model recommends different promotions for the same store in different months, it actually means the model is capturing context properly.
To understand this:
I would check feature importance
Look at how factors like month, festivals, and past trends are influencing predictions
For example:
December might perform better with loyalty rewards due to festive demand
March might need discounts to boost sales
When explaining this to the marketing team, 
I would keep it simple:
The recommendation changes because customer behavior changes across months.


(c) The trained model needs to generate recommendations at the start of every month for all 50 stores without being retrained each time. Describe the end-to-end deployment process: how you would save the model, how new monthly data would be prepared and fed in, and what monitoring you would put in place to detect when the model's performance has degraded and retraining is needed.

Every month:
New data is collected
Features are prepared in the same format
The model is used to predict sales for each promotion
The best option is selected
For monitoring:
Compare predicted vs actual sales
Track error metrics over time
Watch for any changes in patterns
If the performance starts dropping, the model should be retrained using more recent data.