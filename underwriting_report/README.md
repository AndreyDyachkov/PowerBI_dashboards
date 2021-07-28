### Underwriting report

This dashboard is intended for monitoring the income flow of requests for proposals in the health and accident department in an insurance company. 
Basically, there are three lines of business: health insurance, accident insurance and travel insurance. Underwriters receive requests and make decisions about particular clients. So, the goal is to monitor the number of requests per week and make monthly reports as well.

From a technical point of view, the dashboard is quite simple:
- The data is collected from a SQL database and a few excel tables, that underwriters fill manually. These tables are appended in PowerQuery.
- The column “quote_duration” is made using variables in order to deal with typos made by underwriters:
```
quote_duration = 
VAR duration =
    DATEDIFF ( UWjournals[request_date], UWjournals[quote_date], DAY )
RETURN
    IF ( duration < 0, 0, duration )
```
Schema:
![Schema](https://github.com/AndreyDyachkov/PowerBI_dashboards/blob/main/provider_management_report/schema.png)
Measures are made in a special table. Also, there is a "last refresh" table, that allows us to display the date and time of the last refresh. 
The table is created in PowerQuery editor:
```
let
Source = #table(type table[LastRefresh=datetime], {{DateTime.LocalNow()}})
in
Source

```
