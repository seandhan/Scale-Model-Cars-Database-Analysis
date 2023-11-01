# Exploratory Data Analysis


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

# Exploring database

**List of tables**


```sql
%%sql
SELECT name FROM sqlite_master WHERE type='table';
```

*Output:*




<table>
    <thead>
        <tr>
            <th>name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>customers</td>
        </tr>
        <tr>
            <td>employees</td>
        </tr>
        <tr>
            <td>offices</td>
        </tr>
        <tr>
            <td>orderdetails</td>
        </tr>
        <tr>
            <td>orders</td>
        </tr>
        <tr>
            <td>payments</td>
        </tr>
        <tr>
            <td>productlines</td>
        </tr>
        <tr>
            <td>products</td>
        </tr>
    </tbody>
</table>



**Overall Table Summary**


```python
%%capture

# Execute the query to list the names of tables
table_query = "SELECT name FROM sqlite_master WHERE type='table';"
table_names = %sql $table_query

# Create an empty DataFrame to store the table summaries
table_summaries = []

# Iterate through the table names
for table_name in table_names:
    table_name = table_name[0]

    # Execute queries to get the number of attributes and rows per table
    attributes_query = f"PRAGMA table_info({table_name});"
    row_count_query = f"SELECT COUNT(*) FROM {table_name};"

    # Get the results
    attributes = %sql $attributes_query
    row_count = %sql $row_count_query

    # Add the table summary to the list
    table_summaries.append((table_name, len(attributes), row_count[0][0]))

# Create a DataFrame from the list of table summaries
table_summary_df = pd.DataFrame(table_summaries, columns=[
    'Table Name', 'Number of Attributes', 'Number of Rows'])
```


```python
# Display the DataFrame
display(table_summary_df)
```


<div>

    
*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Table Name</th>
      <th>Number of Attributes</th>
      <th>Number of Rows</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>customers</td>
      <td>13</td>
      <td>122</td>
    </tr>
    <tr>
      <th>1</th>
      <td>employees</td>
      <td>8</td>
      <td>23</td>
    </tr>
    <tr>
      <th>2</th>
      <td>offices</td>
      <td>9</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>orderdetails</td>
      <td>5</td>
      <td>2996</td>
    </tr>
    <tr>
      <th>4</th>
      <td>orders</td>
      <td>7</td>
      <td>326</td>
    </tr>
    <tr>
      <th>5</th>
      <td>payments</td>
      <td>4</td>
      <td>273</td>
    </tr>
    <tr>
      <th>6</th>
      <td>productlines</td>
      <td>4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>7</th>
      <td>products</td>
      <td>9</td>
      <td>110</td>
    </tr>
  </tbody>
</table>
</div>


---

**Tables Missing Values Summary**


```python
%%capture

# Create an empty DataFrame to store the results
results = []

# Execute the query to list the names of tables
table_query = "SELECT name FROM sqlite_master WHERE type='table';"
tables = %sql $table_query

# Iterate through the tables
for table in tables:
    table_name = table[0]

    # Execute a query to retrieve the names of attributes (columns) in the table
    attributes_query = f"PRAGMA table_info({table_name});"
    attributes_info = %sql $attributes_query

    # Check each attribute for missing values
    for attribute_info in attributes_info:
        attribute_name = attribute_info[1]
        notnull_query = f"SELECT COUNT(*) FROM {table_name} WHERE {attribute_name} IS NULL;"
        missing_count = %sql $notnull_query

        if missing_count[0][0] > 0:
            results.append((table_name, attribute_name, missing_count[0][0]))

# Create a DataFrame from the results
missing_values_df = pd.DataFrame(
    results, columns=['Table Name', 'Attribute Name', 'Missing Values'])
```


```python
# Display the DataFrame
display(missing_values_df)
```


<div>
    
*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Table Name</th>
      <th>Attribute Name</th>
      <th>Missing Values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>customers</td>
      <td>addressLine2</td>
      <td>100</td>
    </tr>
    <tr>
      <th>1</th>
      <td>customers</td>
      <td>state</td>
      <td>73</td>
    </tr>
    <tr>
      <th>2</th>
      <td>customers</td>
      <td>postalCode</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>customers</td>
      <td>salesRepEmployeeNumber</td>
      <td>22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>employees</td>
      <td>reportsTo</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>offices</td>
      <td>addressLine2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>6</th>
      <td>offices</td>
      <td>state</td>
      <td>3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>orders</td>
      <td>shippedDate</td>
      <td>14</td>
    </tr>
    <tr>
      <th>8</th>
      <td>orders</td>
      <td>comments</td>
      <td>246</td>
    </tr>
    <tr>
      <th>9</th>
      <td>productlines</td>
      <td>htmlDescription</td>
      <td>7</td>
    </tr>
    <tr>
      <th>10</th>
      <td>productlines</td>
      <td>image</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>


---

