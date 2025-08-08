paginated report:
1. parameter get user identity, retrive project list
2. project list used as a new parameter
3. get dataset use SQL stored procedure / functions / table / view, read and use parameter
4. multiple values parameter passed as a string like 'a,b,c,d', to use in SQL stored procedure, need to transform it into ''a'',''b'',''c'',''d''. 
since in stored procedure, it first concates the query string, then excutes the query. and '' can escape '.
SET @CustomersStr = REPLACE(REPLACE(@Customers,'''',''''''),',',''',''');
5. the new added field can also be manually added into the query result field
6. to display close / open projects, based on the include closed project filter, and prefilter the return dataset. Get the closed project list first, then filter the user permissions project list like =CInt(Fields!projectCode.Value) in @Projects. @Projects is a parameter allow multiple values, and the default takes from closed project list project codes

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
8. to show and hide visuals via bookmark, must select both show and hidden visuals when update
9. table doesn't show duplicate records, so to include duplicate records, better have an aggregate column, like sum(sales)
10. power bi link can choose go to specific page
11. to switch order of hierarchy, or unit like hour / day, can use field parameters like switchActual = {
    ("Time Worked2", NAMEOF('EmpTimesheet'[aday]), 0,"day","Days by Clients","Total Days"),
    ("Time Worked2", NAMEOF('EmpTimesheet'[ahour]), 1,"hour","Hours by Clients","Total Hours")
}. Add exrtra columns to indicate the slicer / title. If want to sum the columns, create measures first, then field parameters.
12. if switch order of hierarchy only works properly in desktop, like switchVisual = {
    ("Project", NAMEOF('EmpTimesheet'[Project Code and Name]), 0,"by project"),
    ("Employee", NAMEOF('EmpTimesheet'[Primary Name]), 1,"by project"),
    ("Employee", NAMEOF('EmpTimesheet'[Primary Name]), 2,"by employee"),
    ("Project", NAMEOF('EmpTimesheet'[Project Code and Name]), 3,"by employee")
}, when switching in web page, it can't jump to the top. Then split it in two like: switchVisual = {
    ("Project", NAMEOF('EmpTimesheet'[Project Code and Name]), 0,"by project"),
    ("Employee", NAMEOF('EmpTimesheet'[Primary Name]), 0,"by employee")
}, switchVisual2 = {
    ("Employee", NAMEOF('EmpTimesheet'[Primary Name]), 0,"by project"),
    ("Project", NAMEOF('EmpTimesheet'[Project Code and Name]), 0,"by employee")
}, and build 1-1 relationship

power apps
1. dynamic input in gallery
2. power bi query requires the user is a workplace viewer and have the build permission of the dataset
3. In solution, put dev / prod as environment variable, then store relevant values in SharePoint list, run power automate flow to query the list and get values in power apps.

aws
1. glue job has around 8 seconds start up time, if the script runs less than 10 mins, can consider using lambda
2. in glue, pyspark can be weird. For items column has nested json in nested json, it's struct / map in dynamoDB. I f use pyspark F.explode directly, it can lead to item_key mismatch. Like when processing a row, it tries to read b row's item_keys.
3. when using UDF to process row by row, it also has weird phenomenon if the data type is struct / map. When processing a row, it either somehow loses some item_keys of a, or somehow adds the item_keys of b into a
4. in the end, it transforms the items column to json string foramt, then loads the json when processing row, in this way, it doesn't have weird keys problem. 
or when reading the data, define the column data type to be string, so it saves the transform time.
5. same as power bi, when processing large volume of data, retrieve the needed columns at first, so it reduces the data size.
6. in python Variables that are created outside of a function (as in all of the examples in the previous pages) are known as global variables.
Global variables can be used by everyone, both inside of functions and outside.
If you create a variable with the same name inside a function, this variable will be local, and can only be used inside the function. The global variable with the same name will remain as it was, global and with the original value.
python doesn't really have constant data type, but you can use all capital letters variable to represent it.

