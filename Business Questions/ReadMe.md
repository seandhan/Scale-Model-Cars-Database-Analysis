<span style = "font-family: Arial; font-weight:bold;font-size:2.5em;color:blue;">Scale Model Cars Database Analysis (SQL)

Customers and Products Analysis Using SQL

![model_cars_img.jpg](attachment:model_cars_img.jpg)

*Source*: [Diecast Secrets](https://www.carmodels.com.au/blogs/news/16248-diecast-secrets-starting-a-model-car-collection)

<span style = "font-family: Arial; font-weight:bold;font-size:2.2em;color:black;"> Introduction

The Vehicle Distributors is a fictitious global wholesale distributor specializing in die-cast vehicle models, serving a diverse customer base across more than 15 countries. Our team has been entrusted with a significant dataset analysis project, aimed at assisting the company in making strategic decisions concerning prospective expansion efforts. 

The primary goal of this undertaking is to comprehensively investigate the provided dataset and furnish well-informed responses, driven by data, to the questions posed by our client.

**Dataset**

The provided dataset, along with its corresponding schema, can be found [here](https://github.com/seandhan/Scale-Model-Cars-Database-Analysis/blob/main/stores.db).

**Database schema**

The scale model cars database contains eight tables:

1. **Customers**: customer data
2. **Employees**: all employee information
3. **Offices**: sales office information
4. **Orders**: customers' sales orders
5. **OrderDetails**: sales order line for each sales order
6. **Payments**: customers' payment records
7. **Products**: a list of scale model cars
8. **ProductLines**: a list of product line categories


The scale model cars database schema is as follows.

![stores_db_schema.png](attachment:stores_db_schema.png)

---

<span style = "font-family: Arial; font-weight:bold;font-size:2.2em;color:black;"> Objectives

The primary objective of this project is to conduct an in-depth analysis of data from a sales records database, with the intent of extracting meaningful insights that can be employed to enhance decision-making processes. The power of data analysis in the realm of sales is well-established, as it enables the derivation of essential Key Performance Indicators (KPIs), thereby fostering more informed and streamlined decision-making practices.

Utilizing data analysis offers the potential for significant savings in terms of time, resources, and financial investments. Sales data analysis encompasses a wide array of elements, including sales performance, production, customer satisfaction, and workforce efficiency, presenting a diverse set of challenges that must be addressed. To realize the overarching objectives of this project, we will delve into the following inquiries to reveal practical and actionable insights.

---

<span style = "font-family: Arial; font-weight:bold;font-size:2.2em;color:black;"> Business Questions

1. <a href="#Question_1">Which products need more or less inventory?</a>
2. <a href="#Question_2">How can we tailor marketing to customer behaviors?</a>
3. <a href="#Question_3">What is the budget for acquiring new customers?</a>
4. <a href="#Question_4">Which countries should marketing prioritize for best returns?</a>


---


```python
# Importing Libraries

import sqlite3
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import seaborn as sns
```


```python
# Database connection
```


```python
%%capture
%load_ext sql
%sql sqlite:///stores.db
```

---

# Business Questions

<a id="Question_1"></a>
## Which products need more or less inventory?

The goal is to optimize inventory levels by examining inventory reports to pinpoint in-demand products that are running low and analyze which products are selling well. This will help prevent popular items from going out of stock, improving the customer experience.

Top priority for restocking will be given to high-performing products that are nearing out of stock status. Promptly replenishing these successful, sought-after products will help maintain sufficient inventory to meet customer demand. Focusing restocking efforts on the most popular and fastest-selling items aligns with the objectives of keeping adequate stock of products customers want and providing a positive shopping experience.

**The top 10 low stock for each product**


```sql
%%sql
SELECT p.productCode,
       p.productName,
       sum(o.quantityOrdered)/p.quantityInStock as low_stock
  FROM products p 
  JOIN orderdetails o 
    ON p.productCode = o.productCode 
 GROUP BY p.productCode 
 ORDER BY low_stock DESC 
 LIMIT 10;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>productCode</th>
            <th>productName</th>
            <th>low_stock</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>S24_2000</td>
            <td>1960 BSA Gold Star DBD34</td>
            <td>67</td>
        </tr>
        <tr>
            <td>S12_1099</td>
            <td>1968 Ford Mustang</td>
            <td>13</td>
        </tr>
        <tr>
            <td>S32_4289</td>
            <td>1928 Ford Phaeton Deluxe</td>
            <td>7</td>
        </tr>
        <tr>
            <td>S32_1374</td>
            <td>1997 BMW F650 ST</td>
            <td>5</td>
        </tr>
        <tr>
            <td>S72_3212</td>
            <td>Pont Yacht</td>
            <td>2</td>
        </tr>
        <tr>
            <td>S700_3167</td>
            <td>F/A 18 Hornet 1/72</td>
            <td>1</td>
        </tr>
        <tr>
            <td>S700_1938</td>
            <td>The Mayflower</td>
            <td>1</td>
        </tr>
        <tr>
            <td>S50_4713</td>
            <td>2002 Yamaha YZR M1</td>
            <td>1</td>
        </tr>
        <tr>
            <td>S32_3522</td>
            <td>1996 Peterbilt 379 Stake Bed with Outrigger</td>
            <td>1</td>
        </tr>
        <tr>
            <td>S18_2795</td>
            <td>1928 Mercedes-Benz SSK</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



**The top 10 product performance for each product**


```sql
%%sql
SELECT 
    p.productCode,
    p.productName, 
    round(sum(o.quantityOrdered*o.priceEach),2) as product_performance
 FROM orderdetails o 
 JOIN products p 
   ON p.productCode = o.productCode 
GROUP BY p.productCode 
ORDER BY product_performance DESC 
LIMIT 10;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>productCode</th>
            <th>productName</th>
            <th>product_performance</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>S18_3232</td>
            <td>1992 Ferrari 360 Spider red</td>
            <td>276839.98</td>
        </tr>
        <tr>
            <td>S12_1108</td>
            <td>2001 Ferrari Enzo</td>
            <td>190755.86</td>
        </tr>
        <tr>
            <td>S10_1949</td>
            <td>1952 Alpine Renault 1300</td>
            <td>190017.96</td>
        </tr>
        <tr>
            <td>S10_4698</td>
            <td>2003 Harley-Davidson Eagle Drag Bike</td>
            <td>170686.0</td>
        </tr>
        <tr>
            <td>S12_1099</td>
            <td>1968 Ford Mustang</td>
            <td>161531.48</td>
        </tr>
        <tr>
            <td>S12_3891</td>
            <td>1969 Ford Falcon</td>
            <td>152543.02</td>
        </tr>
        <tr>
            <td>S18_1662</td>
            <td>1980s Black Hawk Helicopter</td>
            <td>144959.91</td>
        </tr>
        <tr>
            <td>S18_2238</td>
            <td>1998 Chrysler Plymouth Prowler</td>
            <td>142530.63</td>
        </tr>
        <tr>
            <td>S18_1749</td>
            <td>1917 Grand Touring Sedan</td>
            <td>140535.6</td>
        </tr>
        <tr>
            <td>S12_2823</td>
            <td>2002 Suzuki XREO</td>
            <td>135767.03</td>
        </tr>
    </tbody>
</table>



**Priority products for restocking**


```sql
%%sql
WITH
table1 AS(
    SELECT
        p.productCode
    FROM (SELECT
              p.productCode,
              p.productName,
              sum(o.quantityOrdered) / p.quantityInStock as low_stock
          FROM products p
          JOIN orderdetails o 
            ON p.productCode=o.productCode
         GROUP BY p.productCode
         ORDER BY low_stock DESC
         ) AS p
),
table2 AS(
    SELECT
        p.productCode
    FROM (SELECT
              p.productCode, 
              p.productLine,
              p.productName,
              round(sum(o.quantityOrdered * o.priceEach), 2) as product_performance
           FROM orderdetails o
           JOIN products p 
             ON p.productCode=o.productCode
          GROUP BY p.productCode
          ORDER BY product_performance DESC
          LIMIT 10
         ) AS p
)
SELECT
    p.productCode, 
    p.productName, 
    p.productLine
 FROM products p
WHERE p.productCode IN table1 AND p.productCode IN table2
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>productCode</th>
            <th>productName</th>
            <th>productLine</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>S10_1949</td>
            <td>1952 Alpine Renault 1300</td>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>S10_4698</td>
            <td>2003 Harley-Davidson Eagle Drag Bike</td>
            <td>Motorcycles</td>
        </tr>
        <tr>
            <td>S12_1099</td>
            <td>1968 Ford Mustang</td>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>S12_1108</td>
            <td>2001 Ferrari Enzo</td>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>S12_2823</td>
            <td>2002 Suzuki XREO</td>
            <td>Motorcycles</td>
        </tr>
        <tr>
            <td>S12_3891</td>
            <td>1969 Ford Falcon</td>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>S18_1662</td>
            <td>1980s Black Hawk Helicopter</td>
            <td>Planes</td>
        </tr>
        <tr>
            <td>S18_1749</td>
            <td>1917 Grand Touring Sedan</td>
            <td>Vintage Cars</td>
        </tr>
        <tr>
            <td>S18_2238</td>
            <td>1998 Chrysler Plymouth Prowler</td>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>S18_3232</td>
            <td>1992 Ferrari 360 Spider red</td>
            <td>Classic Cars</td>
        </tr>
    </tbody>
</table>




```python
# Connect to the SQLite database
connection = sqlite3.connect('stores.db')

priority_prods_query ="""WITH
table1 AS(
    SELECT
        p.productCode
    FROM (SELECT
              p.productCode,
              p.productName,
              sum(o.quantityOrdered) / p.quantityInStock as low_stock
          FROM products p
          JOIN orderdetails o 
            ON p.productCode=o.productCode
         GROUP BY p.productCode
         ORDER BY low_stock DESC
         ) AS p
),
table2 AS(
    SELECT
        p.productCode
    FROM (SELECT
              p.productCode, 
              p.productLine,
              p.productName,
              round(sum(o.quantityOrdered * o.priceEach), 2) as product_performance
           FROM orderdetails o
           JOIN products p 
             ON p.productCode=o.productCode
          GROUP BY p.productCode
          ORDER BY product_performance DESC
          LIMIT 10
         ) AS p
)
SELECT
    p.productCode, 
    p.productName, 
    p.productLine
 FROM products p
WHERE p.productCode IN table1 AND p.productCode IN table2"""

priority_prods = pd.read_sql_query(priority_prods_query, connection)

```

We shall use bar chart to represent the categorical variables.


```python
def bar_chart(data):
    """
    This function below generates a `bar chart` showing
    the `distribution of the categorical varible input`.
    * The function also `generates an image file` of the plot.
    * The function takes the Pandas series as the input.
    * It `computes the frequency of each unique element` and 
      displays the distribution of the elements to in horizontal bars.
    * The `percentage of each bar` is also calculated and placed to 
      the right end of each bar.
    * `sns.despine()` - removes the upper and right border of the chart
    * For each horizontal bar the width is calculated as a percentage of
      the entire quanta of datapoints.
    * The percentage is annotated to the each bar by plotting the cardinal locations.

    """
    
    # Create a horizontal count plot while sorting variables in descending order
    g=sns.countplot(y=data)
    # Remove the top and right spines from plot
    sns.despine()
    # length of the column
    col_length = len(data) 
    for p in g.patches:
        # percentage of each class of the category
        percentage = '{:.1f}%'.format(100 * p.get_width()/col_length)
        # width of the plot
        x = p.get_x() + p.get_width() + 0.02
        # height of the plot
        y = p.get_y() + p.get_height()/2
        # annotate the percentage
        g.annotate(percentage, (x, y), size = 12) 
        plt.title("Distribution of {}".format(data.name),loc="center",fontsize = 22)
    plt.show()
    # Line separator
```


```python
bar_chart(priority_prods.productLine)
```


    
![png](output_34_0.png)
    


Classic cars and motorcycles are in high demand and top priority for restocking inventory. However, vintage cars and planes have seen steady, even interest over time.

---

**Dead stock**

Deadstock are products unsold for a long time. They can become a financial burden by occupying storage space and risking damage. Deadstock is challenging to deal with as it may continue to go unsold, incurring more costs.


```sql
%%sql
SELECT
    productCode, 
    productName,
    quantityInStock , 
    buyPrice
FROM products
WHERE productCode NOT IN (SELECT productCode FROM orderdetails);
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>productCode</th>
            <th>productName</th>
            <th>quantityInStock</th>
            <th>buyPrice</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>S18_3233</td>
            <td>1985 Toyota Supra</td>
            <td>7733</td>
            <td>57.01</td>
        </tr>
    </tbody>
</table>



A potential strategy for slow-moving deadstock is price reductions to increase appeal and accelerate sales. Though counterintuitive, lower prices could save more money long-term than holding deadstock and prolonging costs.

---

<a id="Question_2"></a>
## How can we tailor marketing to customer behaviors?

To address the question about customer information, we will segment customers into VIPs (Very Important Persons) and less engaged users. This categorization enables tailored strategies that meet the specific needs of each customer group.

**Lets get the profit by customer**


```sql
%%sql
SELECT 
    o.customerNumber, 
    round(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS profit
 FROM orders o
 JOIN orderdetails od 
   ON od.orderNumber = o.orderNumber
 JOIN products p 
   ON p.productCode = od.productCode
GROUP BY o.customerNumber
ORDER BY profit DESC;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>customerNumber</th>
            <th>profit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>141</td>
            <td>326519.66</td>
        </tr>
        <tr>
            <td>124</td>
            <td>236769.39</td>
        </tr>
        <tr>
            <td>151</td>
            <td>72370.09</td>
        </tr>
        <tr>
            <td>114</td>
            <td>70311.07</td>
        </tr>
        <tr>
            <td>119</td>
            <td>60875.3</td>
        </tr>
        <tr>
            <td>148</td>
            <td>60477.38</td>
        </tr>
        <tr>
            <td>187</td>
            <td>60095.86</td>
        </tr>
        <tr>
            <td>323</td>
            <td>60013.99</td>
        </tr>
        <tr>
            <td>131</td>
            <td>58669.1</td>
        </tr>
        <tr>
            <td>450</td>
            <td>55931.37</td>
        </tr>
        <tr>
            <td>321</td>
            <td>55674.28</td>
        </tr>
        <tr>
            <td>382</td>
            <td>54724.68</td>
        </tr>
        <tr>
            <td>276</td>
            <td>54551.66</td>
        </tr>
        <tr>
            <td>146</td>
            <td>53211.19</td>
        </tr>
        <tr>
            <td>353</td>
            <td>52698.66</td>
        </tr>
        <tr>
            <td>282</td>
            <td>52331.45</td>
        </tr>
        <tr>
            <td>278</td>
            <td>52309.63</td>
        </tr>
        <tr>
            <td>496</td>
            <td>51771.5</td>
        </tr>
        <tr>
            <td>145</td>
            <td>50973.68</td>
        </tr>
        <tr>
            <td>458</td>
            <td>49192.39</td>
        </tr>
        <tr>
            <td>386</td>
            <td>48516.67</td>
        </tr>
        <tr>
            <td>448</td>
            <td>46777.54</td>
        </tr>
        <tr>
            <td>363</td>
            <td>45090.39</td>
        </tr>
        <tr>
            <td>201</td>
            <td>43523.64</td>
        </tr>
        <tr>
            <td>398</td>
            <td>43487.88</td>
        </tr>
        <tr>
            <td>298</td>
            <td>43393.75</td>
        </tr>
        <tr>
            <td>161</td>
            <td>41510.76</td>
        </tr>
        <tr>
            <td>121</td>
            <td>41391.52</td>
        </tr>
        <tr>
            <td>175</td>
            <td>41313.51</td>
        </tr>
        <tr>
            <td>166</td>
            <td>41305.17</td>
        </tr>
        <tr>
            <td>167</td>
            <td>40592.06</td>
        </tr>
        <tr>
            <td>157</td>
            <td>40429.55</td>
        </tr>
        <tr>
            <td>205</td>
            <td>40397.88</td>
        </tr>
        <tr>
            <td>334</td>
            <td>40095.85</td>
        </tr>
        <tr>
            <td>357</td>
            <td>39321.91</td>
        </tr>
        <tr>
            <td>320</td>
            <td>39136.14</td>
        </tr>
        <tr>
            <td>186</td>
            <td>38808.53</td>
        </tr>
        <tr>
            <td>406</td>
            <td>38755.5</td>
        </tr>
        <tr>
            <td>412</td>
            <td>38399.18</td>
        </tr>
        <tr>
            <td>311</td>
            <td>38334.95</td>
        </tr>
        <tr>
            <td>462</td>
            <td>36886.05</td>
        </tr>
        <tr>
            <td>259</td>
            <td>36744.01</td>
        </tr>
        <tr>
            <td>286</td>
            <td>36337.69</td>
        </tr>
        <tr>
            <td>385</td>
            <td>35987.68</td>
        </tr>
        <tr>
            <td>227</td>
            <td>34655.62</td>
        </tr>
        <tr>
            <td>172</td>
            <td>34650.17</td>
        </tr>
        <tr>
            <td>249</td>
            <td>34100.63</td>
        </tr>
        <tr>
            <td>486</td>
            <td>33598.57</td>
        </tr>
        <tr>
            <td>362</td>
            <td>32549.53</td>
        </tr>
        <tr>
            <td>328</td>
            <td>32240.57</td>
        </tr>
        <tr>
            <td>319</td>
            <td>31815.01</td>
        </tr>
        <tr>
            <td>324</td>
            <td>31667.93</td>
        </tr>
        <tr>
            <td>239</td>
            <td>31594.7</td>
        </tr>
        <tr>
            <td>209</td>
            <td>31526.6</td>
        </tr>
        <tr>
            <td>112</td>
            <td>31312.72</td>
        </tr>
        <tr>
            <td>455</td>
            <td>29265.9</td>
        </tr>
        <tr>
            <td>379</td>
            <td>28969.09</td>
        </tr>
        <tr>
            <td>202</td>
            <td>28882.99</td>
        </tr>
        <tr>
            <td>250</td>
            <td>28390.81</td>
        </tr>
        <tr>
            <td>181</td>
            <td>28279.5</td>
        </tr>
        <tr>
            <td>129</td>
            <td>28092.43</td>
        </tr>
        <tr>
            <td>128</td>
            <td>27728.34</td>
        </tr>
        <tr>
            <td>350</td>
            <td>27184.74</td>
        </tr>
        <tr>
            <td>171</td>
            <td>27143.39</td>
        </tr>
        <tr>
            <td>240</td>
            <td>26808.79</td>
        </tr>
        <tr>
            <td>424</td>
            <td>26001.55</td>
        </tr>
        <tr>
            <td>314</td>
            <td>25861.96</td>
        </tr>
        <tr>
            <td>233</td>
            <td>25856.86</td>
        </tr>
        <tr>
            <td>299</td>
            <td>25665.76</td>
        </tr>
        <tr>
            <td>495</td>
            <td>25244.69</td>
        </tr>
        <tr>
            <td>144</td>
            <td>25138.38</td>
        </tr>
        <tr>
            <td>256</td>
            <td>24610.8</td>
        </tr>
        <tr>
            <td>177</td>
            <td>24360.18</td>
        </tr>
        <tr>
            <td>204</td>
            <td>24296.9</td>
        </tr>
        <tr>
            <td>260</td>
            <td>24118.93</td>
        </tr>
        <tr>
            <td>216</td>
            <td>24113.54</td>
        </tr>
        <tr>
            <td>242</td>
            <td>23905.16</td>
        </tr>
        <tr>
            <td>333</td>
            <td>22579.18</td>
        </tr>
        <tr>
            <td>471</td>
            <td>22433.82</td>
        </tr>
        <tr>
            <td>484</td>
            <td>21225.65</td>
        </tr>
        <tr>
            <td>339</td>
            <td>20737.79</td>
        </tr>
        <tr>
            <td>189</td>
            <td>19588.29</td>
        </tr>
        <tr>
            <td>447</td>
            <td>18957.41</td>
        </tr>
        <tr>
            <td>344</td>
            <td>18953.3</td>
        </tr>
        <tr>
            <td>452</td>
            <td>18358.11</td>
        </tr>
        <tr>
            <td>487</td>
            <td>17230.12</td>
        </tr>
        <tr>
            <td>475</td>
            <td>17186.93</td>
        </tr>
        <tr>
            <td>211</td>
            <td>16868.68</td>
        </tr>
        <tr>
            <td>347</td>
            <td>14928.37</td>
        </tr>
        <tr>
            <td>173</td>
            <td>13734.7</td>
        </tr>
        <tr>
            <td>456</td>
            <td>13066.02</td>
        </tr>
        <tr>
            <td>415</td>
            <td>13033.35</td>
        </tr>
        <tr>
            <td>381</td>
            <td>11693.99</td>
        </tr>
        <tr>
            <td>489</td>
            <td>10868.04</td>
        </tr>
        <tr>
            <td>103</td>
            <td>10063.8</td>
        </tr>
        <tr>
            <td>473</td>
            <td>9532.93</td>
        </tr>
        <tr>
            <td>198</td>
            <td>6586.02</td>
        </tr>
        <tr>
            <td>219</td>
            <td>2610.87</td>
        </tr>
    </tbody>
</table>



**Top 5 VIP customers**


```sql
%%sql
WITH performance AS (
    SELECT o.customerNumber, 
    round(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS profit
    FROM orders o
    JOIN orderdetails od 
      ON od.orderNumber = o.orderNumber
    JOIN products p 
      ON p.productCode = od.productCode
   GROUP BY o.customerNumber
   ORDER BY profit DESC
)

SELECT 
    c.contactFirstName ||" "||c.contactLastName as name,
    c.city,
    c.country, 
    profit
FROM customers c 
JOIN performance 
  ON performance.customerNumber = c.customerNumber 
LIMIT 5;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>name</th>
            <th>city</th>
            <th>country</th>
            <th>profit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Diego  Freyre</td>
            <td>Madrid</td>
            <td>Spain</td>
            <td>326519.66</td>
        </tr>
        <tr>
            <td>Susan Nelson</td>
            <td>San Rafael</td>
            <td>USA</td>
            <td>236769.39</td>
        </tr>
        <tr>
            <td>Jeff Young</td>
            <td>NYC</td>
            <td>USA</td>
            <td>72370.09</td>
        </tr>
        <tr>
            <td>Peter Ferguson</td>
            <td>Melbourne</td>
            <td>Australia</td>
            <td>70311.07</td>
        </tr>
        <tr>
            <td>Janine  Labrune</td>
            <td>Nantes</td>
            <td>France</td>
            <td>60875.3</td>
        </tr>
    </tbody>
</table>



**The top five least-engaged customers**


```sql
%%sql
WITH performance AS (
    SELECT o.customerNumber, 
           round(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS profit
      FROM orders o
      JOIN orderdetails od 
        ON od.orderNumber = o.orderNumber
      JOIN products p 
        ON p.productCode = od.productCode
     GROUP BY o.customerNumber
     ORDER BY profit
)

SELECT 
    c.contactFirstName ||" "||c.contactLastName as name,
    c.city,
    c.country, 
    profit
FROM customers c 
JOIN performance 
  ON performance.customerNumber = c.customerNumber  
LIMIT 5;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>name</th>
            <th>city</th>
            <th>country</th>
            <th>profit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Mary Young</td>
            <td>Glendale</td>
            <td>USA</td>
            <td>2610.87</td>
        </tr>
        <tr>
            <td>Leslie Taylor</td>
            <td>Brickhaven</td>
            <td>USA</td>
            <td>6586.02</td>
        </tr>
        <tr>
            <td>Franco Ricotti</td>
            <td>Milan</td>
            <td>Italy</td>
            <td>9532.93</td>
        </tr>
        <tr>
            <td>Carine  Schmitt</td>
            <td>Nantes</td>
            <td>France</td>
            <td>10063.8</td>
        </tr>
        <tr>
            <td>Thomas  Smith</td>
            <td>London</td>
            <td>UK</td>
            <td>10868.04</td>
        </tr>
    </tbody>
</table>



---

<a id="Question_3"></a>
## What is the budget for acquiring new customers?

To determine the budget for acquiring new customers, we need to calculate the Customer Lifetime Value (LTV). LTV shows the average revenue generated per customer over the entire customer relationship. This will indicate how much we can optimally spend on marketing to gain new customers.

**Compute the Customer Lifetime Value**


```sql
%%sql
WITH  performance AS (
    SELECT 
        o.customerNumber, 
        round(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)),2) AS profit
      FROM orders o
      JOIN orderdetails od 
        ON od.orderNumber = o.orderNumber
      JOIN products p 
        ON p.productCode = od.productCode
     GROUP BY o.customerNumber
     ORDER BY profit
)

SELECT round(avg(profit),2) as Customer_lifetime_value
  FROM performance;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>Customer_lifetime_value</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>39039.59</td>
        </tr>
    </tbody>
</table>



The Lifetime Value (LTV) metric shows the total profitability of an average customer with our store. It allows us to forecast potential profits, like estimating we could earn \\$390,395 ($39,039.59 X 10) by acquiring 10 new customers next month. Knowing the LTV helps us set appropriate budgets for gaining new customers, as we can predict the long-term value of acquisitions.

----

<a id="Question_4"></a>
## Which countries should marketing prioritize for best returns?

Since marketing has costs, it's important to focus on markets where our strategy will be most profitable. Targeting the highest potential markets will help ensure our marketing drives the best return on investment and maximizes profits.


```sql
%%sql
SELECT 
    c.country, 
    round(SUM(od.quantityOrdered * (od.priceEach - p.buyPrice)), 2) AS profit
 FROM orders o
 JOIN orderdetails od 
   ON od.orderNumber = o.orderNumber
 JOIN products p 
   ON p.productCode = od.productCode 
 JOIN customers c 
   ON o.customerNumber = c.customerNumber  
GROUP BY c.country 
ORDER BY profit DESC
LIMIT 5;
```

     * sqlite:///stores.db
    Done.
    




<table>
    <thead>
        <tr>
            <th>country</th>
            <th>profit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>USA</td>
            <td>1308815.59</td>
        </tr>
        <tr>
            <td>Spain</td>
            <td>440004.54</td>
        </tr>
        <tr>
            <td>France</td>
            <td>413016.12</td>
        </tr>
        <tr>
            <td>Australia</td>
            <td>222207.18</td>
        </tr>
        <tr>
            <td>New Zealand</td>
            <td>189506.58</td>
        </tr>
    </tbody>
</table>



The analysis shows we should focus marketing on select markets in North America (USA), Europe (Spain, France), and Oceania (Australia, New Zealand). Concentrating efforts on these high-potential regions will allow efficient resource allocation and let us pursue the most promising opportunities. This targeted approach can improve overall business performance.

----

# Recommendations

After having analysed the data based on the queries put forward by the Vehicle Model Company, the following are the recommendations that we could make based on the insights we have gained.

- To optimize inventory, classic cars and motorcycles should be prioritized for restocking due to high demand, while vintage cars and planes can be managed at a steady pace given even interest over time. For slow-moving deadstock, consider strategic price reductions to increase appeal and accelerate sales. Though counterintuitive, lower prices could reduce losses compared to prolonging costs by holding deadstock. This balanced approach can align inventory to demand and minimize financial burden of deadstock.


- Personalized interactions and recognition of loyal customers can build long-term relationships and repeat business. For VIP customers, we can offer exclusive, tailored events and initiatives to increase satisfaction and loyalty. For less engaged customers, targeted campaigns based on their needs and preferences can reinvigorate interest in our brand.


- We should prioritize marketing in North America (USA), Europe (Spain, France), and Oceania (Australia, New Zealand). Concentrating on these targeted markets can improve efficiency and pursue the most promising opportunities. This focused approach can enhance overall business performance.


----

<a id="Question_3"></a>

<a id="Question_4"></a>

<a id="Question_5"></a>

<a id="Question_6"></a>

<a id="Question_7"></a>

<a id="Question_8"></a>

<a id="Question_9"></a>
