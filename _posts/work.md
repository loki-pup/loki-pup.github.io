paginated report:
1. parameter get user identity, retrive project list
2. project list used as a new parameter
3. get dataset use SQL stored procedure / functions / table / view, read and use parameter
4. multiple values parameter passed as a string like 'a,b,c,d', to use in SQL stored procedure, need to transform it into ''a'',''b'',''c'',''d''. 
since in stored procedure, it first concates the query string, then excutes the query. and '' can escape '.
SET @CustomersStr = REPLACE(REPLACE(@Customers,'''',''''''),',',''',''');
5. the new added field can also be manually added into the query result field

SQL
1. CONVERT(VARCHAR(15), b.jobNo) COLLATE SQL_Latin1_General_CP1_CI_AS AS [Project No],

power bi report
1. to handle default date, add another period dropdown list, and control via relationship
2. to adjust bar width, turn on overlap, increase space between series
3. header icons to remove filter 
4. pop out navigation
5. direct query can change column format in model view
6. direct query better return less data, can done via group by
7. bookmark with selected visuals can keep the filter and only modify the visuals

power apps
1. dynamic input in gallery