# [Python] E-Commerce Customer Churn Prediction & Analysis

---

## 📑 Table of Contents
1. [📌 Background & Overview](#-background--overview)
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)
3. [🧹 Data Preprocessing & Cleaning](#-data-preprocessing--cleaning)
4. [📊 Exploratory Data Analysis (EDA) & Insights](#-exploratory-data-analysis-eda--insights)
5. [🤖 Supervised Learning: Churn Prediction](#-supervised-learning-churn-prediction)
6. [🧩 Unsupervised Learning: Churner Segmentation](#-unsupervised-learning-churner-segmentation)
7. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)
---

## 📌 Background & Overview

This project uses Machine Learning to help the business keep customers before they decide to leave. The goal is to analyze 5,630 customers, predict who is likely to leave, and group the lost customers to create personalized offers to win them back.

### 📖 Objective:
This project applies Supervised and Unsupervised Machine Learning techniques to an E-commerce dataset to solve three core challenges:
* ✔️ **Exploratory Data Analysis (EDA):** Uncover the behavioral patterns and demographic traits of users who have already churned.
* ✔️ **Supervised Learning (Prediction):** Build a classification model to predict the `Churn` flag for current active users.
* ✔️ **Unsupervised Learning (Segmentation):** Apply clustering algorithms (K-Means) exclusively on churned users (`Churn == 1`) to group them for tailored promotional strategies.

### 👤 Who is this project for?
* ✔️ **CRM & Marketing Teams:** To design targeted email/app campaigns based on specific user segments.
* ✔️ **Product Managers:** To identify friction points in the user journey (e.g., complaints, app usage).

---

## 📂 Dataset Description & Data Structure

### 📊 Data Structure
The dataset contains customer demographic, geographic, and behavioral metrics.

<details>
  
| Key Columns | Data Type | Description |
| :--- | :--- | :--- |
| `Churn` | INT (0/1) | Target Variable: 1 if the customer churned, 0 otherwise. |
| `Tenure` | INT | Number of months the customer has been with the platform. |
| `Complain` | INT (0/1) | Indicates if the user raised a complaint in the last month. |
| `SatisfactionScore` | INT | Customer's satisfaction rating. |
| `PreferedOrderCat` | STRING | The category the user purchases from the most. |
| `HourSpendOnApp` | FLOAT | Time spent on the mobile app/website. |
| `DaySinceLastOrder` | FLOAT | Recency of the last purchase. |

</details>

---

## 🧹 Data Preprocessing & Cleaning

Before diving into modeling, the raw dataset required cleaning .

* **Missing Values Handling:** Approximately 32.9% of rows contained missing values. 
    * Replaced with **Median**: `tenure`, `warehousetohome`, `orderamounthikefromlastyear`, `ordercount`, `daysincelastorder` (due to skewed distributions).
    * Replaced with **0**: `hourspendonapp`, `couponused`.
* **Standardization:** Consolidated redundant categorical values to prevent feature splitting (e.g., merged 'Mobile Phone' into 'Phone', 'CC' into 'Credit Card', and 'COD' into 'Cash on Delivery').
* **Encoding & Scaling:** Applied One-Hot Encoding (`pd.get_dummies`) for categorical variables and utilized `MinMaxScaler` / `StandardScaler` to normalize the numerical features for machine learning.

---

## 📊 Exploratory Data Analysis (EDA) & Insights

An initial baseline Random Forest model highlighted the most critical features driving churn. I isolated these top features to uncover the following behavioral patterns:

1. **Tenure (The 90-Day Cliff):** Most churn occurs in the first 0-3 months. If the platform can push a user past the 3-month , the churn rate stabilizes below 10%.
2. **Cashback Amount (Financial Incentive):** Churned users consistently received less cashback ($160.37 on average) compared to retained users ($180.64).
3. **Complaints:** The churn rate is drastically higher among users who have recently filed a complaint. 
4. **Days Since Last Order:** Churned users with recent complaints actually had **fewer** days since their last order, suggesting that a recent negative shopping experience directly triggers immediate churn.
5. **Distance to Warehouse:** Distributions between churned and retained users were nearly identical (16.8km vs 15.3km). This feature lacked predictive power and was systematically dropped to prevent model bias.

---

## 🤖 Supervised Learning: Churn Prediction

To find users who might leave, I tested four Machine Learning models (Train: 3941, Val: 844, Test: 845).

### 1. Model Selection
I checked the Balanced Accuracy score for four models:
* **Random Forest:** 0.854 *(Best)*
* **Gradient Boosting:** 0.755
* **Logistic Regression:** 0.705
* **Decision Tree:** 0.704

### 2. Hyperparameter Tuning (GridSearchCV)
I fine-tuned the winning Random Forest model using Grid Search. 
* **Best Parameters:** `bootstrap=False`, `max_depth=20`, `min_samples_leaf=1`, `min_samples_split=2`, `n_estimators=100`.

### 3. Final Model Performance
* **Validation Balanced Accuracy:** 87.9%
* **Test Balanced Accuracy:** **89.6%**

*Result:* The fine-tuned Random Forest model identifies nearly 90% of churning behaviors without heavily overfitting.strictly based on the user's individual `PreferedOrderCat` or `PreferredPaymentMode`).

---

## 🧩 Unsupervised Learning: Churner Segmentation

The goal here was to use K-Means clustering to group the 948 churned users (`Churn == 1`), so the business can send specific promotions to each group.

**Process & Findings:**
* **PCA & Elbow Method:** I used PCA and the Elbow Method to find the best number of groups. However, the chart was a straight line with no clear "elbow". 
* **Business Conclusion:** The lost customer data is highly **scattered**. Users leave the app for their own personal reasons, not as one big group. We cannot easily put them into specific categories.

---

## 🔎 Final Conclusion & Recommendations

📍 **Key Takeaways:**
* **New users leave fast:** The first 3 months are the most dangerous time.
* **Bad service = instant churn:** A bad recent order makes people leave right away.
* **Missing Data:** Because lost customers do not share a clear pattern, we are likely missing some important data about why they actually leave.

🚀 **Strategic Actions:**
* ✔️ **Better Welcome Offers:** The Marketing team must focus heavily on the first 90 days. Give strong "Welcome Discounts" to keep new buyers interested.
* ✔️ **Fix the Cashback:** Since low cashback makes people leave, the system should automatically give more cashback to high-risk users to make them stay.
* ✔️ **Use the AI Model Now:** Put the Random Forest model into the system. When the AI spots a user who might leave (e.g., new user + recent complaint), customer service should call or email them immediately to fix the problem.
* ✔️ **Collect More Data:** Since we couldn't group the lost users easily, we need to collect new data like customer service chat logs or delivery delays. This will help us fully understand the real reasons they leave.