## `customers` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM customers LIMIT 5;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>customerNumber</th>
            <th>customerName</th>
            <th>contactLastName</th>
            <th>contactFirstName</th>
            <th>phone</th>
            <th>addressLine1</th>
            <th>addressLine2</th>
            <th>city</th>
            <th>state</th>
            <th>postalCode</th>
            <th>country</th>
            <th>salesRepEmployeeNumber</th>
            <th>creditLimit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>103</td>
            <td>Atelier graphique</td>
            <td>Schmitt</td>
            <td>Carine </td>
            <td>40.32.2555</td>
            <td>54, rue Royale</td>
            <td>None</td>
            <td>Nantes</td>
            <td>None</td>
            <td>44000</td>
            <td>France</td>
            <td>1370</td>
            <td>21000</td>
        </tr>
        <tr>
            <td>112</td>
            <td>Signal Gift Stores</td>
            <td>King</td>
            <td>Jean</td>
            <td>7025551838</td>
            <td>8489 Strong St.</td>
            <td>None</td>
            <td>Las Vegas</td>
            <td>NV</td>
            <td>83030</td>
            <td>USA</td>
            <td>1166</td>
            <td>71800</td>
        </tr>
        <tr>
            <td>114</td>
            <td>Australian Collectors, Co.</td>
            <td>Ferguson</td>
            <td>Peter</td>
            <td>03 9520 4555</td>
            <td>636 St Kilda Road</td>
            <td>Level 3</td>
            <td>Melbourne</td>
            <td>Victoria</td>
            <td>3004</td>
            <td>Australia</td>
            <td>1611</td>
            <td>117300</td>
        </tr>
        <tr>
            <td>119</td>
            <td>La Rochelle Gifts</td>
            <td>Labrune</td>
            <td>Janine </td>
            <td>40.67.8555</td>
            <td>67, rue des Cinquante Otages</td>
            <td>None</td>
            <td>Nantes</td>
            <td>None</td>
            <td>44000</td>
            <td>France</td>
            <td>1370</td>
            <td>118200</td>
        </tr>
        <tr>
            <td>121</td>
            <td>Baane Mini Imports</td>
            <td>Bergulfsen</td>
            <td>Jonas </td>
            <td>07-98 9555</td>
            <td>Erling Skakkes gate 78</td>
            <td>None</td>
            <td>Stavern</td>
            <td>None</td>
            <td>4110</td>
            <td>Norway</td>
            <td>1504</td>
            <td>81700</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(customers);
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>customerNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>customerName</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>contactLastName</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>contactFirstName</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>phone</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>addressLine1</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>addressLine2</td>
            <td>nvarchar(50)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>7</td>
            <td>city</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>8</td>
            <td>state</td>
            <td>nvarchar(50)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>9</td>
            <td>postalCode</td>
            <td>nvarchar(15)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>10</td>
            <td>country</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>11</td>
            <td>salesRepEmployeeNumber</td>
            <td>INTEGER</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>12</td>
            <td>creditLimit</td>
            <td>numeric(10,2)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



**Lets get the top 10 customer count by country**


```sql
%%sql
SELECT 
    country,
    COUNT(customerNumber) as customer_count,
    ROUND(COUNT(country) * 100.0 / SUM(COUNT(country)) OVER(), 1) AS 'percentage_%'
  FROM customers
 GROUP BY country

ORDER BY customer_count DESC
LIMIT 10;
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>country</th>
            <th>customer_count</th>
            <th>percentage_%</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>USA</td>
            <td>36</td>
            <td>29.5</td>
        </tr>
        <tr>
            <td>Germany</td>
            <td>13</td>
            <td>10.7</td>
        </tr>
        <tr>
            <td>France</td>
            <td>12</td>
            <td>9.8</td>
        </tr>
        <tr>
            <td>Spain</td>
            <td>7</td>
            <td>5.7</td>
        </tr>
        <tr>
            <td>Australia</td>
            <td>5</td>
            <td>4.1</td>
        </tr>
        <tr>
            <td>UK</td>
            <td>5</td>
            <td>4.1</td>
        </tr>
        <tr>
            <td>Italy</td>
            <td>4</td>
            <td>3.3</td>
        </tr>
        <tr>
            <td>New Zealand</td>
            <td>4</td>
            <td>3.3</td>
        </tr>
        <tr>
            <td>Canada</td>
            <td>3</td>
            <td>2.5</td>
        </tr>
        <tr>
            <td>Finland</td>
            <td>3</td>
            <td>2.5</td>
        </tr>
    </tbody>
</table>




```python
# Connect to the SQLite database
connection = sqlite3.connect('stores.db')  # Replace with your database file name

# Define the table name and categorical attribute to analyze
table_name = 'customers'  # Replace with your table name
categorical_attribute = 'country'  # Replace with the attribute name

# Retrieve the data from the database
query = f"""
SELECT {categorical_attribute}, 
COUNT(*) as count FROM {table_name} GROUP BY {categorical_attribute} ORDER BY count DESC LIMIT 10;
"""
data = pd.read_sql_query(query, connection)

# Create the barplot using seaborn
plt.figure(figsize=(10, 6))
# sns.set(style="whitegrid")
sns.barplot(y=categorical_attribute, x="count", data=data)
plt.title(f"Top 10 Customer Count by {categorical_attribute}")
plt.xlabel("Country")
plt.ylabel("Count")
# plt.xticks(rotation=45)
plt.show()
```


    
![png](output_38_0.png)
    


----

## `employees` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM employees LIMIT 5;
```

     
*Output:*






