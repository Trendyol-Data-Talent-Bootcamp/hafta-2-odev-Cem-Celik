# Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var.

```SQL
-- For data review
Select * from `dsmbootcamp.cem_celik.pageview` limit 1;

-- Solution begins

with one_min_users_table as(
    SELECT 
    timestamp_trunc(view_ts,MINUTE) as minu ,HLL_COUNT.init(deviceid) users
    from `dsmbootcamp.cem_celik.pageview`
    group by minu
    order by minu
),

five_min_table as (
    Select minu, ARRAY_AGG(users) OVER (ROWS BETWEEN 4 PRECEDING AND CURRENT ROW) five_minute_users
    from one_min_users_table)

SELECT
  minu,(
  SELECT
    HLL_COUNT.merge(users)
  FROM
    UNNEST(five_minute_users) users) five_min_count
FROM
  five_min_table
ORDER BY
  minu;
```
