### LTV and insurance metrics

#### Overall description

The dashboard shows LTV and basic financial metrics for each client of an insurance company. A user can choose a client on the second page of the dashboard and get the client’s metrics on the next two pages. 

#### PowerQuery

All indicators are based on 2 data sources: the amount of insurance premium (GPW) and the losses (Claims and RBNS). Contract_id attribute allows us to connect these data. The insurance company has several lines of business (LoB), such as health insurance, motor insurance etc., and plenty of products in each LoB. The product mapping table helps to assign products to LoB. Data are stored in excel files.

![Schema](https://github.com/AndreyDyachkov/PowerBI_dashboards/blob/main/LTV_and_insurance_metrics/dependencies.png)

#### Metrics
For each client the dashboard shows major insurance metrics:
- GPW (Gross Premium Written) - the total amount that customers are required to pay for insurance coverage on policies issued by a company during a specific period.
- GPE (Gross Premium Earned) - premiums that an insurance company books as earnings for providing insurance during a specific period.
- Losses
- RBNS - Reported But Not Settled losses
- LR (Loss ratio = Loss / GPE) - The total amount of claims paid out to policyholders as a percentage of the total premium earned over the same period. 
- CommR - Commission Rate
- LR + CommR
- CR (Combine Ratio) = LR + DAC + Expense ratio
- LTV (LifeTime Value = GPE – DAC – Loss - RBNS)
- DAC - Deferred Acquisition Costs

#### Expense ratio
The Expense ratio (the ratio of firm-wide expenses to the total amount of premiums earned over the same period) is required to be corrected manually. So, we generate a list of values (expense_ratio table) and use SELECTEDVALUE function to choose the right one.
```
ExpenseRatio =
SELECTEDVALUE ( 'expense_ratio'[expense_ratio], "0.16" )
```

#### Measures

##### GPE and DAC measures

These measures are the most interesting ones. 

```
GPE =
SUMX (
    FILTER (
        SUMMARIZE ( 'GPW', 'GPW'[start_date], 'GPW'[end_date], "GPE", [GPW] ),
        'GPW'[start_date] <= LASTDATE ( 'Date_for_Loss'[Date] )
            && 'GPW'[end_date] >= FIRSTDATE ( 'Date_for_Loss'[Date] )
    ),
    [GPE]
        * COUNTROWS (
            DATESBETWEEN (
                'Date_for_Loss'[Date],
                IF (
                    FIRSTDATE ( 'Date_for_Loss'[Date] ) > 'GPW'[start_date],
                    FIRSTDATE ( 'Date_for_Loss'[Date] ),
                    'GPW'[start_date]
                ),
                IF (
                    LASTDATE ( 'Date_for_Loss'[Date] ) < 'GPW'[end_date],
                    LASTDATE ( 'Date_for_Loss'[Date] ),
                    'GPW'[end_date]
                )
            )
        )
        / COUNTROWS (
            DATESBETWEEN ( 'Date_for_Loss'[Date], 'GPW'[start_date], 'GPW'[end_date] )
        )
)
```
Formula:

GPE = GPW*(the number of days an insurance contract is in effect in the reporting period)/(the total number of days the contract is valid)

How to calculate:
1.	We need to create a Date table that is not connected to the fact table(!)
2.	SUMMARIZE - creates a table, where data are grouped by Start Date, End Date with SUM aggregation function (measure: GPW). Each client usually has more than one entry in this table, so we combine those rows.
2.	FILTER excludes contracts that are not in the date range.
3.	First COUNTROWS – counts rows in a table of dates when the contract is valid in the period given.
4.	Second COUNTROWS – count rows in a table of dates when the contract is valid.

```
DAC =
SUMX (
    FILTER (
        SUMMARIZE ( 'GPW', 'GPW'[start_date], 'GPW'[end_date], "DAC", [Commission] ),
        'GPW'[start_date] <= LASTDATE ( 'Date_for_Loss'[Date] )
            && 'GPW'[end_date] >= FIRSTDATE ( 'Date_for_Loss'[Date] )
    ),
    [DAC]
        * COUNTROWS (
            DATESBETWEEN (
                'Date_for_Loss'[Date],
                IF (
                    FIRSTDATE ( 'Date_for_Loss'[Date] ) > 'GPW'[start_date],
                    FIRSTDATE ( 'Date_for_Loss'[Date] ),
                    'GPW'[start_date]
                ),
                IF (
                    LASTDATE ( 'Date_for_Loss'[Date] ) < 'GPW'[end_date],
                    LASTDATE ( 'Date_for_Loss'[Date] ),
                    'GPW'[end_date]
                )
            )
        )
        / COUNTROWS (
            DATESBETWEEN ( 'Date_for_Loss'[Date], 'GPW'[start_date], 'GPW'[end_date] )
        )
)
```

DAC calculation is similar to GPE

Formula

DAC = Commission * (the number of days an insurance contract is in effect in the reporting period)/(the total number of days the contract is valid)


##### Basic measures:
```
GPW =
SUM ( 'GPW'[GPW_final] )
```
```
Loss =
SUM ( 'claims'[loss] )
```
```
Commission =
SUM ( 'GPW'[commission] )
```
```
RBNS =
SUM ( 'RBNS'[RBNS] )
```
```
Refund =
SUM ( 'GPW'[refund] )
```
```
Years =
DATEDIFF ( MIN ( 'GPW'[start_date] ), MAX ( 'GPW'[end_date] ), DAY ) / 365
```

##### Ratios:
```
LR =
DIVIDE ( ( [Loss] + [RBNS] ), [GPE] )
```
```
CommR =
DIVIDE ( [DAC], [GPE], 0 )
```
##### Complex measure:
```
CR =
[LR+CommR] + 'expense_ratio'[ExpenseRatio]
```
```
LR+CommR =
[LR] + [CommR]
```
```
LTV_per_period =
[GPE] - [DAC] - [Loss] - [RBNS]
```

Schema:
-	3 fact tables: GPW, Claims, and RBNS
-	2 date tables: (1) for GPE and DAC calculations, and losses; (2) for GPW  
![Schema](https://github.com/AndreyDyachkov/PowerBI_dashboards/blob/main/LTV_and_insurance_metrics/schema.png)

All sensitive information and all personal data were removed.