<table>
    <thead>
        <tr>
            <th>employeeNumber</th>
            <th>lastName</th>
            <th>firstName</th>
            <th>extension</th>
            <th>email</th>
            <th>officeCode</th>
            <th>reportsTo</th>
            <th>jobTitle</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1002</td>
            <td>Murphy</td>
            <td>Diane</td>
            <td>x5800</td>
            <td>dmurphy@classicmodelcars.com</td>
            <td>1</td>
            <td>None</td>
            <td>President</td>
        </tr>
        <tr>
            <td>1056</td>
            <td>Patterson</td>
            <td>Mary</td>
            <td>x4611</td>
            <td>mpatterso@classicmodelcars.com</td>
            <td>1</td>
            <td>1002</td>
            <td>VP Sales</td>
        </tr>
        <tr>
            <td>1076</td>
            <td>Firrelli</td>
            <td>Jeff</td>
            <td>x9273</td>
            <td>jfirrelli@classicmodelcars.com</td>
            <td>1</td>
            <td>1002</td>
            <td>VP Marketing</td>
        </tr>
        <tr>
            <td>1088</td>
            <td>Patterson</td>
            <td>William</td>
            <td>x4871</td>
            <td>wpatterson@classicmodelcars.com</td>
            <td>6</td>
            <td>1056</td>
            <td>Sales Manager (APAC)</td>
        </tr>
        <tr>
            <td>1102</td>
            <td>Bondur</td>
            <td>Gerard</td>
            <td>x5408</td>
            <td>gbondur@classicmodelcars.com</td>
            <td>4</td>
            <td>1056</td>
            <td>Sale Manager (EMEA)</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(employees);
```
    
*Output:*






<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>employeeNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>lastName</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>firstName</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>extension</td>
            <td>nvarchar(10)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>email</td>
            <td>nvarchar(100)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>officeCode</td>
            <td>nvarchar(10)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>reportsTo</td>
            <td>INTEGER</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>7</td>
            <td>jobTitle</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



**Employee to Manager**


```sql
%%sql
SELECT
    e.employeeNumber,
    e.firstName || ' ' || e.lastName AS employeeName,
    e.jobTitle,
    m.firstName || ' ' || m.lastName AS managerName,
    ROW_NUMBER() OVER (PARTITION BY e.reportsTo ORDER BY e.employeeNumber) AS level
FROM employees e
LEFT JOIN employees m ON e.reportsTo = m.employeeNumber;
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>employeeNumber</th>
            <th>employeeName</th>
            <th>jobTitle</th>
            <th>managerName</th>
            <th>level</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1002</td>
            <td>Diane Murphy</td>
            <td>President</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1056</td>
            <td>Mary Patterson</td>
            <td>VP Sales</td>
            <td>Diane Murphy</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1076</td>
            <td>Jeff Firrelli</td>
            <td>VP Marketing</td>
            <td>Diane Murphy</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1088</td>
            <td>William Patterson</td>
            <td>Sales Manager (APAC)</td>
            <td>Mary Patterson</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1102</td>
            <td>Gerard Bondur</td>
            <td>Sale Manager (EMEA)</td>
            <td>Mary Patterson</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1143</td>
            <td>Anthony Bow</td>
            <td>Sales Manager (NA)</td>
            <td>Mary Patterson</td>
            <td>3</td>
        </tr>
        <tr>
            <td>1621</td>
            <td>Mami Nishi</td>
            <td>Sales Rep</td>
            <td>Mary Patterson</td>
            <td>4</td>
        </tr>
        <tr>
            <td>1611</td>
            <td>Andy Fixter</td>
            <td>Sales Rep</td>
            <td>William Patterson</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1612</td>
            <td>Peter Marsh</td>
            <td>Sales Rep</td>
            <td>William Patterson</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1619</td>
            <td>Tom King</td>
            <td>Sales Rep</td>
            <td>William Patterson</td>
            <td>3</td>
        </tr>
        <tr>
            <td>1337</td>
            <td>Loui Bondur</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1370</td>
            <td>Gerard Hernandez</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1401</td>
            <td>Pamela Castillo</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>3</td>
        </tr>
        <tr>
            <td>1501</td>
            <td>Larry Bott</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>4</td>
        </tr>
        <tr>
            <td>1504</td>
            <td>Barry Jones</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>5</td>
        </tr>
        <tr>
            <td>1702</td>
            <td>Martin Gerard</td>
            <td>Sales Rep</td>
            <td>Gerard Bondur</td>
            <td>6</td>
        </tr>
        <tr>
            <td>1165</td>
            <td>Leslie Jennings</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1166</td>
            <td>Leslie Thompson</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1188</td>
            <td>Julie Firrelli</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>3</td>
        </tr>
        <tr>
            <td>1216</td>
            <td>Steve Patterson</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>4</td>
        </tr>
        <tr>
            <td>1286</td>
            <td>Foon Yue Tseng</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>5</td>
        </tr>
        <tr>
            <td>1323</td>
            <td>George Vanauf</td>
            <td>Sales Rep</td>
            <td>Anthony Bow</td>
            <td>6</td>
        </tr>
        <tr>
            <td>1625</td>
            <td>Yoshimi Kato</td>
            <td>Sales Rep</td>
            <td>Mami Nishi</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



**organizational_structure**


