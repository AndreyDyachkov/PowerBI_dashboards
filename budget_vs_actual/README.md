Here is a part of a real dashboard that shows a monthly performance of an insurance portfolio in terms of gross premium written (GPW), which is how much money an insurance company got selling insurance policies. There are 3 types of data: GPW budget (planned sales), actual GPW and GPW forecast. Forecast is calculated using linear regression models with seasonal adjustment. The PowerBI model gets only the result of prediction models from an excel file.    
Data are stored in separate tables, so some tables are merged in Power Query to optimize the dashboard. 

Measures:

GPW (Gross Premium Written) represents the premiums on all policies the company have issued over the period given.


