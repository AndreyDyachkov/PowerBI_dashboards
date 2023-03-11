#### Overall description
The dashboard demonstrates one of the easiest ways of calculating retention and performing cohort analysis. 
The data for this analysis is obtained from a long-term corporate health insurance contract. According to that contract, the employees co-finance their insurance policies, but they can choose to cover not only themselves but one of their relatives as well. The cohorts are defined by the first year of insurance coverage.

Retention in this case is the number of policies divided by the number of employees in the first period. As not all the employees chose health insurance in the first period, the percentage is lower than 100%. The same is applied to the relative group.

The matrices show retention over five consecutive years for all insured persons, and employees and relatives separately. Absolute values are shown just for demonstration purposes.

#### Data structure
The main table consists of the following columns: employee id, year of insurance coverage, and category of the policyholder (employee or relative). We also need an additional table with future years to use in columns of our matrix. So we generate a series of numbers:
````
future_years = GENERATESERIES(0,5,1)
````
We can also make them more informative, by adding a column:
````
Year = "Year "&'future_years'[Value]
````

#### Measures
First, we have to define the first year of insurance for each employee. To do this we create a new column, using the EARLIER function to get employee_id for the current row. Then the FILTER function returns a table with all rows for the current employee_id to find the earliest year of insurance.
````
join_year =
MINX (
    FILTER ( 'data', [employee_id] = EARLIER ( data[employee_id] ) ),
    'data'[ins_year]
)
````
Second, let’s make a matrix with rows equal to the first year of insurance (join year) and columns equal to the next years.
<br>
Finally, to calculate retention we divide the number of customers (in our case policyholders) in each year by the number of customers in the first year of insurance coverage.
<br>
We make two variables for the current column and row. After that, we use the FILTER function to return the table for the year of insurance equal to the first year plus the given number of years. Then we count employee IDs for that given number of years and divide the value by the number of employee IDs in the first year of insurance.
Specifically for that task, when we have two groups of policyholders, the business logic dictates that we have to count all employees in the first year, including those who bought insurance for their relatives only. Therefore, in the denominator, we use the ALL function to count all purchases. The easier version of the measure is shown below.
````
Retention rate % = 
VAR CurrentYearAfter = SELECTEDVALUE('future_years'[Value])
VAR JoinYear = SELECTEDVALUE('data'[join_year])
RETURN
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(
            'data'[employee_id]), 
        FILTER(
            'data','data'[ins_year] = JoinYear+CurrentYearAfter)
        ),
    CALCULATE(DISTINCTCOUNT('data'[employee_id]),ALL('data'[employee_relative]))    
    ,0)
````
Var2:
````
Retention rate % = 
VAR CurrentYearAfter = SELECTEDVALUE('future_years'[Value])
VAR JoinYear = SELECTEDVALUE('data'[join_year])
RETURN
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(
            'data'[employee_id]), 
        FILTER(
            'data','data'[ins_year] = JoinYear+CurrentYearAfter)
        ),
    DISTINCTCOUNT('data'[employee_id]),0)
````
