# ENM_HW5
# Introduction: Answering Bea's Question
Bea's question is "How does average daily temperature affect online fahsin sales?". This question stemmed from Bea's interest in fashion data, and using statistics to quantify fashion trends. Though we had to renegotiate this question several times as the project evolved, this question still interests Bea because she is interested in investigating the relationship between online fashion sales and weather. 
In the realm of e-commerce, understanding the intricate relationship between external factors and sales performance is paramount for companies seeking to optimize their strategies. This data science project delves into the correlation between average temperature and clothing sales across diverse countries. Leveraging two distinct datasets—one containing weather data with country-specific average temperatures and the other encompassing online sales data—the investigation aims to unearth patterns and insights that could empower e-commerce clothing companies to make informed decisions.

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
