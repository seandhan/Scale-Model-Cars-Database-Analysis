[![forthebadge](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/USES-SQLITE-.svg)]()
[![forthebadge](https://forthebadge.com/images/badges/made-with-python.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/made-with-markdown.svg)](https://forthebadge.com)

<h1 align="center">Scale Model Cars Database Analysis (SQL) 🚗 </h1>


<p align="center">The primary objective of this project is to conduct an in-depth analysis of data from a sales records database, with the intent of extracting meaningful insights that can be employed to enhance decision-making processes. 
  </p>




---

## 📝 Table of Contents

- [🧐 Description](#Description)
- [💻 Database Overview](#Database-Overview)
- [📊 Exploratory Data Analysis](#Exploratory-Data-Analysis)
- [🚀 Business Questions](business-questions)
- [🎨 Contributing](#contributing)
- [🌟 Support](#support)

## 🧐 Description <a name = "Description"></a>

The power of data analysis in the realm of sales is well-established, as it enables the derivation of essential Key Performance Indicators (KPIs), thereby fostering more informed and streamlined decision-making practices.

The Vehicle Distributors is a fictitious global wholesale distributor specializing in die-cast vehicle models, serving a diverse customer base across more than 15 countries. Our team has been entrusted with a significant dataset analysis project, aimed at assisting the company in making strategic decisions concerning prospective expansion efforts.

The primary goal of this undertaking is to comprehensively investigate the provided dataset and furnish well-informed responses, driven by data, to the questions posed by our client.

---

## 💻 Database Overview <a name = "Database-Overview"></a>

The database source file can found through the following link:
### Click to view 👇:

[![Data_link](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Data-LINK-.svg)](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/stores.db)


The scale model cars database contains eight tables:

| customers | employees | offices | orders | orderdetails | payments | products | productlines |
| --------- | --------- | ------- | ------ | ------------ | -------- | -------- | ------------ | 

The scale model cars database schema is as follows.

![stores_db_schema.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/stores_db_schema.png)

---

## 📊 Exploratory Data Analysis <a name = "Exploratory-Data-Analysis"></a>

The database was accessed and explored using Python SQLite.

### Click to view 👇:

[![Solution-Exploratory Data Analysis](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Solution-Exploratory%20Data%20Analysis-.svg)](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/Exploratory%20Data%20Analysis/ReadME.md)




---

🚀 Business Questions](business-questions)

## 🚀 Business Questions <a name = "business-questions"></a>


The following business questions will be addressed in the analysis

1. **Which products need more or less inventory?**
2. **How can we tailor marketing to customer behaviors?**
3. **What is the budget for acquiring new customers?**
4. **Which countries should marketing prioritize for best returns?**


---

## Project Details



## Data Source

Data Source: [stores.db](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/stores.db)

---

## Code

The code used to the explore this database can be found in the Jupyter notebook. Click [HERE](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/Scale%20Model%20Cars%20Database%20Analysis.ipynb) 

---

## Database Exploration

### Overview

The database summary is as follows:

![db_structure_summary.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/db_structure_summary.png)


There are **110** products across **7** product lines 

---

### Missing Values

![missing_values.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/missing_values.png)

---

## Key Observations


### Product Lines

![product_line.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/product_line.png)
---

### top 10 customer count by country

![top10_customers.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/top10_customers.png)
---
### Priority products for restocking

![Priority products for restocking.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Priority%20products%20for%20restocking.png)

![Dist Priority products for restocking.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Dist%20Priority%20products%20for%20restocking.png)


Classic cars and motorcycles are in high demand and top priority for restocking inventory. However, vintage cars and planes have seen steady, even interest over time.

---

### Dead stock

![Dead stock.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Dead%20stock.png)

A potential strategy for slow-moving deadstock is price reductions to increase appeal and accelerate sales. Though counterintuitive, lower prices could save more money long-term than holding deadstock and prolonging costs

---
### Top 5 VIP customers

![Top 5 VIP.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Top%205%20VIP.png)

---
### Top 5 least-engaged customers

![Top 5 Least engaged.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Top%205%20Least%20engaged.png)

---

### Customer Lifetime Value

![CLV.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/CLV.png)

The Lifetime Value (LTV) metric shows the total profitability of an average customer with our store. It allows us to forecast potential profits, like estimating we could earn $390,395 ($39,039.59 X 10) by acquiring 10 new customers next month. Knowing the LTV helps us set appropriate budgets for gaining new customers, as we can predict the long-term value of acquisition

---
### Top 5 profitable countries

![Top 5 countries.png](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/images/Top%205%20countries.png)

The analysis shows we should focus marketing on select markets in North America (USA), Europe (Spain, France), and Oceania (Australia, New Zealand). Concentrating efforts on these high-potential regions will allow efficient resource allocation and let us pursue the most promising opportunities. This targeted approach can improve overall business performance.

---

## Recommendations

After having analysed the data based on the queries put forward by the Vehicle Model Company, the following are the recommendations that we could make based on the insights we have gained.

To optimize inventory, classic cars and motorcycles should be prioritized for restocking due to high demand, while vintage cars and planes can be managed at a steady pace given even interest over time. For slow-moving deadstock, consider strategic price reductions to increase appeal and accelerate sales. Though counterintuitive, lower prices could reduce losses compared to prolonging costs by holding deadstock. This balanced approach can align inventory to demand and minimize financial burden of deadstock.

Personalized interactions and recognition of loyal customers can build long-term relationships and repeat business. For VIP customers, we can offer exclusive, tailored events and initiatives to increase satisfaction and loyalty. For less engaged customers, targeted campaigns based on their needs and preferences can reinvigorate interest in our brand.

We should prioritize marketing in North America (USA), Europe (Spain, France), and Oceania (Australia, New Zealand). Concentrating on these targeted markets can improve efficiency and pursue the most promising opportunities. This focused approach can enhance overall business performance

---

## Contact Information

- Email: [sean_dhanasar@msn.com](mailto:sean_dhanasar@msn.com)
- LinkedIn: [Sean Dhanasar](https://www.linkedin.com/in/sdhanasar)