```python
query = """
SELECT
    e.employeeNumber,
    e.firstName || ' ' || e.lastName AS employeeName,
    e.jobTitle,
    m.firstName || ' ' || m.lastName AS managerName,
    ROW_NUMBER() OVER (PARTITION BY e.reportsTo ORDER BY e.employeeNumber) AS level
FROM employees e
LEFT JOIN employees m ON e.reportsTo = m.employeeNumber;

"""

data = pd.read_sql_query(query, connection)

org_data = data[['employeeName','managerName']]

import networkx as nx
# Create a directed graph using NetworkX
G = nx.DiGraph()

# Add nodes to the graph
for _, row in org_data.iterrows():
    G.add_node(row['employeeName'])
    if not pd.isna(row['managerName']):
        G.add_edge(row['managerName'], row['employeeName'])

# Define node positions using Kamada-Kawai layout with adjusted scale and center
pos = nx.kamada_kawai_layout(G, scale=10, center=(0, 0))

# Mirror the layout horizontally (invert x-coordinates)
for node, (x, y) in pos.items():
    pos[node] = (-x, y)

# Create the organizational chart using Matplotlib
plt.figure(figsize=(12, 8))
nx.draw(G, pos, with_labels=True, node_size=2000, node_color='lightblue', font_size=8, font_color='black', node_shape='o')
plt.title("Organizational Chart")
plt.axis('off')

# Display the chart
plt.show()
```


    
![png](output_48_0.png)
    


----

## `offices` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM offices;
```
    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>officeCode</th>
            <th>city</th>
            <th>phone</th>
            <th>addressLine1</th>
            <th>addressLine2</th>
            <th>state</th>
            <th>country</th>
            <th>postalCode</th>
            <th>territory</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>San Francisco</td>
            <td>+1 650 219 4782</td>
            <td>100 Market Street</td>
            <td>Suite 300</td>
            <td>CA</td>
            <td>USA</td>
            <td>94080</td>
            <td>NA</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Boston</td>
            <td>+1 215 837 0825</td>
            <td>1550 Court Place</td>
            <td>Suite 102</td>
            <td>MA</td>
            <td>USA</td>
            <td>02107</td>
            <td>NA</td>
        </tr>
        <tr>
            <td>3</td>
            <td>NYC</td>
            <td>+1 212 555 3000</td>
            <td>523 East 53rd Street</td>
            <td>apt. 5A</td>
            <td>NY</td>
            <td>USA</td>
            <td>10022</td>
            <td>NA</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Paris</td>
            <td>+33 14 723 4404</td>
            <td>43 Rue Jouffroy D&#x27;abbans</td>
            <td>None</td>
            <td>None</td>
            <td>France</td>
            <td>75017</td>
            <td>EMEA</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Tokyo</td>
            <td>+81 33 224 5000</td>
            <td>4-1 Kioicho</td>
            <td>None</td>
            <td>Chiyoda-Ku</td>
            <td>Japan</td>
            <td>102-8578</td>
            <td>Japan</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Sydney</td>
            <td>+61 2 9264 2451</td>
            <td>5-11 Wentworth Avenue</td>
            <td>Floor #2</td>
            <td>None</td>
            <td>Australia</td>
            <td>NSW 2010</td>
            <td>APAC</td>
        </tr>
        <tr>
            <td>7</td>
            <td>London</td>
            <td>+44 20 7877 2041</td>
            <td>25 Old Broad Street</td>
            <td>Level 7</td>
            <td>None</td>
            <td>UK</td>
            <td>EC2N 1HN</td>
            <td>EMEA</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(offices);
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>officeCode</td>
            <td>nvarchar(10)</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>city</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>phone</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>addressLine1</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>addressLine2</td>
            <td>nvarchar(50)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>state</td>
            <td>nvarchar(50)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>country</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>7</td>
            <td>postalCode</td>
            <td>nvarchar(15)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>8</td>
            <td>territory</td>
            <td>nvarchar(10)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



**Lets get the top 10 office count by country**


```sql
%%sql
SELECT 
    country,
    COUNT(officeCode) as office_count,
    ROUND(COUNT(country) * 100.0 / SUM(COUNT(country)) OVER(), 1) AS 'percentage_%'
  FROM offices
 GROUP BY country

ORDER BY office_count DESC
LIMIT 10;
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>country</th>
            <th>office_count</th>
            <th>percentage_%</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>USA</td>
            <td>3</td>
            <td>42.9</td>
        </tr>
        <tr>
            <td>Australia</td>
            <td>1</td>
            <td>14.3</td>
        </tr>
        <tr>
            <td>France</td>
            <td>1</td>
            <td>14.3</td>
        </tr>
        <tr>
            <td>Japan</td>
            <td>1</td>
            <td>14.3</td>
        </tr>
        <tr>
            <td>UK</td>
            <td>1</td>
            <td>14.3</td>
        </tr>
    </tbody>
</table>




```python
# Connect to the SQLite database
connection = sqlite3.connect('stores.db')  # Replace with your database file name

# Define the table name and categorical attribute to analyze
table_name = 'offices'  # Replace with your table name
categorical_attribute = 'country'  # Replace with the attribute name

# Retrieve the data from the database
query = f"""
SELECT {categorical_attribute}, 
COUNT(*) as count FROM {table_name} GROUP BY {categorical_attribute} ORDER BY count DESC;
"""
data = pd.read_sql_query(query, connection)

# Create the barplot using seaborn
plt.figure(figsize=(10, 6))
# sns.set(style="whitegrid")
sns.barplot(y=categorical_attribute, x="count", data=data)
plt.title(f"Office Count by {categorical_attribute}")
plt.xlabel("Country")
plt.xticks(range(4))
plt.ylabel("Count")
# plt.xticks(rotation=45)
plt.show()
```


    
![png](output_57_0.png)
    


