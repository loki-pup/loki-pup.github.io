---
title: Power BI / SSRS paginated report
teaser: paginated report
category: Power BI
tags: [paginated report]
---

How to build paginated reports based on specific column / value:

* Use subreport

* Use row group

1. Insert list, then insert charts / matrix / table into chart

2. Bind the list dataset to required dataset

3. Add row group to the list, and set the group on to required column / value

4. Set page break to between each instance of a group

5. Set document map to required column /  value

To retrive datasets from SQL, add parameters into parameter section, and write the query accordingly.
