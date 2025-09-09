# Optimizing E-Commerce Strategy with Customer Segmentation and Predictive Analytics

## Project Overview
E-commerce businesses face challenges in improving customer retention, boosting sales, and personalizing marketing strategies. Without data-driven insights, decisions are often reactive and inefficient.  

This project applies advanced analytics on Google BigQuery’s **`thelook_ecommerce` public dataset** to:  
1. Segment customers based on purchase behavior and demographics.  
2. Predict key outcomes such as repeat buying, high-value potential, and churn risk.  
3. Provide actionable recommendations to guide marketing and product strategies.  

---

## Dataset Overview
The data was sourced from **Google BigQuery – `thelook_ecommerce` public dataset**, which simulates an e-commerce platform.  

The following tables were merged into a single analytical dataset:  
- **users** – customer demographics  
- **orders** – transaction history and status  
- **order_items** – item-level purchase details  
- **products** – product information  
- **inventory_items** – inventory and cost metadata  
- **distribution_centers** – logistics information  

After cleaning and merging, a **customer-level dataset** was created containing key behavioral and demographic features.

---

## Feature Engineering
Derived features included:  
- **Recency** (`recency_days`) – days since last purchase  
- **Frequency** (`num_orders`) – total orders per customer  
- **Monetary Value** (`total_spent`) – total spend per customer  
- **Average Order Value (AOV)** – mean order size  
- **Return Rate** – ratio of returned to completed orders  
- **Preferred Category & Brand** – most frequent purchase behavior  

These features formed the basis for both **segmentation** and **predictive modeling**.

---

## Exploratory Data Analysis (EDA)

### Customer Demographics
- Majority of customers are between **20–29 years old**.  
- Gender split shows a balanced customer base.  

### Purchase Behavior
- Most customers make only **one order**, underscoring the retention challenge.  
- Approx **37% are repeat buyers**, while 63% are one-time buyers.  
- Total spend distribution indicates that the **top 5% of customers drive a disproportionate share of revenue**.  

### Product Insights
- **Jumpsuits & Rompers**: high sales volume but also **one of the highest return rates**, highlighting a profitability issue despite popularity.  
- **Intimates**: large share of sales, but also prone to high return rates.  
- **Accessories**: strong representation among high-value customers.  

### Operations
- **Product Velocity**: On average, products sold in approx 30 days after inventory creation. Some categories moved faster, others slower.  
- **Delivery Performance**: Majority of products delivered within **48 hours**, enabling a competitive marketing promise of 1–2 day delivery.  

---

## Customer Segmentation (Unsupervised Learning)

### Method
- Applied **KMeans clustering** on standardized features.  
- Optimal number of clusters determined using **Elbow Method** and **Silhouette Score**.  
- Final segmentation: **k = 4 clusters**.  

### Cluster Profiles
| Cluster | Customer Count | Avg Orders | Avg Spend | Avg Recency (days) | Avg Return Rate | Top Category | Top Brand |
|---------|----------------|------------|-----------|---------------------|-----------------|--------------|-----------|
| 2       | 15,788         | 2.69       | 340.98    | 313                 | 0.11            | Accessories  | Allegra K |
| 0       | 5,653          | 1.23       | 109.98    | 480                 | 1.45            | Intimates    | Allegra K |
| 3       | 16,879         | 1.16       | 83.66     | 1,273               | 0.03            | Intimates    | Allegra K |
| 1       | 41,629         | 1.34       | 80.30     | 273                 | 0.02            | Intimates    | Allegra K |

### Segment Interpretation
- **Cluster 2 – High-Value Buyers**: Most profitable, multiple purchases, moderate recency. Ideal for loyalty and upselling campaigns.  
- **Cluster 0 – High Returners**: Low spend but extremely high return rates. Potentially unprofitable, requiring review of return policies.  
- **Cluster 3 – Lost Customers**: Very long recency and low spend. Dormant customers, low ROI for aggressive marketing.  
- **Cluster 1 – Low-Spend One-Timers**: Largest cluster, low spend and frequency but more recent than Cluster 3. Growth opportunity if converted into repeat buyers.  

