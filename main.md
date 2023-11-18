# ENM_HW5
## Introduction: Answering and Negotiating Bea's Question
Bea's question is "How does average daily temperature affect fashion sales?". Specifically, I chose to answer this question by using average daily temperature data from Spain (Bea's home country!) with online fashion sales data. This question stemmed from Bea's interest in fashion data, and using statistics to quantify fashion trends. Though we had to renegotiate this question several times as the project evolved, this question still interests Bea. Her original question was originall much more focused on how the fact of geography affects culture, but unfortunately I was unable to generate the data I needed for this (more on that later). We were able to renegotiate the quesion to its current form because Bea is interested in investigating the relationship between online fashion sales and weather, especially in Spain where she lives. 
In the realm of e-commerce, understanding the intricate relationship between external factors and sales performance is paramount for companies seeking to optimize their strategies. This data science project delves into the correlation between average temperature and clothing sales across diverse countries. Leveraging two distinct datasets—one containing weather data with country-specific average temperatures and the other encompassing online sales data—the investigation aims to unearth patterns and insights that could empower clothing companies, the audience of this project, to make informed decisions.

## Abstract
This data science project focuses on the intersection of weather patterns and online clothing sales, exploring the nuanced correlations across various countries. Through meticulous data cleaning, merging, and analysis, the project aims to uncover how average temperature influences overall sales and whether these effects differ at a country-specific level. The investigation also extends to examining correlations between temperature and sales concerning different device types. The project's findings provide valuable insights for e-commerce clothing companies, potentially informing tailored strategies to capitalize on weather-related consumer behaviors. Through visualizations and statistical analyses, this project contributes a nuanced understanding of the intricate interplay between climate conditions and online clothing sales.

