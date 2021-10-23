Here is a part of a real dashboard that shows a monthly performance of an insurance portfolio in terms of gross premium written (GPW), which is how much money an insurance company got selling insurance policies. There are 3 types of data: GPW budget (planned sales), actual GPW and GPW forecast. Forecast is calculated using linear regression models with seasonal adjustment. The PowerBI model gets only the result of prediction models from an excel file.    
Data are stored in separate tables, so some tables are merged in Power Query to optimize the dashboard. 

Measures:
GPW (Gross Premium Written) represents the premiums on all policies the company have issued over the period given.
Basic:
```
GPWactual = SUM ( 'GPWactual'[GPW] )
GPWbudget = SUM ( 'GPWbudget'[GPWplan] )
GPWforecast = SUM ( 'GPWforecast'[GPWfc] )
```
Year To Date (YTD):
```
GPWactualYTD = CALCULATE ( [GPWactual], DATESYTD ( 'Date'[Date] ) )
GPWbudgetYTD = CALCULATE ( [GPWbudget], DATESYTD ( 'Date'[Date] ) )
GPWforecastYTD = CALCULATE ( [GPWforecast], DATESYTD ( 'Date'[Date] ) )
```
Last year:
```
GPWLY = CALCULATE ( [GPWactual], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )
GPWLYYTD = CALCULATE ( [GPWactualYTD], SAMEPERIODLASTYEAR ( 'Date'[Date] ) )
```
Ratios:
```
GPWactualYTDvsGPWbudgetYTD_0-100% =
IF ( [GPWactualYTD] <= 0, 0, DIVIDE ( [GPWactualYTD], [GPWbudgetYTD], 1 ) )
GPWactualYTDvsGPWforecastYTD% = DIVIDE ( [GPWactualYTD], [GPWforecastYTD], 0 )

GPWactualYTDvsGPWLYYTD_0-100% =
VAR GPWLYYTD_ =
    IF ( [GPWLYYTD] <= 0, 0, [GPWLYYTD] )
RETURN
    IF ( [GPWactualYTD] <= 0, 0, DIVIDE ( [_GPWactualYTD], GPWLYYTD_, 1 ) )
```
(IF statements are to deal with negative GPW)
```
GPWforecastvsGPWbudget% = DIVIDE ( [GPWforecast], [GPWbudget], 0 )
GPWforecastvsGPWLY% = DIVIDE ( [GPWforecast], [GPWLY], 0 )
```