----

## `orderdetails` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM orderdetails LIMIT 10;
```

     
*Output:*






<table>
    <thead>
        <tr>
            <th>orderNumber</th>
            <th>productCode</th>
            <th>quantityOrdered</th>
            <th>priceEach</th>
            <th>orderLineNumber</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>10100</td>
            <td>S18_1749</td>
            <td>30</td>
            <td>136</td>
            <td>3</td>
        </tr>
        <tr>
            <td>10100</td>
            <td>S18_2248</td>
            <td>50</td>
            <td>55.09</td>
            <td>2</td>
        </tr>
        <tr>
            <td>10100</td>
            <td>S18_4409</td>
            <td>22</td>
            <td>75.46</td>
            <td>4</td>
        </tr>
        <tr>
            <td>10100</td>
            <td>S24_3969</td>
            <td>49</td>
            <td>35.29</td>
            <td>1</td>
        </tr>
        <tr>
            <td>10101</td>
            <td>S18_2325</td>
            <td>25</td>
            <td>108.06</td>
            <td>4</td>
        </tr>
        <tr>
            <td>10101</td>
            <td>S18_2795</td>
            <td>26</td>
            <td>167.06</td>
            <td>1</td>
        </tr>
        <tr>
            <td>10101</td>
            <td>S24_1937</td>
            <td>45</td>
            <td>32.53</td>
            <td>3</td>
        </tr>
        <tr>
            <td>10101</td>
            <td>S24_2022</td>
            <td>46</td>
            <td>44.35</td>
            <td>2</td>
        </tr>
        <tr>
            <td>10102</td>
            <td>S18_1342</td>
            <td>39</td>
            <td>95.55</td>
            <td>2</td>
        </tr>
        <tr>
            <td>10102</td>
            <td>S18_1367</td>
            <td>41</td>
            <td>43.13</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(orderdetails);
```

     
*Output:*






<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>orderNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>productCode</td>
            <td>nvarchar(15)</td>
            <td>1</td>
            <td>None</td>
            <td>2</td>
        </tr>
        <tr>
            <td>2</td>
            <td>quantityOrdered</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>priceEach</td>
            <td>numeric(10,2)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>orderLineNumber</td>
            <td>smallint(6)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



---

## `orders` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM orders LIMIT 5;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>orderNumber</th>
            <th>orderDate</th>
            <th>requiredDate</th>
            <th>shippedDate</th>
            <th>status</th>
            <th>comments</th>
            <th>customerNumber</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>10100</td>
            <td>2003-01-06</td>
            <td>2003-01-13</td>
            <td>2003-01-10</td>
            <td>Shipped</td>
            <td>None</td>
            <td>363</td>
        </tr>
        <tr>
            <td>10101</td>
            <td>2003-01-09</td>
            <td>2003-01-18</td>
            <td>2003-01-11</td>
            <td>Shipped</td>
            <td>Check on availability.</td>
            <td>128</td>
        </tr>
        <tr>
            <td>10102</td>
            <td>2003-01-10</td>
            <td>2003-01-18</td>
            <td>2003-01-14</td>
            <td>Shipped</td>
            <td>None</td>
            <td>181</td>
        </tr>
        <tr>
            <td>10103</td>
            <td>2003-01-29</td>
            <td>2003-02-07</td>
            <td>2003-02-02</td>
            <td>Shipped</td>
            <td>None</td>
            <td>121</td>
        </tr>
        <tr>
            <td>10104</td>
            <td>2003-01-31</td>
            <td>2003-02-09</td>
            <td>2003-02-01</td>
            <td>Shipped</td>
            <td>None</td>
            <td>141</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(orders);
```
    
*Output:*






<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>orderNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>orderDate</td>
            <td>date</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>requiredDate</td>
            <td>date</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>shippedDate</td>
            <td>date</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>status</td>
            <td>nvarchar(15)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>comments</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>customerNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



**Check the states for _status_ category**


```sql
%%sql
SELECT 
    status,
    COUNT(status) "count"
FROM orders
GROUP BY status
ORDER BY status DESC;
```
    
*Output:*






<table>
    <thead>
        <tr>
            <th>status</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Shipped</td>
            <td>303</td>
        </tr>
        <tr>
            <td>Resolved</td>
            <td>4</td>
        </tr>
        <tr>
            <td>On Hold</td>
            <td>4</td>
        </tr>
        <tr>
            <td>In Process</td>
            <td>6</td>
        </tr>
        <tr>
            <td>Disputed</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Cancelled</td>
            <td>6</td>
        </tr>
    </tbody>
</table>



**Check the total order count per year**


```sql
%%sql
SELECT strftime('%Y', shippedDate) AS year, COUNT(*) AS order_count
FROM orders
WHERE status = 'Shipped'
GROUP BY year
ORDER BY year;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>year</th>
            <th>order_count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2003</td>
            <td>108</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>145</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>50</td>
        </tr>
    </tbody>
</table>




