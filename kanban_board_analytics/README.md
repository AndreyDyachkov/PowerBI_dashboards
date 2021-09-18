Here is a small part of a dashboard aimed to monitor the efficiency of database development. All tasks are tracked using special software (similar to Trello). The process goes through several stages/columns on the Kanban board. The dashboard shows the average time that tasks spend at each stage. 

You can find pdf and pbix files.

Data are initially stored in multiple tables, so some tables are merged in Power Query to optimize the dashboard. 

To calculate the number of days for each task at each stage I added a calculated column:

```
Days =
VAR PreviousDate =
    CALCULATE (
        MAX ( 'tasks_log'[CREATED_DATE] ),
        ALLEXCEPT ( 'tasks_log', 'tasks_log'[TASK_ID] ),
        'tasks_log'[CREATED_DATE] < EARLIER ( 'tasks_log'[CREATED_DATE] )
    )
RETURN
    IF (
        ISBLANK ( PreviousDate ),
        BLANK (),
        'tasks_log'[CREATED_DATE] - PreviousDate
    )

```
We define a variable, which is equal to the maximum date for each task_id but is less than the row date. Then we return the difference between the row date and the variable or leave it blank if it’s an initial stage.
The schema:    

![Schema](https://github.com/AndreyDyachkov/PowerBI_dashboards/blob/main/kanban_board_analytics/schema.png)
