### LTV and insurance metrics

#### Overall description

The dashboard shows LTV and basic financial metrics for each client of an insurance company. A user can choose a client on the second page of the dashboard and get the client’s metrics on the next two pages. 

#### PowerQuery

All indicators are based on 2 data sources: the amount of insurance premium (GPW) and the losses (Claims and RBNS). Contract_id attribute allows us to connect these data. The insurance company has several lines of business (LoB), such as health insurance, motor insurance etc., and plenty of products in each LoB. The product mapping table helps to assign products to LoB. Data are stored in excel files.