```python
# Retrieve the data from the database
query = f"""
SELECT strftime('%Y', shippedDate) AS year, COUNT(*) AS order_count
FROM orders
WHERE status = 'Shipped'
GROUP BY year
ORDER BY year;
"""
data = pd.read_sql_query(query, connection)

# Create the barplot using seaborn
plt.figure(figsize=(10, 6))
# sns.set(style="whitegrid")
sns.barplot(y="order_count", x="year", data=data)
plt.title(f"Orders by Year")
plt.xlabel("Year")
plt.ylabel("Orders")
plt.show()
```


    
![png](output_74_0.png)
    



```sql
%%sql
SELECT
    strftime('%Y', shippedDate) AS year,
    CASE
        WHEN strftime('%m', shippedDate) BETWEEN '01' AND '03' THEN 'Q1'
        WHEN strftime('%m', shippedDate) BETWEEN '04' AND '06' THEN 'Q2'
        WHEN strftime('%m', shippedDate) BETWEEN '07' AND '09' THEN 'Q3'
        ELSE 'Q4'
    END AS quarter,
    COUNT(*) AS order_count
FROM orders
WHERE status = 'Shipped'
GROUP BY year, quarter
ORDER BY year, quarter;
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>year</th>
            <th>quarter</th>
            <th>order_count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2003</td>
            <td>Q1</td>
            <td>14</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q2</td>
            <td>19</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q3</td>
            <td>19</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q4</td>
            <td>56</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q1</td>
            <td>25</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q2</td>
            <td>26</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q3</td>
            <td>35</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q4</td>
            <td>59</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Q1</td>
            <td>34</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Q2</td>
            <td>16</td>
        </tr>
    </tbody>
</table>




```python
# SQL query to extract the number of shipped orders by year
query = """
SELECT
    strftime('%Y', shippedDate) AS year,
    CASE
        WHEN strftime('%m', shippedDate) BETWEEN '01' AND '03' THEN 'Q1'
        WHEN strftime('%m', shippedDate) BETWEEN '04' AND '06' THEN 'Q2'
        WHEN strftime('%m', shippedDate) BETWEEN '07' AND '09' THEN 'Q3'
        ELSE 'Q4'
    END AS quarter,
    COUNT(*) AS order_count
FROM orders
WHERE status = 'Shipped'
GROUP BY year, quarter
ORDER BY year, quarter;
"""

# Execute the query and fetch the data
data = pd.read_sql_query(query, connection)


# Create a bar plot using Seaborn
plt.figure(figsize=(10, 6))
sns.barplot(data=data, x="year", y="order_count", hue="quarter")
plt.xlabel("Year")
plt.ylabel("Order Count")
plt.title("Order Count by Year and Quarter")
plt.xticks(rotation=45)  # Rotate x-axis labels for better readability

# Show the plot
plt.tight_layout()
plt.show()
```


    
![png](output_76_0.png)
    


---

## `payments` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM payments LIMIT 5;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>customerNumber</th>
            <th>checkNumber</th>
            <th>paymentDate</th>
            <th>amount</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>103</td>
            <td>HQ336336</td>
            <td>2004-10-19</td>
            <td>6066.78</td>
        </tr>
        <tr>
            <td>103</td>
            <td>JM555205</td>
            <td>2003-06-05</td>
            <td>14571.44</td>
        </tr>
        <tr>
            <td>103</td>
            <td>OM314933</td>
            <td>2004-12-18</td>
            <td>1676.14</td>
        </tr>
        <tr>
            <td>112</td>
            <td>BO864823</td>
            <td>2004-12-17</td>
            <td>14191.12</td>
        </tr>
        <tr>
            <td>112</td>
            <td>HQ55022</td>
            <td>2003-06-06</td>
            <td>32641.98</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(payments);
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>customerNumber</td>
            <td>INTEGER</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>checkNumber</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>2</td>
        </tr>
        <tr>
            <td>2</td>
            <td>paymentDate</td>
            <td>date</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>amount</td>
            <td>numeric(10,2)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



**Top 10 payments by customerNumer**


```sql
%%sql
SELECT 
    customerNumber,
    ROUND(SUM(amount),2) AS total_payment
 FROM payments
GROUP BY customerNumber
ORDER BY total_payment DESC
LIMIT 10;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>customerNumber</th>
            <th>total_payment</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>141</td>
            <td>715738.98</td>
        </tr>
        <tr>
            <td>124</td>
            <td>584188.24</td>
        </tr>
        <tr>
            <td>114</td>
            <td>180585.07</td>
        </tr>
        <tr>
            <td>151</td>
            <td>177913.95</td>
        </tr>
        <tr>
            <td>148</td>
            <td>156251.03</td>
        </tr>
        <tr>
            <td>323</td>
            <td>154622.08</td>
        </tr>
        <tr>
            <td>187</td>
            <td>148410.09</td>
        </tr>
        <tr>
            <td>276</td>
            <td>137034.22</td>
        </tr>
        <tr>
            <td>321</td>
            <td>132340.78</td>
        </tr>
        <tr>
            <td>146</td>
            <td>130305.35</td>
        </tr>
    </tbody>
</table>



**Payments by Year and Quarter**


