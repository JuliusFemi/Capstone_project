# Capstone Project: Optimizing E-Commerce Strategy with Customer Segmentation and Predictive Analytics

## Project Overview
E-commerce businesses face challenges in improving customer retention, boosting sales, and personalizing marketing strategies. Without data-driven insights, decisions are often reactive and inefficient.  

This project uses data from Google BigQuery’s `thelook_ecommerce` dataset to:  
1. Segment customers based on purchase behavior and demographics.  
2. Predict key customer behaviors such as repeat buying, high-value potential, and churn risk.  
3. Provide actionable business recommendations to improve marketing and product strategies.  

---

## Dataset Overview
The following tables were merged into a single analytical dataset (`merged_df`):  
- **users** – customer demographics  
- **orders** – transaction history and status  
- **order_items** – item-level purchase details  
- **products** – product information  
- **inventory_items** – inventory and cost metadata  
- **distribution_centers** – logistics information  

After cleaning and merging, we created a **customer-level dataset** (`customer_df`) containing key engineered features.

---

## Feature Engineering
Derived features included:  
- **Recency**: Days since the last order (`recency_days`)  
- **Frequency**: Number of orders per user (`num_orders`)  
- **Monetary Value**: Total customer spend (`total_spent`)  
- **Average Order Value (AOV)**: `avg_order_value`  
- **Return Rate**: Ratio of returned orders to total orders (`return_rate`)  
- **Preferred Category and Brand**: Most common purchase behavior  

These features were necessary to enable both **segmentation (unsupervised learning)** and **prediction (supervised learning)**.

---

## Exploratory Data Analysis (EDA)

### Customer Demographics
- **Age distribution**: Majority of customers are between 20–29 years old.  
- **Gender distribution**: Visualized with bar and pie charts.  

### Purchase Behavior
- **Order frequency per user**: Most customers make only 1 order, highlighting the challenge of retention.  
- **Total spend distribution**: A small fraction of customers contribute disproportionately to revenue (top 5%).  
- **Repeat vs One-time buyers**: Approx 37% are repeat buyers, while 63% are one-time buyers.  
- **New vs Returning customers (monthly)**: Returning customers contribute consistently to sales, but new customer acquisition is also strong.  

### Product Insights
- **Top products by gender**: Identified distinct preferences among male and female buyers.  
- **Return rates by product category**: Certain categories such as *Jumpsuits & Rompers* and *Intimates* have high return rates.  
- **Product velocity**: Average of approx 30 days between inventory creation and sale, with variation across categories.  
- **Delivery performance**: Majority of orders are delivered within 48 hours, supporting marketing promises of 1–2 day delivery.  

---

## Customer Segmentation (Unsupervised Learning)

### Method
- Applied **KMeans clustering** on standardized behavioral features.  
- Optimal number of clusters selected using **Elbow Method** and **Silhouette Score**.  
- Final choice: **k = 4 clusters** for interpretability and business relevance.  

### Cluster Profiles
| Cluster | Customer Count | Avg Orders | Avg Spend | Avg Recency (days) | Avg Return Rate | Top Category | Top Brand |
|---------|----------------|------------|-----------|---------------------|-----------------|--------------|-----------|
| 2       | 15,788         | 2.69       | 340.98    | 313                 | 0.11            | Accessories  | Allegra K |
| 0       | 5,653          | 1.23       | 109.98    | 480                 | 1.45            | Intimates    | Allegra K |
| 3       | 16,879         | 1.16       | 83.66     | 1,273               | 0.03            | Intimates    | Allegra K |
| 1       | 41,629         | 1.34       | 80.30     | 273                 | 0.02            | Intimates    | Allegra K |

### Segment Interpretation
- **Cluster 2 – High-Value Buyers**: Most profitable, moderate recency, worth prioritizing with loyalty programs.  
- **Cluster 0 – High Returners**: Low spend, extremely high return rates, potentially unprofitable.  
- **Cluster 3 – Lost Customers**: Very long recency, low spend, largely dormant.  
- **Cluster 1 – Low-Spend One-Timers**: Largest group, low spend and order frequency, growth potential if converted.  

### Visualizations
- **Heatmap of cluster profiles** (normalized features).  
- **Radar chart** to compare behavioral “shapes” of each cluster.  

---

## Predictive Modeling (Supervised Learning)

Three predictive tasks were defined:  

### 1. Repeat Buyer Prediction
- **Target:** 1 if >1 order, else 0.  
- **Performance:** Accuracy ≈ 100% on test set (23,985 customers).  
- **Top Features:** `total_spent`, `avg_order_value`.  
- **Insight:** Spending patterns are the strongest signal for predicting repeat buyers.  

### 2. High-Value Customer Prediction
- **Target:** 1 if in top 20% of spenders, else 0.  
- **Performance:** Accuracy ≈ 100% on test set.  
- **Top Features:** `avg_order_value` (67%), `num_orders` (30%).  
- **Insight:** High-value customers are characterized by high AOV and order frequency.  

### 3. Churn Risk Prediction
- **Target:** 1 if no purchases in last 90 days, else 0.  
- **Performance:** Accuracy = 70%.  
  - Recall for churned customers = 86% (strong).  
  - Recall for active customers = 16% (weak).  
- **Top Features:** `total_spent`, `avg_order_value`, `age`.  
- **Insight:** Model effectively flags churned customers but struggles with active ones due to class imbalance.  

---

## Key Business Insights

1. **Retention Challenge**: Majority of customers purchase only once; improving repeat purchases is critical.  
2. **High-Value Buyers**: A small segment drives most revenue; must be prioritized with loyalty/VIP programs.  
3. **Returns Issue**: Certain categories (e.g., Jumpsuits & Rompers, Intimates) have high return rates, affecting profitability.  
4. **Churn Risk**: Predictive model can identify churned customers well, though further improvement is needed to identify active customers.  
5. **Delivery Strength**: Fast delivery times (within 48 hours) are a competitive advantage and can be leveraged in marketing.  

---

## Recommendations

- **Cluster 2 (High-Value Buyers):** Focus on retention, exclusive offers, and upselling.  
- **Cluster 1 (Low-Spend One-Timers):** Targeted promotions and personalized recommendations to encourage repeat purchases.  
- **Cluster 0 (High Returners):** Review return policies, consider stricter rules for high-return customers.  
- **Cluster 3 (Lost Customers):** Low-priority, but selective win-back campaigns may revive a fraction.  

For prediction models:  
- **Repeat Buyer and High-Value models** are highly accurate and can be deployed for targeting campaigns.  
- **Churn model** requires improvement (SMOTE, XGBoost, additional features), but already provides value in flagging at-risk customers.  

---

## Conclusion
This project demonstrates how customer analytics can guide e-commerce strategy by combining **descriptive segmentation** with **predictive modeling**. By understanding who the customers are and anticipating their future behavior, businesses can allocate resources more effectively, personalize campaigns, and ultimately increase retention and profitability.  