### Visualizations
- **Heatmap**: Highlights normalized feature differences across clusters.  
- **Radar Chart**: Shows the relative “shapes” of each cluster’s behavioral profile.  

---

## Predictive Modeling (Supervised Learning)
Random Forest Classifier was chosen for prediction tasks. It balances strong accuracy with interpretability, handles imbalanced data, and identifies key drivers of behavior, making it a practical and explainable model for business decision-making.

### Target Variables
1. **Repeat Buyer** – 1 if >1 order, else 0.  
2. **High-Value Customer** – 1 if in top 20% of spenders.  
3. **Churn Risk** – 1 if no purchase in last 120 days, else 0.  

### Results

#### 1. Repeat Buyer Prediction
- Accuracy: ≈ 100% (test set = 23,985 customers)  
- Misclassifications: 7 total  
- Top Features: `total_spent`, `avg_order_value`  
- Insight: Customers with higher spending are far more likely to become repeat buyers.  

#### 2. High-Value Customer Prediction
- Accuracy: ≈ 100%  
- Misclassifications: 0  
- Top Features: `avg_order_value`, `num_orders`  
- Insight: High-value customers are distinguished by high average order values and repeat purchases.  

#### 3. Churn Risk Prediction
- Accuracy: 70%  
- Recall for churned customers: 86% (model flags churn well)  
- Recall for active customers: 16% (weak at identifying active customers)  
- Top Features: `total_spent`, `avg_order_value`, `age`  
- Insight: Model effectively identifies churned customers but struggles with active ones due to class imbalance.  

---

## Business Insights

1. **Retention is critical**: Majority of customers are one-time buyers, making retention strategies essential.  
2. **High-Value Customers**: A small but important group drives most revenue. Loyalty programs and personalized marketing should target them.  
3. **Returns Challenge**: Categories like *Jumpsuits & Rompers* and *Intimates* generate high sales but also high return rates, reducing profitability. Product quality, sizing, or expectation management may be issues.  
4. **Dormant Customers**: A large cluster of customers has not purchased in over 3 years. Win-back campaigns can be attempted, but ROI may be low.  
5. **Low-Spend One-Timers**: Largest segment, more recent than dormant customers. Represents an opportunity for targeted promotions to encourage repeat behavior.  
6. **Operational Strength**: Fast delivery (within 48 hours) is a competitive advantage and should be emphasized in marketing.  
7. **Churn Prediction**: Early churn detection enables proactive engagement, though the model requires improvement to reduce false churn predictions.  

---

## Recommendations

- **High-Value Buyers (Cluster 2)**:  
  - Implement loyalty programs and personalized recommendations.  
  - Encourage cross-selling and upselling through tailored bundles.  

- **Low-Spend One-Timers (Cluster 1)**:  
  - Use targeted promotions and discounts to drive second purchases.  
  - Introduce referral incentives to spread acquisition.  

- **High Returners (Cluster 0)**:  
  - Review return policies to reduce losses.  
  - Consider adjusting marketing to reduce targeting of high-return segments.  

- **Lost Customers (Cluster 3)**:  
  - Limited focus, but selective win-back campaigns may reactivate some.  
  - Consider exclusion from costly acquisition channels.  

- **Product Strategy**:  
  - Investigate *Jumpsuits & Rompers* and *Intimates* categories for quality or sizing issues.  
  - Strengthen product detail pages to reduce mismatched expectations and returns.  

- **Operations & Marketing**:  
  - Emphasize fast delivery (1–2 days) in promotional messaging.  
  - Use predictive models to preempt churn and target customers with retention offers.  

---

## Conclusion
This project demonstrates how e-commerce companies can combine **customer segmentation** and **predictive modeling** to drive smarter strategy.  

- **Segmentation** uncovered four distinct customer groups with clear business implications.  
- **Prediction** models achieved strong performance for repeat buyer and high-value customers, while the churn model provided useful but limited insights.  
- The analysis highlights specific opportunities in **customer retention, return management, and product strategy**.  

By aligning analytics with business needs, e-commerce organizations can improve retention, increase revenue, and optimize marketing spend.  


## Reference 

[Ecommerce customer retention rate: definitions, formulas and benchmarks](https://loyaltylion.com/blog/customer-retention-rate)
