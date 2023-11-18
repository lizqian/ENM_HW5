# ENM_HW5
# Introduction: Answering Bea's Question
Bea's question is "How does average daily temperature affect fashion sales?". Specifically, I chose to answer this question by using average daily temperature data from Spain (Bea's home country!) with online fashion sales data. This question stemmed from Bea's interest in fashion data, and using statistics to quantify fashion trends. Though we had to renegotiate this question several times as the project evolved, this question still interests Bea because she is interested in investigating the relationship between online fashion sales and weather, especially in Spain where she lives. 
In the realm of e-commerce, understanding the intricate relationship between external factors and sales performance is paramount for companies seeking to optimize their strategies. This data science project delves into the correlation between average temperature and clothing sales across diverse countries. Leveraging two distinct datasets—one containing weather data with country-specific average temperatures and the other encompassing online sales data—the investigation aims to unearth patterns and insights that could empower clothing companies, the audience of this project, to make informed decisions.

# Abstract
This data science project focuses on the intersection of weather patterns and online clothing sales, exploring the nuanced correlations across various countries. Through meticulous data cleaning, merging, and analysis, the project aims to uncover how average temperature influences overall sales and whether these effects differ at a country-specific level. The investigation also extends to examining correlations between temperature and sales concerning different device types. The project's findings provide valuable insights for e-commerce clothing companies, potentially informing tailored strategies to capitalize on weather-related consumer behaviors. Through visualizations and statistical analyses, this project contributes a nuanced understanding of the intricate interplay between climate conditions and online clothing sales.

# Data Collection
The datasets utilized in this project were sourced from Kaggle.com. The first dataset encapsulates comprehensive weather data, encompassing country, date, and average temperature. The second dataset involves online sales data, specifying the country of purchase, date, item type (we're focusing on'Clothing,' 'Beauty,' and 'Accessory'), and the device type used for the transaction.

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
In the "Filter and Merge Datasets" phase of this project, I focused on combining and refining the datasets to facilitate a more targeted analysis. After cleaning and preprocessing the weather and sales datasets individually, I employed the Pandas library to merge these datasets based on common columns such as 'country' and 'date.' This integration created a unified dataset that incorporates both weather-related information and sales transactions. To narrow our focus to relevant sales data for e-commerce clothing companies, we filtered the merged dataset to include only items categorized as 'Clothing,' 'Beauty,' or 'Accessory.' This filtering was essential for honing in on the specific product types of interest. The resulting 'filtered_data' dataset now serves as a refined foundation for further exploration, enabling us to delve into correlations between average temperature and sales for these specific product categories. The code modifications implemented ensure the accurate filtering of the dataset, addressing any potential errors encountered during the process.
