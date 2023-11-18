# ENM_HW5
# Introduction: Answering Bea's Question
Bea's question is "How does average daily temperature affect fashion sales?". Specifically, I chose to answer this question by using average daily temperature data from Spain (Bea's home country!) with online fashion sales data. This question stemmed from Bea's interest in fashion data, and using statistics to quantify fashion trends. Though we had to renegotiate this question several times as the project evolved, this question still interests Bea because she is interested in investigating the relationship between online fashion sales and weather, especially in Spain where she lives. 
In the realm of e-commerce, understanding the intricate relationship between external factors and sales performance is paramount for companies seeking to optimize their strategies. This data science project delves into the correlation between average temperature and clothing sales across diverse countries. Leveraging two distinct datasets—one containing weather data with country-specific average temperatures and the other encompassing online sales data—the investigation aims to unearth patterns and insights that could empower clothing companies, the audience of this project, to make informed decisions.

# Abstract
This data science project focuses on the intersection of weather patterns and online clothing sales, exploring the nuanced correlations across various countries. Through meticulous data cleaning, merging, and analysis, the project aims to uncover how average temperature influences overall sales and whether these effects differ at a country-specific level. The investigation also extends to examining correlations between temperature and sales concerning different device types. The project's findings provide valuable insights for e-commerce clothing companies, potentially informing tailored strategies to capitalize on weather-related consumer behaviors. Through visualizations and statistical analyses, this project contributes a nuanced understanding of the intricate interplay between climate conditions and online clothing sales.

