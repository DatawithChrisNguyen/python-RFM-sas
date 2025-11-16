# python-RFM-sas
# 📊 Your Project Name [ Business question + Domain + Tools ]  

  
Author: Chris Nguyen/ Dinh Khoi Nguyen
Finished Date: YYYY-MM-DD  
Uploaded Date:
Tools Used:  Python  

---

## 📑 Table of Contents  
1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)
4. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview  

### Background
SuperStore is a global retail company headquartered in the UK, serving thousands of customers across multiple international markets. Over recent years, the company has transitioned from small-scale operations to enterprise-level retail, generating $8.3 million in revenue from 4,330 customers.
- Industry: Global retail
- Customer Base: Primarily UK (90%), with growing international segments
- Revenue Model: Direct-to-consumer sales through retail channels
- Growth Stage: Rapid expansion requiring advanced analytics for customer management
- Seasonal Opportunity: Christmas and New Year represent the most critical business period, with peak spending and relationship-building potential
The company’s Marketing team faces challenges in segmenting and targeting customers effectively. Manual Excel-based analysis is no longer sufficient, creating a need for automated, scalable solutions to support personalized campaigns and maximize holiday season ROI.

### Objective

📖 What is this project about? What Business Question will it solve?

This project applies Python and customer analytics to segment SuperStore’s customer base using the RFM (Recency, Frequency, Monetary) model. It transforms raw transaction data into actionable insights for holiday campaign planning and long-term retention strategies.

✔️ Segment customers into meaningful groups (Champions, Promising, New, At-Risk)

✔️ Identify spending patterns and engagement behaviors across UK and international markets

✔️ Highlight high-value customers and at-risk segments for targeted campaigns

✔️ Provide evidence-based recommendations to optimize holiday marketing and maximize ROI

Main Business Question:

➡️ How can SuperStore leverage automated customer segmentation to design personalized holiday campaigns that increase sales, strengthen loyalty, and reduce churn across global markets?

👤 Who is this project for?

✔️ Data analysts & business analysts – to build segmentation models and interpret customer behavior

✔️ Marketing teams – to design personalized campaigns based on customer value and engagement

✔️ CRM managers – to improve retention strategies and lifecycle management

✔️ Business leaders & stakeholders – to make data-driven decisions that maximize holiday season revenue and long-term growth


---

## 📂 Dataset Description & Data Structure  
<details><summary>Click here to know more</summary>