## Data Collection
The datasets utilized in this project were sourced from Kaggle.com. The first dataset encapsulates comprehensive weather data, encompassing country, date, and average temperature. The second dataset involves online sales data, specifying the country of purchase, date, item type (we're focusing on'Clothing,' 'Beauty,' and 'Accessory'), and the device type used for the transaction.

https://www.kaggle.com/datasets/ronnykym/online-store-sales-data
https://www.kaggle.com/datasets/luisvivas/spain-portugal-weather

## Import Necessary Libraries and Load Datasets
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

weather_data = pd.read_csv('cleaned_weather_data.csv')
sales_data = pd.read_csv('raw_sales_data.csv')
```

## Data Cleaning and Preprocessing
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

## Filter and Merge Datasets
I integrated sales data with weather information to uncover potential correlations between weather conditions and retail performance. Leveraging Python and the Pandas library, we merged the sales data, represented by the 'MonthDay' column, with the daily average temperature data derived from the weather dataset. The merging process allowed us to associate each sales record with the corresponding temperature information based on the common date. To facilitate further analysis, we discretized the temperature data into intervals of 3 degrees Celsius, ranging up to a maximum value of 30 degrees. This discretization enabled us to explore how different temperature ranges might influence consumer purchasing behavior. The resulting 'Temperature Interval' column provided a structured basis for investigating patterns and trends in sales relative to varying temperature conditions. Overall, these preprocessing steps laid the foundation for subsequent analyses and visualizations, contributing to a comprehensive exploration of the interplay between weather and retail sales in our study.

```
# Merge sales data with weather data based on the common 'MonthDay' column
merged_df = pd.merge(sales_df, daily_avg, left_on='MonthDay', right_on='day_month', how='left')
merged_df = merged_df.drop(['day_month'], axis=1)

# Create temperature intervals of 3 degrees, with a maximum value of 30 degrees
merged_df['Temperature Interval'] = pd.cut(merged_df['tavg'], bins=range(0, 31, 3), right=False)
```

### How does average temperature affect general volume of sales?
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

### Which clothing items sell the best at different temperatures?
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

### What categories of clothing sell best at various temperatures?
In the final leg of our analysis, I delved deeper into understanding consumer preferences by classifying the most sold items into broader categories. Utilizing a function to map specific items to overarching clothing categories such as Tops, Pants, Shoes, Accessories, or Other, I aimed to distill key insights from our previously identified best-sellers. This categorization allowed us to discern patterns in consumer choices based on temperature intervals, offering retailers valuable insights for inventory management and strategic planning. As I visualized this information through a bar plot, each bar not only represented the number of items sold but also provided a clear label specifying the clothing category. This comprehensive view is instrumental for retailers seeking to align their product offerings with consumer preferences in various weather conditions, ultimately informing targeted marketing strategies and optimizing sales. I passed into ChatGPT a random sample of 200 of entries to create a simple classification function. While this is a redimentary way to classify the clothing types, many of the clothing types were repetitive and therefore those 200 random samples represented almost all if not all of the different clothing types. From this leg of the data anaylsis, clothing companies can better understand the correlation between the most popular categories of clothing types sold at different temperatures.

```
import pandas as pd

#Read the CSV data into a DataFrame
df = pd.read_csv('sales_with_average_temperature.csv')

#Define a function to map items to categories
def classify_item(item):
    tops = ['Tunic', 'Tank Top', 'Onesie', 'Jacket', 'T-shirt', 'Romper', 'Blouse', 'Sweater', 'Cardigan', 'Camisole', 'Hoodie', 'Kimono', 'Polo Shirt']
    pants = ['Leggings', 'Trousers', 'Jeans', 'Shorts', 'Pants', 'Overalls']
    shoes = ['Loafers', 'Flip-Flops', 'Boots', 'Sneakers', 'Sandals']
    accessories = ['Handbag', 'Wallet', 'Bowtie', 'Gloves', 'Scarf', 'Backpack', 'Belt', 'Hat', 'Tie', 'Umbrella', 'Socks', 'Sunglasses']

    if item in tops:
        return 'Top'
    elif item in pants:
        return 'Pants'
    elif item in shoes:
        return 'Shoes'
    elif item in accessories:
        return 'Accessories'
    else:
        return 'Other'

#Create a dictionary to store the most sold category for each temperature interval
most_sold_dict = {}

#Find and save the data for the most sold category for each temperature range
for interval, group in merged_df.groupby('Temperature Interval'):
    most_sold_item = group['Item Purchased'].mode().iloc[0] if not group.empty else ''
    most_sold_category = classify_item(most_sold_item)
    number_sold = group['Item Purchased'].value_counts().iloc[0] if not group.empty else 0
    most_sold_dict[interval] = (most_sold_category, number_sold)

#Print the dictionary
print(most_sold_dict)

temp_intervals = list(most_sold_dict.keys())
num_sales = [info[0] for info in most_sold_dict.values()]
item_types = [info[1] for info in most_sold_dict.values()]

#Extract data for plotting
temperature_intervals = list(most_sold_dict.keys())
num_items_sold = [value[1] for value in most_sold_dict.values()]
#Convert intervals to strings for plotting
temperature_intervals_str = [str(interval) for interval in temperature_intervals]

#Create a bar plot
plt.figure(figsize=(10, 6))
plt.bar(temperature_intervals_str, num_items_sold, color='lightcoral')

#Add labels and title
plt.xlabel('Temperature Intervals')
plt.ylabel('Number of Items Sold')
plt.title('Most Sold Clothing Category in Each Temperature Interval')

#Add labels to each bar
for interval, (category, num_sold) in most_sold_dict.items():
    plt.text(str(interval), num_sold + 0.1, f'{num_sold}\n{category}', ha='center', va='bottom')
    
#Add labels to each bar
#for i, num_sold in enumerate(num_items_sold):
#plt.text(temperature_intervals_str[i], num_sold + 0.1, f'{num_sold}', ha='center', va='bottom')

#for interval, (amount_sold, item_name) in most_sold_items_data.items():
#plt.text(str(interval), amount_sold + 0.1, f'{item_name}\n({amount_sold} sold)', ha='center', va='bottom')

#Show the plot
plt.show()
```
![Figure_3](https://github.com/lizqian/ENM_HW5/assets/133675095/23e85f22-74e8-46be-809f-2cf50b6e34f1)

## LLM Reflection and Limitations 
While our data analysis has provided valuable insights, it is essential to acknowledge its inherent limitations. Firstly, the dataset's size and scope may not encapsulate the entirety of consumer behaviors, as it might not represent a diverse range of regions, demographics, or shopping preferences. Additionally, the reliance on temperature intervals as a proxy for weather patterns oversimplifies the multifaceted nature of climate influences on consumer choices. Other weather factors such as humidity, precipitation, or seasonal nuances could play pivotal roles in shaping purchasing behaviors and are aspects worth exploring in future iterations of this analysis. Furthermore, cofounders have pointed out the potential influence of external factors like economic trends or cultural events, which were not incorporated into the current analysis. Recognizing these limitations is crucial for refining and expanding our analytical approach, ensuring a more comprehensive understanding of the complex interplay between retail sales and external factors.

Personally, I struggled A LOT on this project. I had to adapt my project multiple times when I hit an obstacle I coulnd't move past. One big thing I struggled with during this project was trying to generate my own dataset. I originally wanted to generate my own complex dataset relating fashion choices, demographic information, and geographical location. I originally had a veyr clear cut plan to write a program to query an LLM and write the output to the program. First, I tried using ChatGPT's AI, before switching to webscraping. However, by now both ChatGPT and BingAI (as well as other LLMs) have built up very strong defenses against web scraping. Though I kept trying to persist at this method because I was excited about the possibility of web scraping an LLM. This was very frustrating for me because I could see online that people had webscraped ChatGPT in the past, but I wasn't able to get past the current security. Furthermore, when I was working with ChatGPT I ran into a lot of code that threw errors. Even after working with ChatGPT to diagnose and resolve these errors, ChatGPT sometimes just led me in circles. I had to do a lot of debugging myself. This project, I gained a lot more intuition on prompt engineering. I learned the get ChatGPT to get closer to doing what I wanted when I broke larger steps down myself and asked ChatGPT to complete several smaller steps to reach a goal instead of asking it to accomplish this entire goal at once.

Embarking on this data-driven journey to analyze the intersection of retail sales and weather patterns has been both challenging and enlightening. From the initial stages of cleaning and merging datasets to the intricate process of defining temperature intervals and identifying consumer preferences, each step presented its unique set of hurdles. The iterative nature of our work, constantly refining our approach based on insights gained, reflects the dynamic nature of data science. One of the key takeaways from this project is the importance of interdisciplinary collaboration — merging retail sales data with weather data unearthed nuanced connections that may have been overlooked in isolation. The development of classification functions to categorize items provided a broader perspective, enabling us to distill meaningful trends. The visualizations, including bar plots with labeled categories, not only communicate our findings effectively but also offer actionable insights for businesses in the fashion retail sector. Overall, this project underscores the power of data in unraveling consumer behavior intricacies and the potential for such analyses to drive informed decision-making in the retail landscape.