```sql
%%sql
SELECT 
    strftime('%Y', paymentDate) AS year,
    CASE
        WHEN strftime('%m', paymentDate) BETWEEN '01' AND '03' THEN 'Q1'
        WHEN strftime('%m', paymentDate) BETWEEN '04' AND '06' THEN 'Q2'
        WHEN strftime('%m', paymentDate) BETWEEN '07' AND '09' THEN 'Q3'
        ELSE 'Q4'
    END AS quarter,
    ROUND(SUM(amount),2) AS total_payments_rec
 FROM payments
GROUP BY year, quarter
ORDER BY year, quarter;
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>year</th>
            <th>quarter</th>
            <th>total_payments_rec</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2003</td>
            <td>Q1</td>
            <td>370356.46</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q2</td>
            <td>476414.87</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q3</td>
            <td>565658.09</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Q4</td>
            <td>1837788.28</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q1</td>
            <td>745407.35</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q2</td>
            <td>567613.24</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q3</td>
            <td>1138731.31</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Q4</td>
            <td>1861576.35</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Q1</td>
            <td>775057.37</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Q2</td>
            <td>515235.91</td>
        </tr>
    </tbody>
</table>




```python
# SQL query to extract the sum payment amounts by year and quarter
query = """
SELECT 
    strftime('%Y', paymentDate) AS year,
    CASE
        WHEN strftime('%m', paymentDate) BETWEEN '01' AND '03' THEN 'Q1'
        WHEN strftime('%m', paymentDate) BETWEEN '04' AND '06' THEN 'Q2'
        WHEN strftime('%m', paymentDate) BETWEEN '07' AND '09' THEN 'Q3'
        ELSE 'Q4'
    END AS quarter,
    ROUND(SUM(amount),2) AS total_payments_rec
 FROM payments
GROUP BY year, quarter
ORDER BY year, quarter;
"""

# Execute the query and fetch the data
data = pd.read_sql_query(query, connection)


# Create a bar plot using Seaborn
plt.figure(figsize=(10, 6))
sns.barplot(data=data, x="year", y="total_payments_rec", hue="quarter")
plt.xlabel("Year")
plt.ylabel("Order Count")
plt.title("Payment Amounts Received by Year and Quarter")
plt.xticks(rotation=45)  # Rotate x-axis labels for better readability

# Show the plot
plt.tight_layout()
plt.show()
```


    
![png](output_87_0.png)
    


----

## `productlines` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM productlines LIMIT 5;
```
    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>productLine</th>
            <th>textDescription</th>
            <th>htmlDescription</th>
            <th>image</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Classic Cars</td>
            <td>Attention car enthusiasts: Make your wildest car ownership dreams come true. Whether you are looking for classic muscle cars, dream sports cars or movie-inspired miniatures, you will find great choices in this category. These replicas feature superb attention to detail and craftsmanship and offer features such as working steering system, opening forward compartment, opening rear trunk with removable spare wheel, 4-wheel independent spring suspension, and so on. The models range in size from 1:10 to 1:24 scale and include numerous limited edition and several out-of-production vehicles. All models include a certificate of authenticity from their manufacturers and come fully assembled and ready for display in the home or office.</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Motorcycles</td>
            <td>Our motorcycles are state of the art replicas of classic as well as contemporary motorcycle legends such as Harley Davidson, Ducati and Vespa. Models contain stunning details such as official logos, rotating wheels, working kickstand, front suspension, gear-shift lever, footbrake lever, and drive chain. Materials used include diecast and plastic. The models range in size from 1:10 to 1:50 scale and include numerous limited edition and several out-of-production vehicles. All models come fully assembled and ready for display in the home or office. Most include a certificate of authenticity.</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Planes</td>
            <td>Unique, diecast airplane and helicopter replicas suitable for collections, as well as home, office or classroom decorations. Models contain stunning details such as official logos and insignias, rotating jet engines and propellers, retractable wheels, and so on. Most come fully assembled and with a certificate of authenticity from their manufacturers.</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Ships</td>
            <td>The perfect holiday or anniversary gift for executives, clients, friends, and family. These handcrafted model ships are unique, stunning works of art that will be treasured for generations! They come fully assembled and ready for display in the home or office. We guarantee the highest quality, and best value.</td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>Trains</td>
            <td>Model trains are a rewarding hobby for enthusiasts of all ages. Whether you&#x27;re looking for collectible wooden trains, electric streetcars or locomotives, you&#x27;ll find a number of great choices for any budget within this category. The interactive aspect of trains makes toy trains perfect for young children. The wooden train sets are ideal for children under the age of 5.</td>
            <td>None</td>
            <td>None</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(productlines);
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>productLine</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>textDescription</td>
            <td>nvarchar(4000)</td>
            <td>0</td>
            <td>NULL</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>htmlDescription</td>
            <td>mediumtext</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>image</td>
            <td>mediumblob</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>




```sql
%%sql
SELECT DISTINCT productLine
  FROM productlines
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>productLine</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Classic Cars</td>
        </tr>
        <tr>
            <td>Motorcycles</td>
        </tr>
        <tr>
            <td>Planes</td>
        </tr>
        <tr>
            <td>Ships</td>
        </tr>
        <tr>
            <td>Trains</td>
        </tr>
        <tr>
            <td>Trucks and Buses</td>
        </tr>
        <tr>
            <td>Vintage Cars</td>
        </tr>
    </tbody>
</table>



---

## `products` table

**View the first 5 rows**


```sql
%%sql
SELECT *
  FROM products LIMIT 5;
