# DK_Snowflake_Scaling_Compute_Demo
Welcome to the Snowflake Scaling Compute Demo. This demo showcases how you can easily scale the compute in Snowflake.
The sample data set used in this query for this demo are provided in the SNOWFLAKE_SAMPLE_DATA shared database.

## Setup
### Create warehouse
<img width="1432" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Scaling_Compute_Demo/assets/109616231/352646cc-a9a4-4a86-ac7b-c9ef48879943">

<img width="503" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Scaling_Compute_Demo/assets/109616231/75f38e36-ac82-4601-a8c2-19582113d2a8">

### Make sure the warehouse is in suspend mode to flush out the cache and make sure the warehouse is not active

<img width="1418" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Scaling_Compute_Demo/assets/109616231/3d98c99e-e3a3-4305-a3b8-610ed5a232a8">


### Create five SQL worksheet tabs in Snowsight



<img width="157" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/feecc512-90eb-4ba3-8ade-d17370930447">

### Each of the tabs corresponds to a session. So copy and paste the following code to each of the five tabs:

<img width="1693" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Scaling_Compute_Demo/assets/109616231/b56f7870-d2b7-4faa-bc63-1923ab8d5985">


```SQL

use database snowflake_sample_data;
use schema tpch_sf1000;
use warehouse voya_test;

ALTER SESSION SET USE_CACHED_RESULT = FALSE;


SELECT
    p.p_partkey,
    p.p_name,
    p.p_brand,
    p.p_type,
    p.p_size,
    SUM(CASE WHEN DATEDIFF(DAY,CURRENT_DATE, o.o_orderdate) <= 30 THEN l.l_quantity ELSE 0 END) AS last_30_days_qty,
    SUM(CASE WHEN DATEDIFF(DAY, CURRENT_DATE, o.o_orderdate) > 30 AND DATEDIFF(DAY, CURRENT_DATE, o.o_orderdate) <= 60 THEN l.l_quantity ELSE 0 END) AS last_31_60_days_qty,
   SUM(CASE WHEN DATEDIFF(DAY, CURRENT_DATE, o.o_orderdate) > 60 AND DATEDIFF(DAY, CURRENT_DATE, o.o_orderdate) <= 90 THEN l.l_quantity ELSE 0 END) AS last_61_90_days_qty,
   SUM(CASE WHEN DATEDIFF(DAY, CURRENT_DATE, o.o_orderdate) > 90 THEN l.l_quantity ELSE 0 END) AS over_90_days_qty
FROM
    part p
JOIN
    lineitem l ON p.p_partkey = l.l_partkey
JOIN
    orders o ON l.l_orderkey = o.o_orderkey
GROUP BY
    p.p_partkey, p.p_name, p.p_brand, p.p_type, p.p_size
ORDER BY
    p.p_partkey;

```

### Highlight the code and execute from the first tab
<img width="1690" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/8fcc8cbf-440d-4086-8d2c-5136db054392">


### Navigate back to the Admin/Warehouse Tab to see the first active cluster

<img width="1677" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/f2f88b25-7acf-435b-b595-e777c7216419">

### Keep executing one query at a time and for each iteration, check the number of active clusters
<img width="1701" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/5b26549f-ef3f-478d-acc5-e619a34dbce9">

<img width="1442" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/b85f1168-4cc7-44d9-bc4c-db691593fea9">

### Cancel all the queries
<img width="1689" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/77b863b3-c136-410f-b158-76eb6a8cff50">

### All the clusters for the warehouse will suspend due to being inactive
<img width="1447" alt="image" src="https://github.com/durandkwok-snowflake/DK_Snowflake_Compute_Scaling_Example/assets/109616231/b72be6a3-0d89-4375-84b3-6d47206b5d42">


