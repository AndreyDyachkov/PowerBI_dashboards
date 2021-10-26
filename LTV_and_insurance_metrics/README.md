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
'''
ExpenseRatio =
SELECTEDVALUE ( 'expense_ratio'[expense_ratio], "0.16" )
'''