```

    
*Output:*






<table>
    <thead>
        <tr>
            <th>productCode</th>
            <th>productName</th>
            <th>productLine</th>
            <th>productScale</th>
            <th>productVendor</th>
            <th>productDescription</th>
            <th>quantityInStock</th>
            <th>buyPrice</th>
            <th>MSRP</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>S10_1678</td>
            <td>1969 Harley Davidson Ultimate Chopper</td>
            <td>Motorcycles</td>
            <td>1:10</td>
            <td>Min Lin Diecast</td>
            <td>This replica features working kickstand, front suspension, gear-shift lever, footbrake lever, drive chain, wheels and steering. All parts are particularly delicate due to their precise scale and require special care and attention.</td>
            <td>7933</td>
            <td>48.81</td>
            <td>95.7</td>
        </tr>
        <tr>
            <td>S10_1949</td>
            <td>1952 Alpine Renault 1300</td>
            <td>Classic Cars</td>
            <td>1:10</td>
            <td>Classic Metal Creations</td>
            <td>Turnable front wheels; steering function; detailed interior; detailed engine; opening hood; opening trunk; opening doors; and detailed chassis.</td>
            <td>7305</td>
            <td>98.58</td>
            <td>214.3</td>
        </tr>
        <tr>
            <td>S10_2016</td>
            <td>1996 Moto Guzzi 1100i</td>
            <td>Motorcycles</td>
            <td>1:10</td>
            <td>Highway 66 Mini Classics</td>
            <td>Official Moto Guzzi logos and insignias, saddle bags located on side of motorcycle, detailed engine, working steering, working suspension, two leather seats, luggage rack, dual exhaust pipes, small saddle bag located on handle bars, two-tone paint with chrome accents, superior die-cast detail , rotating wheels , working kick stand, diecast metal with plastic parts and baked enamel finish.</td>
            <td>6625</td>
            <td>68.99</td>
            <td>118.94</td>
        </tr>
        <tr>
            <td>S10_4698</td>
            <td>2003 Harley-Davidson Eagle Drag Bike</td>
            <td>Motorcycles</td>
            <td>1:10</td>
            <td>Red Start Diecast</td>
            <td>Model features, official Harley Davidson logos and insignias, detachable rear wheelie bar, heavy diecast metal with resin parts, authentic multi-color tampo-printed graphics, separate engine drive belts, free-turning front fork, rotating tires and rear racing slick, certificate of authenticity, detailed engine, display stand\r\n, precision diecast replica, baked enamel finish, 1:10 scale model, removable fender, seat and tank cover piece for displaying the superior detail of the v-twin engine</td>
            <td>5582</td>
            <td>91.02</td>
            <td>193.66</td>
        </tr>
        <tr>
            <td>S10_4757</td>
            <td>1972 Alfa Romeo GTA</td>
            <td>Classic Cars</td>
            <td>1:10</td>
            <td>Motor City Art Classics</td>
            <td>Features include: Turnable front wheels; steering function; detailed interior; detailed engine; opening hood; opening trunk; opening doors; and detailed chassis.</td>
            <td>3252</td>
            <td>85.68</td>
            <td>136</td>
        </tr>
    </tbody>
</table>



**View the data types**


```sql
%%sql
PRAGMA table_info(products);
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>productCode</td>
            <td>nvarchar(15)</td>
            <td>1</td>
            <td>None</td>
            <td>1</td>
        </tr>
        <tr>
            <td>1</td>
            <td>productName</td>
            <td>nvarchar(70)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>productLine</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>productScale</td>
            <td>nvarchar(10)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>productVendor</td>
            <td>nvarchar(50)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>productDescription</td>
            <td>TEXT</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>quantityInStock</td>
            <td>smallint(6)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>7</td>
            <td>buyPrice</td>
            <td>numeric(10,2)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>8</td>
            <td>MSRP</td>
            <td>numeric(10,2)</td>
            <td>1</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>




```sql
%%sql
SELECT 
    productLine,
    COUNT(*) AS count
FROM products
GROUP BY productLine
ORDER BY count DESC
```

    
*Output:*


    




<table>
    <thead>
        <tr>
            <th>productLine</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Classic Cars</td>
            <td>38</td>
        </tr>
        <tr>
            <td>Vintage Cars</td>
            <td>24</td>
        </tr>
        <tr>
            <td>Motorcycles</td>
            <td>13</td>
        </tr>
        <tr>
            <td>Planes</td>
            <td>12</td>
        </tr>
        <tr>
            <td>Trucks and Buses</td>
            <td>11</td>
        </tr>
        <tr>
            <td>Ships</td>
            <td>9</td>
        </tr>
        <tr>
            <td>Trains</td>
            <td>3</td>
        </tr>
    </tbody>
</table>




```python
# Retrieve the data from the database
query = f"""
SELECT 
    productLine,
    COUNT(*) AS count
FROM products
GROUP BY productLine
ORDER BY count DESC
"""
data = pd.read_sql_query(query, connection)

# Create the barplot using seaborn
plt.figure(figsize=(10, 6))
# sns.set(style="whitegrid")
sns.barplot(y="productLine", x="count", data=data)
plt.title(f"Product distribution")
plt.xlabel("Count")
plt.ylabel("Product lines")
# plt.xticks(rotation=45)
plt.show()
```


    
![png](output_102_0.png)
    


---

<a id="Question_9"></a>