# Data Collection
The datasets utilized in this project were sourced from Kaggle.com. The first dataset encapsulates comprehensive weather data, encompassing country, date, and average temperature. The second dataset involves online sales data, specifying the country of purchase, date, item type (we're focusing on'Clothing,' 'Beauty,' and 'Accessory'), and the device type used for the transaction.

https://www.kaggle.com/datasets/ronnykym/online-store-sales-data
https://www.kaggle.com/datasets/luisvivas/spain-portugal-weather

# Import Necessary Libraries and Load Datasets
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

weather_data = pd.read_csv('cleaned_weather_data.csv')
sales_data = pd.read_csv('raw_sales_data.csv')
```

# Data Cleaning and Preprocessing
First, I addressed date format discrepancies in the 'weather_data' CSV by explicitly specifying the format and using the errors='coerce' parameter to handle potential inconsistencies. Subsequently, I ensured uniformity in the 'date' column across datasets by converting it to the datetime format. Additionally, we standardized column names, renaming the 'average_temperature' column in 'weather_data' to 'temperature' for consistency in merging. To enhance data quality, missing values were identified, and rows with null entries were removed.

```
weather_df = weather_df.drop_duplicates()
weather_df = weather_df.fillna({'Review Rating': 0, 'moonrise': '00:00 AM', 'moonset': '00:00 AM'})
weather_df = weather_df.drop(['uvIndex', 'moon_illumination'], axis=1)
weather_df['date'] = pd.to_datetime(weather_df['date'])
weather_df['day_month'] = weather_df['date'].dt.strftime('%m-%d')
weather_df['tavg'] = (weather_df['maxtempC'] + weather_df['mintempC']) / 2
daily_avg = weather_df.groupby('day_month')['tavg'].mean().reset_index()
daily_avg.to_csv('cleaned_daily_temp.csv', index=False)

sales_df = sales_df.drop_duplicates()
sales_df = sales_df.fillna({'Review Rating': 0})
sales_df = sales_df.drop(['Customer Reference ID', 'Payment Method'], axis=1)
sales_df['Date Purchase'] = pd.to_datetime(sales_df['Date Purchase'])
sales_df['Date Purchase'] = pd.to_datetime(sales_df['Date Purchase'])
sales_df['MonthDay'] = sales_df['Date Purchase'].dt.strftime('%m-%d')
```

# Filter and Merge Datasets
I integrated sales data with weather information to uncover potential correlations between weather conditions and retail performance. Leveraging Python and the Pandas library, we merged the sales data, represented by the 'MonthDay' column, with the daily average temperature data derived from the weather dataset. The merging process allowed us to associate each sales record with the corresponding temperature information based on the common date. To facilitate further analysis, we discretized the temperature data into intervals of 3 degrees Celsius, ranging up to a maximum value of 30 degrees. This discretization enabled us to explore how different temperature ranges might influence consumer purchasing behavior. The resulting 'Temperature Interval' column provided a structured basis for investigating patterns and trends in sales relative to varying temperature conditions. Overall, these preprocessing steps laid the foundation for subsequent analyses and visualizations, contributing to a comprehensive exploration of the interplay between weather and retail sales in our study.

```
# Merge sales data with weather data based on the common 'MonthDay' column
merged_df = pd.merge(sales_df, daily_avg, left_on='MonthDay', right_on='day_month', how='left')
merged_df = merged_df.drop(['day_month'], axis=1)

# Create temperature intervals of 3 degrees, with a maximum value of 30 degrees
merged_df['Temperature Interval'] = pd.cut(merged_df['tavg'], bins=range(0, 31, 3), right=False)
```

# Filter and Merge Datasets
# How does average temperature affect general volume of sales?
Continuing our data exploration, I delved into the relationship between temperature intervals and the most sold clothing items. Beginning with the initialization of a dictionary to store our findings, I utilized Pandas to group the dataset by 'Temperature Interval,' creating distinct subsets for each temperature range. For each group, I identified the most frequently sold item and its corresponding sales count. This information was then stored in the 'most_sold_items_data' dictionary, associating each temperature interval with the most popular clothing item and the quantity sold. To enhance interpretability, I converted temperature intervals into string representations for both dictionary keys and x-axis labels. The subsequent creation of a bar plot provided a visual depiction of the most sold items across temperature ranges, with each bar labeled to showcase the specific item and its sales volume. This visual representation serves as a valuable tool for identifying patterns and insights into consumer preferences in response to varying temperature conditions, enriching our understanding of the intricate interplay between weather and retail dynamics. From this data, clothing companies can gain a better sense of the best selling clothing items at different temperatures. 

```
# Initialize a dictionary to store the data
most_sold_items_data = {}

# Find and save the data for the most sold item for each temperature range
for interval, group in merged_df.groupby('Temperature Interval'):
    most_sold_item = group['Item Purchased'].mode().iloc[0] if not group.empty else ''
    number_sold = group['Item Purchased'].value_counts().iloc[0] if not group.empty else 0
    most_sold_items_data[interval] = (number_sold, most_sold_item)

# Print the dictionary
print(most_sold_items_data)

# Convert temperature intervals to strings for dictionary keys and x-axis labels
interval_strings = [str(interval) for interval in most_sold_items_data.keys()]

# Create a barplot using the most_sold_items_data dictionary
plt.figure(figsize=(12, 8))
plt.bar(interval_strings, [item[0] for item in most_sold_items_data.values()], color='skyblue')

# Add labels to the bars
for interval, (amount_sold, item_name) in most_sold_items_data.items():
    plt.text(str(interval), amount_sold + 0.1, f'{item_name}\n({amount_sold} sold)', ha='center', va='bottom')

# Set plot labels and title
plt.xlabel('Temperature Interval')
plt.ylabel('Amount Sold')
plt.title('Amount of Most Sold Item for Each Temperature Interval (degrees C)')

# Show the plot
plt.show()
```
![Figure_1](https://github.com/lizqian/ENM_HW5/assets/133675095/fc94f32c-275f-45cf-9612-1886d7e487cd)

# Which clothing items sell the best at different temperatures?
Continuing the data analysis, I extended the investigation to understand the distribution of total sales across different temperature intervals. Leveraging Pandas, I computed the total number of sales within each temperature range and organized the results into a structured DataFrame. By merging this information with our previous findings on the most sold items, I created a comprehensive dataset encapsulating both the total sales count and the predominant item in each temperature interval. Calculating the percentage of total sales for each interval allowed me to quantify the contribution of specific items to overall sales within varying weather conditions. Visualizing these insights through a bar plot, with each bar labeled for clarity, provided a clear overview of the proportional impact of different clothing categories on total sales. From this part of the analysis, clothing retailers can better the realtionship between the proportional volume of sales and temperatures.

```
#Count total number of sales in each temperature interval
total_sales_by_temp = merged_df.groupby('Temperature Interval')['Item Purchased'].count().reset_index()
total_sales_by_temp = total_sales_by_temp.rename(columns={'Item Purchased': 'Total Sales'})

#Merge with most_sold_items_data to get the item names
total_sales_by_temp = total_sales_by_temp.merge(
    pd.DataFrame(list(most_sold_items_data.values()), index=most_sold_items_data.keys(), columns=['Total Sold', 'Most Sold Item']),
    left_on='Temperature Interval', right_index=True, how='left'
)

#Calculate the percentage of total sales for each temperature interval
total_sales_by_temp['Percentage of Total Sales'] = (total_sales_by_temp['Total Sold'] / total_sales_by_temp['Total Sales']) * 100

#Print the results
print(total_sales_by_temp)

#Plot percentage of total sales for each temperature interval
plt.figure(figsize=(12, 8))
plt.bar(interval_strings, total_sales_by_temp['Percentage of Total Sales'], color='lightcoral')

#Add labels to the bars
for interval, percentage in zip(total_sales_by_temp['Temperature Interval'], total_sales_by_temp['Percentage of Total Sales']):
    plt.text(str(interval), percentage + 0.1, f'{percentage:.2f}%', ha='center', va='bottom')

#Set plot labels and title
plt.xlabel('Temperature Interval')
plt.ylabel('Percentage of Total Sales')
plt.title('Percentage of Total Sales in Each Temperature Interval (degrees C)')

#Show the plot 
plt.show()
```
![Figure_2](https://github.com/lizqian/ENM_HW5/assets/133675095/e3628617-d961-45f1-98f9-c0ae44556558)

