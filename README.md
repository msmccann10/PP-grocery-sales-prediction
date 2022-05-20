# **Sales Prediction Model**

**Author:** Michael McCann
___

## Overview
I have been asked to review sales data at supermarkets and grocery stores to recommend strategies to increase sales. The primary indicator of success for my analysis is Item Outlet Sales which measure the sale of a product at a given outlet. To conduct my analysis I set out to answer the following questions:
1. What (if any) item characteristics affect sales?
2. What (if any) outlet characteristics affect sales?
3. Can we create a machine learning model to accurately predict future sales? 

## Business Problem
A fictional Customer is the head of a food producer/manufacturer. They are looking to bring a new item to market and want to optimize item characteristics and outlet partnerships to increase sales. Item Outlet Sales is the target variable to meet these 

## Data
### Data Sources
The sales data for this project comes from [analyticsvidhya](https://datahack.analyticsvidhya.com/contest/practice-problem-big-mart-sales-iii/) as part of a datahack challenge from May 2016. The original dataset has been stored in this repository and can be found [here](https://githubusercontent.com/msmccann10/Portfolio/tree/main/Sales-predictions/data). 

The dataset consists of 8523 individual observations collected from 10 different outlets of varying sizes, types, and locations. 1559 unique items were captured across 16 item categories.

### Data Dictionary
| Variable | Description |
| ------   | ----------- |
| Item Identifier | Unique Product ID |
| Item Weight | Weight of the Product |
| Item Fat Content | Whether the Product is Low Fat or Regular |
| Item Visibility | The Percentage of Total Display Area of All Products in a Store Allocated to the Product|
| Item Type | The Category to Which the Product Belongs|
| Item MRP | Maximum Retail Price (list price) of the Product|
| Outlet Identifier | Unique Store ID |
| Outlet Establishment Year | The Year in which the Outlet was Established|
| Outlet Size | The Size of the Outlet in Terms of Ground Area Covered|
| Outlet Location Type | The Type of Area in which the Outlet is Located|
| Outlet Type | Whether the Outlet is a Grocery Store or some sort of Supermarket|
| Item_Outlet_Sales | Sales of the Product in the Particular Outlet.|

### Data Validation
+ **Duplicates:** The provided dataset contained no duplicate rows
+ **Missing Values** There were a significant number of missing values in the Item Weight and Outlet Size columns. 
  - Item Weight: I replaced the missing values in Item Weight with the mean values of Items in the same Item Type category.
  - Outlet Size: The missing values in Outlet Size shared similar characteristics (mean, median, and distribution) as the small outlets. Therefore, I coded the missing values as small.
+ **Inconsistent Data:** Minor changes were made to the Item Fat Content and Outlet Size columns for consistency. 
  - Item Fat Content: Multiple entries to indicate the same value (IE: reg and Regular). These values were recoded for consistency. 
  - Outlet Size: Changed 'high' to 'large' for consistency with the rest of the variables. 

### Caveats and Considerations
The dataset documentation does not provide clear guidelines/definitions for the variables within Outlet Size, Outlet Location Type, and Outlet Type (IE: what constitutes a small vs large outlet, what differentiates a supermarket type 1 from a supermarket type 3). These variables are therefore taken at face value but will limit our ability to comment meaningfully on location, size, and type of outlet.

## Methods
See my [Jupyter Notebook](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/Sales_Predictions.ipynb) for detailed steps I took to clean and analyze the data. The general steps were as follows:
1. Imported raw dataframe and inspected it looking for duplicates, NAs, and inconsistent data.
2. Cleaned the data for exploratory data analysis.
3. Created visualizations to explore sales relationships with other variables.
4. Processed data for machine learning.
5. Ran and tuned linear regression, decision tree, bagged tree, and random forest models.

## Results

### Location, Location, Location
Looking at the various outlet variables (size, location, type) it is apparent that location. The below boxplot shows that medium-sized outlets outperform small and large-sized outlets, with medium outlets having a higher median and third quartile, as well as, larger outliers.

![boxplots](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/box%20size%20v%20sales.png)

Outlets located in tier 3 locations have more high-value outliers as well as a higher third quartile range. Outlets in tier 1 locations underperform in all metrics.

![boxplots](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/box%20location%20v%20sales.png)

Supermarket Type 3 outperforms its competitors, grocery stores underperform relative to all supermarket types. 

![boxplots](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/box%20type%20v%20sales.png)

### Item MRP 
Correlation testing conducted during exploratory data analysis revealed a moderate positive correlation (0.57) between max retail price (MRP) and item sales. This seems intuitive as items with a higher possible price point should sell for more per item, but does not account for sales volume which would still be necessary for a high total item outlet sales. The scatterplots below show the relationship between MRP and sales. Interestingly, the correlation varies between outlet size, location, and type, indicating that some outlets do better at selling high MRP items than others. 

![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Sales%20and%20MRP%20w%20Outlet%20Size.png)  |  ![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Sales%20and%20MRP%20w%20Outlet%20Location.png)  |  ![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Sales%20and%20MRP%20w%20Outlet%20Type.png)
:-------------------------:|:-------------------------:|:-------------------------:

Given the correlation between MRP and sales, it would seem prudent to pursue item categories that would maximize MRP. The below boxplots show the item categories used in this dataset ordered by median and mean. 

![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Item%20Type%20Ordered%20by%20Mean%20MRP.png)  |  ![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Item%20Type%20Ordered%20by%20Median%20MRP.png) 
:-------------------------:|:-------------------------:

Household goods, dairy products, starchy foods, snack foods, fruits and vegetables, seafood, and breakfast goods appear in the top half of both metrics. Health and Hygiene and Baking goods consistently appear near the bottom. There is variance within the category (low MRP household goods, high MRP baking goods, etc.) so additional in-category research should be conducted before selection.

### Visibility Paradox

Conventional wisdom suggests that increased item visibility would generate a corresponding increase in sales. However, the below scatterplot reveals there to be no correlation between these two variables. This means that paying for additional space may not be a good investment.

![](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/images/Sales%20and%20Visibility.png) 

The data provided does not allow us to evaluate premium space such as end caps or promotional locations. However, based on these findings we suggest further studies evaluate the relationship between sales and premium space to determine if it provides a positive return on investment.

## Machine Learning 
I used four machine learning models to determine which (if any) would help predict future sales. The results were as follows:

| Model             | RSME          | Train R2 | Test R2 |
| ----------------- | ------------- | -------------- | ------------- |
| Dummy / Baseline  | 1678.869      | 0.000          | -0.000        |
| Linear Regression | 1135.226      | 0.568          | 0.543         |
| Decision Tree     | 1145.560      | **0.938**      | 0.534         |
| Bagged Tree       | 1142.236      | **0.938**      | 0.535         |
| Random Forest     | **1097.597**  | 0.623          | **0.586**     |

The Random Forest model performed the best out of the four models providing the best root mean squared error (RMSE) and correlation coefficient (R2). After tuning I was able to get the Random Forest model to an RMSE of 1097.597 and an R2 of 0.586. While not terribly predictive this model significantly outperforms the baseline model and provides a base from which to start future analyses of products/sales.

### Feature Importance
![Capture](https://user-images.githubusercontent.com/97123912/157112157-8902fe4f-7511-48da-89d0-944314383240.PNG)

## Recommendations
+ **Partner with specific locations to optimize returns.** Medium and large-sized outlets, outlets in tier 2 and 3 locations, and supermarkets have consistently higher sales metrics than their competitors. Avoid grocery stores.
+ **Focus on high MRP Item Types.** Max retail price is the most consistent indicator of high sales. Focusing on the categories with the highest MRP items will allow you to consistently get higher sales figures. Additional research into specific items within high MRP Item Types will help select the best item(s) to pursue.
+ **Increased Visibility Does Not Drive Sales.** There is no discernable correlation between increased visibility and sales. We would advise against paying a premium for increased shelf space. Additional research should be conducted into the return on investment for premium space/placement at outlets. 

## For More Information
Please review my full analysis in my [Jupyter Notebook](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/Sales_Predictions.ipynb) or my [presentation](https://github.com/msmccann10/Portfolio/blob/main/Sales-predictions/presentation/Sales%20Prediction%20Model.pdf).

For any additional questions, please contact **Michael McCann**, [**msmccann10@gmail.com**](mailto:msmccann10@gmail.com).
