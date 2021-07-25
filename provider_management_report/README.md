### Provider management report

Here is an example of a PowerBI dashboard, which I created to analyze the workload of the healthcare provider management department at an insurance company. 

Basically, there are two main types of work:
- looking for new clinics and signing contracts with them,
- performing various tasks created by other departments. 

Technically, there are some interesting moments:
1. All measures are collected in the designated table. I believe it looks better. 
2. Data table has two relationships with table PMtasks because we need both "open date" and "close date" for our dashboard. So to use an inactive relationship we invoke USERELATIONSHIP function.
Such as: 
```
AvgWorkTimeByCloseDate =
CALCULATE (
    AVERAGE ( PMtasks[work_time] ),
    USERELATIONSHIP ( 'Date'[Date], PMtasks[close_date] )
)
```
3. There is "Last refresh" table, that allows us to display the date and time of the last refresh. The table is created in PowerQuery editor:
```
let
Source = #table(type table[LastRefresh=datetime], {{DateTime.LocalNow()}})
in
Source

```

Schema:

![Schema](https://github.com/AndreyDyachkov/PowerBI_dashboards/blob/main/provider_management_report/schema.png)

Originally, most data are collected from SQL Oracle database via ODBC.  
I provide the pbix file. However, all sensitive information and all personal data were removed.