### 📌 Data Source  
- Source:([Dataset/ecommerce retail.xlsx](https://github.com/DatawithChrisNguyen/python-RFM-sas/blob/main/ecommerce--retail.xlsx))  
- Size: 541909 rows x 8 columns
- Format: xlsx

### 📊 Data Structure & Relationships  

#### 1️⃣ Tables Used:  
There is 1 table used for building the RFM model

#### 2️⃣ Table Schema & Data Snapshot  

Table: Ecommerce Retail Table  

---

### 📊 Dataset Overview

| Column Name   | Data Type     | Description                                                                 |
|---------------|---------------|------------------------------------------------------------------------------|
| `InvoiceNo`   | Object  | Unique 6-digit transaction ID. If it starts with 'C', it indicates a cancellation. |
| `StockCode`   | Object  | Unique 5-digit product/item code.                                           |
| `Description` | Object  | Name of the product/item.                                                  |
| `Quantity`    | Integer         | Number of units purchased per transaction.                                 |
| `InvoiceDate` | Datetime        | Date and time when the transaction occurred.                               |
| `UnitPrice`   | Float           | Price per unit in GBP (£).                                                 |
| `CustomerID`  | Float           | Unique 5-digit customer identifier.                                        |
| `Country`     | Object  | Country where the customer resides.                                        |

---




</details>
</p>

## ⚒️ Main Process

### 1️⃣ Data Cleaning & Preprocessing
<details><summary>Click here to know more</summary>
    
_Checking the data_
```
def data_overview(data, name=""):
    print("\n---", name, "---")
    print("Shape:", data.shape)
    print("Dtypes:\n", data.dtypes)
    print("\nMissing values:\n", data.isnull().sum())
    print("\nUnique values per column:\n", data.nunique())

data_overview(records, "Overview Data")
```
![](Images/datatype.png)

Here, I can identify the data type and catch the missing values for further handling.

_Getting the unique value of each attribute_
```
for col in records.columns:
    print(f"Column: {col}")
    print(f"→ Unique values count: {records[col].nunique()}")
    print(f"→ Unique values: {records[col].unique()}")
    print('-' * 50)
```
![](Images/uniquevalue.png)

- Found that 'Phone' and 'Mobile' in PreferredLoginDevice referred to the same device type, so I standardized them to 'Mobile Phone' to ensure consistency and avoid splitting identical categories.
- Unified 'Mobile' in PreferedOrderCat to 'Mobile Phone' for semantic clarity and alignment across features.
- Replaced shorthand values like 'COD' and 'CC' in PreferredPaymentMode with 'Cash on Delivery' and 'Credit Card' to improve interpretability and make outputs more readable for business stakeholders.
- These standardizations prevent redundant encoding, reduce noise in modeling, and improve the quality of clustering and feature importance analysis.

_Unifying Values_
```
records['PreferredLoginDevice'] = records['PreferredLoginDevice'].replace({
    'Phone': 'Mobile Phone'
})
records['PreferedOrderCat'] = records['PreferedOrderCat'].replace({
    'Mobile': 'Mobile Phone'
})

# Unifying values in PreferredPaymentMode
records['PreferredPaymentMode'] = records['PreferredPaymentMode'].replace({
    'COD': 'Cash on Delivery',
    'CC': 'Credit Card'
})
```
![](Images/edited_uniquevalue.png)

_Checking And Handling Missing Data_
```print("Total missing values:", records.isnull().sum().sum())```

Fortunately, there is no missing value here, so I can move to the EDA Analysis
</details>
</p>

✅ Key Actions Taken:
- Standardized category labels:
  
Unified similar entries to avoid duplication and misinterpretation. For example:

    - “Mobile Phone” and “Mobile” were treated as the same device type
    - “Cash on Delivery” and “COD” were merged into one payment method
    - “Credit Card” and “CC” were also unified
- Validated data completeness:
  
The dataset had no missing values, so the total number of records remained unchanged after cleaning.
- Prepared categorical fields for analysis:

Ensured that fields like PreferredLoginDevice, PreferredOrderCat, PreferredPaymentMode, Gender, and Martial Status were consistent and ready for grouping, filtering, and modeling.


### 2️⃣ Exploratory Data Analysis (EDA) 
_Univariate Analysis_

**Categorical Variables**

![](Images/Cat_Hist.png)

- PreferredLoginDevice: Majority of users prefer Mobile, indicating a mobile-first user base.
→ Optimize mobile UX and prioritize mobile engagement strategies.
- PreferredPaymentMode: Debit Card and Credit Card are the most common; UPI and Net Banking are the least used.
→ Focus on secure card-based payment flows; consider incentives for digital adoption.
- PreferedOrderCat: Laptop & Accessories and Mobile dominate, while Grocery and Others are less frequent.
→ Tech categories are popular — tailor promotions and product bundles accordingly.
- MaritalStatus: Single users are the largest group, followed by Divorced and Married.
→ Demographic targeting can be refined based on relationship status.
- Gender: Slight male majority overall.
→ Ensure balanced messaging and product offerings across genders

**Numerical Variables**

![](Images/Num_Hist.png)

- CityTier: Users from Tier 2 and 3 cities show slightly higher churn than Tier 1.
→ Tailor service quality and delivery expectations for lower-tier regions.
- NumberOfDeviceRegistered: Users with more registered devices tend to be more retained.
→ Multi-device usage signals engagement — consider cross-device personalization.
- NumberOfAddress: No strong correlation with churn, but multiple addresses may indicate mobility.
→ Monitor for delivery consistency and address-related service issues.
- OrderCount: Higher order volume is associated with lower churn.
→ Frequent buyers are loyal — reward them with exclusive offers or early access.
- CouponUsed: Users who use more coupons tend to stay longer.
→ Incentive-driven retention works — optimize coupon strategy by segment.
- CashbackAmount: Higher cashback correlates with retention.
→ Cashback is effective — refine targeting to maximize ROI.
- OrderAmountHikeFromLastYear: Spending growth signals loyalty.
→ Track and nurture high-growth customers with tailored upsell strategies.
- DaySinceLastOrder: Longer inactivity strongly predicts churn.
→ Use automated reactivation campaigns and personalized nudges.

_Bivariate Analysis_

**Categorical Variables VS Churn**

![](Images/CatevsChrun.png)

- Login Device: Users on computers show slightly higher churn than Mobile users.
→ Mobile users are more retained — consider improving the desktop experience.
- Payment Mode: Churn is relatively low across all modes, but Cash on Delivery shows slightly higher churn.
→ Encourage prepaid options to reduce churn risk.
- Order Category: Mobile orders show higher churn than other categories.
→ Investigate product satisfaction or delivery issues in the mobile category.
- Marital Status: Divorced and Married users show higher churn than Singles.
→ Explore life-stage-based retention strategies.
- Gender: Male users show slightly higher churn than females.
→ Consider gender-specific engagement tactics

**Numerical Variables VS Churn**

![](Images/NumvsChurn.png)

- Tenure: Longer tenure correlates with lower churn.
→ Loyalty programs may help retain long-tenure users.
- WarehouseToHome: Greater distance may slightly increase churn.
→ Optimize delivery logistics or set expectations for remote users.
- HourSpendOnApp: Higher app usage correlates with retention.
→ Boost app engagement to reduce churn risk.
- SatisfactionScore: Lower scores are strongly associated with churn.
→ Key predictor — prioritize service quality improvements.
- Complain: Users who have complained are more likely to churn.
→ Actively resolve complaints and monitor sentiment.
- OrderAmountHikeFromLastYear: Higher increases correlate with retention.
→ Track spending momentum as a loyalty signal.
- CouponUsed & CashbackAmount: Higher values are linked to retention.
→ Incentives work — optimize targeting and timing.
- DaySinceLastOrder: Longer gaps correlate with churn.
→ Use reactivation campaigns for dormant users.

### 3️⃣ Python Analysis 

## Task 1: Building the Prediction Model
```
le = LabelEncoder()
for col in records.columns:
    if records[col].dtype == "object":
        records[col] = le.fit_transform(records[col])

X = records.drop(["Churn","CustomerID"], axis=1)
y = records["Churn"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

rf = RandomForestClassifier(random_state=42)
params = {"n_estimators":[100,200],
          "max_depth":[5,10,None],
          "min_samples_split":[2,5]}

grid = GridSearchCV(rf, params, cv=3, scoring="accuracy", n_jobs=-1)
grid.fit(X_train, y_train)

best_model = grid.best_estimator_
y_pred = best_model.predict(X_test)

print("Best Params:", grid.best_params_)
print("Accuracy:", accuracy_score(y_test, y_pred))
print(classification_report(y_test, y_pred))
print("Confusion Matrix:\n", confusion_matrix(y_test, y_pred))
```
![](Images/ModelPer.png)

🎯 What the Model Does
- Predicts which customers are likely to churn based on their activity, satisfaction, and engagement patterns.
- Supports marketing and customer service teams in prioritizing retention efforts.
- Enables smarter allocation of resources by focusing on high-risk segments.
- 
📈 Performance Highlights
- Accuracy: The model correctly identified churn status for over 97% of customers.
- Precision: It was highly effective at avoiding false alarms — only 11 loyal customers were mistakenly flagged as churned.
- Recall: It successfully captured 91% of actual churned users, ensuring most at-risk customers are detected.
- Balance: The model maintained strong performance across both churned and non-churned groups, making it reliable for operational use.


## Task 2: Behaviour Segmentation
<details><summary>Click here to know more</summary>
_1.Finding the best number of clusters_

__Elbow Method__
```
# Filter for churned customers and drop irrelevant columns
churned = records[records["Churn"]==1].drop(["Churn","CustomerID"], axis=1)

# Scale data
scaler2 = StandardScaler()
churned_scaled = scaler2.fit_transform(churned)

# Elbow method
inertia = []
K_range = range(1, 11)

for k in K_range:
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(churned_scaled)
    inertia.append(kmeans.inertia_)

# Plot elbow curve
plt.figure(figsize=(8, 5))
plt.plot(K_range, inertia, marker='o')
plt.xlabel("Number of Clusters (k)")
plt.ylabel("Inertia (Within-Cluster Sum of Squares)")
plt.title("Elbow Method for Optimal k")
plt.grid(True)
plt.show()
```

![](Images/Elbow.png)


__Silhouette Score__

```
silhouette_scores = []

for k in range(2, 11):  # Silhouette score not defined for k=1
    kmeans = KMeans(n_clusters=k, random_state=42)
    labels = kmeans.fit_predict(churned_scaled)
    score = silhouette_score(churned_scaled, labels)
    silhouette_scores.append(score)

# Plot silhouette scores
plt.figure(figsize=(8, 5))
plt.plot(range(2, 11), silhouette_scores, marker='o', color='green')
plt.xlabel("Number of Clusters (k)")
plt.ylabel("Silhouette Score")
plt.title("Silhouette Score for Different k")
plt.grid(True)
plt.show()
```

![](Images/Silhouette.png)

Results:
- The Elbow Method favors k = 5 for capturing meaningful variation in customer behavior.
- Silhouette Score warns that clusters may not be sharply separated — segmentation may be useful, but not perfectly clean.
  
Recommendation: Proceed with 5 clusters, but enhance interpretability by:
- Profiling each cluster based on churn risk, satisfaction, and engagement
- Using churn labels to guide retention strategies

_2.Applying k=5_
```
churned = records[records["Churn"]==1].drop(["Churn","CustomerID"], axis=1)

# Re-apply LabelEncoder to categorical columns in 'churned' DataFrame
le = LabelEncoder()
for col in categorical_cols:
    if col in churned.columns and churned[col].dtype == 'object':
        churned[col] = le.fit_transform(churned[col])

scaler2 = StandardScaler()
churned_scaled = scaler2.fit_transform(churned)

kmeans = KMeans(n_clusters=5, random_state=42)
clusters = kmeans.fit_predict(churned_scaled)
churned["Cluster"] = clusters

# PCA visualization
pca = PCA(n_components=2)
reduced = pca.fit_transform(churned_scaled)
plt.scatter(reduced[:,0], reduced[:,1], c=clusters, cmap="viridis")
plt.title("Churned Users Clusters")
plt.show()

print("\n=== Cluster Summary ===")
print(churned.groupby("Cluster").mean())
```
</details>
</p>

![](Images/Seg_Result.png)

Insights:
🔹 Cluster 0 – Active but Dissatisfied

- Moderate tenure and high order volume
- Uses multiple devices and spends time on the app
- Satisfaction score is average
- Complaints are relatively frequent

Summary: These users are engaged but may have unresolved issues. They’re worth retaining through service improvements and loyalty perks.

🔹 Cluster 1 – Low Engagement, High Cashback

- Short tenure, low app usage, and minimal orders
- Very few coupons used, but high cashback received
- Lower satisfaction and fewer complaints

  Summary: These users are passive and incentive-driven. Despite receiving cashback, they didn’t stay. Consider exit surveys or reactivation offers.
  
🔹 Cluster 2 – Tech-Savvy but Under-Rewarded

- High device registration and tech-focused order preferences
- Moderate satisfaction, low coupon usage, and low cashback
- Longer delivery distance

  Summary: Digitally active users who may feel unrewarded. Target with personalized tech offers and better incentives.
  
🔹 Cluster 3 – Loyal but Lost

- Longest tenure and highest app usage
- High satisfaction and moderate order volume
- High cashback and coupon usage 

Summary: These users were loyal and satisfied but still churned. Investigate external factors like pricing or competitor influence. High potential for win-back campaigns.

🔹 Cluster 4 – Silent Churners

- Short tenure, low order volume, but recent activity
- High app usage and low satisfaction
- Very few coupons used, low cashback 

Summary: These users churned quietly despite recent engagement. Monitor post-order experience and automate follow-ups to prevent similar cases.


---

## 🔎 Final Conclusion & Recommendations  

👉🏻 Based on the insights and findings above, we would recommend the [stakeholder team] to consider the following:  

📌 Key Takeaways:  
✔️ Recommendation 1 - EDA Analysis:
1. Retention Strategy
- Launch loyalty programs for long-tenure and high-order users.
- Automate reactivation flows for dormant users based on inactivity thresholds.
2. Personalization & Targeting
- Use device, payment, and demographic preferences to personalize messaging and offers.
- Segment users by engagement level and tailor onboarding or upsell strategies.
3. Operational Improvements
- Enhance mobile and desktop experiences to reduce friction.
- Monitor delivery performance in Tier 2/3 cities and address service gaps.
4. Incentive Optimization
- Refine coupon and cashback targeting using behavioral segmentation.
- Track spending momentum to identify and nurture high-growth customers.

✔️ Recommendation 2  - Prediction Model:
1. Deploy the churn prediction model to flag at-risk customers early and guide retention efforts. With 97% accuracy and strong recall, it reliably identifies who’s likely to leave.
   
- Integrate into CRM to trigger personalized offers, loyalty rewards, or service outreach.
- Prioritize high-risk, high-value users for win-back campaigns.
- Use churn scores with behavioral clusters to tailor re-engagement strategies.
- Monitor and retrain regularly to keep the model sharp and aligned with evolving customer behavior.
2. This model turns churn from a reactive problem into a predictable, actionable opportunity.

✔️ Recommendation 3 - Behavior Segmentation:

The clustering analysis reveals five distinct churn profiles, each with unique engagement patterns and churn drivers. To turn these insights into action:
- Cluster 0 & 3: Loyal, high-value users who churned despite strong engagement.
→ Prioritize for win-back campaigns, loyalty rewards, and personalized outreach.
- Cluster 1 & 4: Low-activity churners with weak retention signals.
→ Use targeted reactivation offers, exit surveys, or simplified app experiences.
- Cluster 2: Tech-savvy but under-rewarded users.
→ Tailor incentives and onboarding to match digital preferences and spending potential.

By aligning retention strategies to these behavioral profiles, you shift from generic churn prevention to targeted lifecycle management — improving ROI and customer satisfaction.


## 🔎 Final Conclusion & Recommendations  

👉🏻 Based on the insights and findings above, we would recommend the [stakeholder team] to consider the following:  

📍 Key Takeaways:  
✔️ Recommendation 1  
✔️ Recommendation 2  
✔️ Recommendation 3

**_📌Remember to summarize the most core insights/ observations you extract from the entire projects. 
 Recap ONLY key actions/ recommendations. DO NOT copy paste everything above_**
